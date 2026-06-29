---
title: Build and flash the firmware
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Build and flash the firmware

This section covers building the SLM firmware from source and flashing it to the [Alif E8](https://alifsemi.com/support/kits/ensemble-e8devkit/) board. You do this manually first, then use [FWAuto](https://fwauto.ai/)'s `/build` and `/deploy` commands.

### Understand the project structure

The project contains two firmware implementations:

| Directory | Description |
|---|---|
| `workshop-ethos-u/` | Batch-mode firmware (runs preset prompts, then stops) |
| `alif_vscode-template/` | Interactive firmware with [UART](https://developer.mozilla.org/en-US/docs/Glossary/UART) input, [BPE](https://huggingface.co/learn/nlp-course/chapter6/5) tokenizer, and READY/DONE markers |

You use the **interactive firmware** in `alif_vscode-template/`.

Key source files:

| File | Description |
|---|---|
| `alif_vscode-template/stories260k_runner/main.c` | Main application: interactive loop, stdin reader, prompt handler |
| `alif_vscode-template/stories260k_runner/llm_infer.c` | LLM inference engine: transformer forward pass, BPE tokenizer |
| `alif_vscode-template/stories260k_runner/llm_infer.h` | Data structures and API declarations |
| `alif_vscode-template/stories260k_runner/model_data.h` | Embedded model weights (260K parameters, ~1.0 MB) |
| `alif_vscode-template/stories260k_runner/tokenizer_data.h` | Embedded BPE tokenizer vocabulary (512 tokens) |

### Build the firmware (manual)

Open a Command Prompt, navigate to the project root, and build:

```bash
cd alif_slm_r\alif_vscode-template
cmake --build tmp --target stories260k_runner.debug+E8-HE
```

This compiles the firmware for the [Cortex-M55](https://developer.arm.com/Processors/Cortex-M55) HE core. The build produces:

- `alif_vscode-template/out/stories260k_runner/E8-HE/debug/stories260k_runner.elf` (ELF with debug symbols)
- `alif_vscode-template/out/stories260k_runner/E8-HE/debug/stories260k_runner.bin` (raw binary for flashing)

You should see output similar to:

```output
[0/6] Performing build step for 'stories260k_runner.debug+E8-HE'
Building CMake target 'stories260k_runner.debug+E8-HE'
Using compiler: GCC V14.2.1
[1/40] Building C object .../llm_infer.c.obj
[2/40] Building C object .../main.c.obj
...
[40/40] Linking C executable stories260k_runner.elf
MRAM: 1149296 B / 2 MB (54.80%)
[5/5] Completed 'stories260k_runner.debug+E8-HE'
```

### Flash the firmware (manual)

Navigate back to the project root and run the deploy script:

```bash
cd ..
python deploy_setools.py "alif_vscode-template/out/stories260k_runner/E8-HE/debug/stories260k_runner.bin" --com COM3
```

Replace `COM3` with your actual COM port number if different. The board's EN/DIS switch stays in the **EN** position -- the script enters SE maintenance mode automatically over UART.

The script:

1. Copies the binary to the SETOOLS build directory
2. Generates an ATOC (Application Table of Contents) package
3. Enters soft maintenance mode
4. Writes the firmware to [MRAM](https://en.wikipedia.org/wiki/Magnetoresistive_random-access_memory)

You should see:

```output
============================================================
  Alif SETOOLS Deploy
============================================================
[PREP] Source: .../stories260k_runner.bin (1149296 bytes)
[PREP] ATOC OK.
[MAINT] Soft Maintenance Mode ACTIVE.
[FLASH] Completed (rc=0).
============================================================
  DEPLOY SUCCESSFUL
============================================================
```

After flashing, the board reboots automatically and runs the new firmware.

### Verify the firmware is running

Open a serial terminal to check the board output:

```bash
powershell -Command "$p = New-Object System.IO.Ports.SerialPort('COM3',115200,'None',8,'One'); $p.ReadTimeout=5000; $p.Open(); Start-Sleep -Seconds 3; $d=$p.ReadExisting(); $p.Close(); Write-Host $d"
```

You should see output similar to:

```output
============================================
 stories260K LLM - Alif E8 Board
 Object Classification Demo
============================================
Model: 260K params, dim=64, 5 layers
Tokenizer: 512 tokens (BPE)

[MAIN] Initializing model...
[LLM] Config: dim=64 hidden=172 layers=5 heads=8 kv=4 vocab=512 seq=512
[MAIN] Model initialized OK
[MAIN] Initializing tokenizer...
[TOK] Loaded 512 tokens
[MAIN] Tokenizer initialized OK

============================================
 Interactive Classification Mode
============================================

READY>
Enter prompt:
```

If you see `READY>` and `Enter prompt:`, the firmware is running correctly.

### Build and flash with FWAuto

Instead of running the build and flash commands manually, you can use FWAuto. From the project root, start FWAuto in chat mode:

```bash
fwauto
```

Then run these slash commands:

```bash
/build
/deploy
```

`/build` runs the same CMake command shown above. `/deploy` runs the same `deploy_setools.py` script. Both commands are configured in `.fwauto/config.toml`.

The build output is identical to the manual CMake build:

```output
Building project...
Running: cmd /c "cd .../alif_vscode-template && cmake --build tmp --target stories260k_runner.debug+E8-HE"
[0/6] Performing build step for 'stories260k_runner.debug+E8-HE'
[1/40] Building C object .../llm_infer.c.obj
...
[40/40] Linking C executable stories260k_runner.elf
MRAM: 1149296 B / 2 MB (54.80%)
Build succeeded
```

The deploy output is identical to the manual `deploy_setools.py` run:

```output
Deploying firmware...
[PREP] Source: .../stories260k_runner.bin (1149296 bytes)
[PREP] ATOC OK.
[MAINT] Soft Maintenance Mode ACTIVE.
[FLASH] Completed (rc=0).
  DEPLOY SUCCESSFUL
```

{{% notice Note %}}
If the build fails, FWAuto reads the error output and suggests a fix. You can also type `/log` to analyze UART output if the board does not respond correctly after flashing.
{{% /notice %}}

## Next steps

Click **Next** to start the web server and interact with the model through your browser.
