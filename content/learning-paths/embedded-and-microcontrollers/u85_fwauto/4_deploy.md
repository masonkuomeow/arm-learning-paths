---
title: Deploy SLM
weight: 4
layout: "learningpathall"
---

## Understand the project structure

Before building and deploying, it is helpful to understand the key directories and files within the `alif_slm_r` repository. This knowledge is essential for a manual workflow, where you need to locate specific build scripts and binaries yourself.

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

## Understand the manual workflow

In a manual firmware deployment workflow, you are responsible for identifying the correct project settings, build commands, and deployment procedures. This means inspecting the repository structure, reading any available documentation, and executing a series of commands by hand.

The `.fwauto/config.toml` configuration file starts out empty when you first clone the repository. In a manual workflow, you would need to populate this file yourself or pass all the required details as command-line arguments. You also need to determine the correct firmware directory, CMake target, binary output path, COM port, and board target on your own.

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

The MRAM usage line shows that the firmware (model weights plus code) occupies about 55% of the available 2 MB MRAM.

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

### Verify the firmware is running (manual)

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

## Common issues with manual configuration

Manual firmware workflows are prone to errors because of the many details that must be correctly identified and consistently applied.

| Issue | Why it happens | How fwauto helps |
|------|----------------|-----------------|
| Empty configuration file | The project starts without generated deployment values | `fwauto` identifies the missing values from the repository |
| Wrong repository path | Commands were copied from a different project | `fwauto` uses the current cloned repository |
| Missing build target | The user has to know which firmware directory and CMake target to use | `fwauto` inspects the project before generating the workflow |
| Inconsistent manual edits | Values are changed in one place but not another | `fwauto` generates a consistent set of changes |

## Use fwauto to inspect the project

Instead of manually inspecting the repository, you can ask `fwauto` to do it for you. `fwauto` reads the project structure, identifies the build system, locates the firmware directories, and determines the required configuration values -- all from the repository itself.

This is useful because the `.fwauto/config.toml` file starts out empty. Rather than filling it in by hand, you can let `fwauto` discover what values are needed and propose them.

## Prompt fwauto to generate the workflow

To leverage `fwauto`'s inspection capabilities, provide it with a prompt that describes the task:

```
Inspect this repository and generate the firmware update workflow for the Alif SLM project. The configuration file is currently empty. Determine which configuration values need to be updated, explain why each value is needed, and generate the commands required to prepare and deploy the firmware. Use the current repository structure as the source of truth.
```

This prompt is effective because:

- It tells `fwauto` that the `.fwauto/config.toml` file is initially empty, so it generates a full configuration rather than assuming one already exists.
- It asks `fwauto` to inspect the current repository, preventing assumptions about external or predefined project structures.
- It tells `fwauto` to use the local repository as the definitive source for project details.
- It asks `fwauto` to explain why each configuration value is needed, which helps you understand the project before executing anything.
- It directs `fwauto` to generate executable commands for building and deploying the firmware.

{{% notice Note %}}
The configuration file is intentionally empty at the start of this workflow. This makes it easier to show how `fwauto` discovers the required updates instead of relying on pre-filled settings.
{{% /notice %}}

## Review the generated configuration updates

After `fwauto` inspects the project and processes your prompt, it generates proposed configuration updates for the `.fwauto/config.toml` file, along with the corresponding build and deploy commands.

Review these generated outputs before executing them. Confirm that:

- The firmware directory matches `alif_vscode-template/`
- The build target is `stories260k_runner.debug+E8-HE`
- The deploy script path and arguments are correct
- The COM port matches your hardware setup

This review step ensures that `fwauto` has correctly interpreted the project structure and that the generated workflow is safe to execute.

## Deploy the firmware with fwauto

Once you have reviewed and confirmed the `fwauto` configuration, you can use slash commands to build and flash. From the project root, start FWAuto in chat mode:

```bash
fwauto
```

Then run these slash commands:

```bash
/build
/deploy
```

`/build` runs the same CMake command shown in the manual section. `/deploy` runs the same `deploy_setools.py` script. Both commands are configured in `.fwauto/config.toml`.

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

After flashing, the board reboots automatically and runs the new firmware.

## Verify the deployment

After deploying the firmware, verify that it is running by opening a serial terminal to the board. Use the same verification command from the manual section:

```bash
powershell -Command "$p = New-Object System.IO.Ports.SerialPort('COM3',115200,'None',8,'One'); $p.ReadTimeout=5000; $p.Open(); Start-Sleep -Seconds 3; $d=$p.ReadExisting(); $p.Close(); Write-Host $d"
```

If the firmware is running correctly, you should see the `READY>` prompt, confirming that the model has loaded and is waiting for input.

## Compare manual and fwauto workflows

The following table summarizes the differences between a manual firmware deployment workflow and one automated with `fwauto`.

| Step | Manual workflow | fwauto workflow |
|------|----------------|-----------------|
| Inspect project files | The user checks files manually | `fwauto` scans the project |
| Update configuration | The user edits values by hand | `fwauto` suggests required updates |
| Build firmware | User runs cmake commands manually | `/build` runs the configured build |
| Flash firmware | User runs deploy script with correct arguments | `/deploy` runs the configured deploy |
| Risk | Missing or inconsistent settings | Lower risk because changes are generated from project context |

## Troubleshooting

Use the following table to resolve common issues you might encounter during the build and deployment process.

| Issue | Why it happens | How to fix it |
|------|----------------|---------------|
| Build fails with "target not found" | Wrong build directory or target name | Verify you are in `alif_vscode-template/` and the target name is `stories260k_runner.debug+E8-HE` |
| Deploy fails with "COM port not found" | Board not connected or wrong port | Check Device Manager for the correct COM port number |
| Board shows garbled output | Wrong firmware binary flashed | Ensure you built from `alif_vscode-template/`, not `workshop-ethos-u/` |
| fwauto build fails | `.fwauto/config.toml` is missing or misconfigured | Run `fwauto build` from the project root to re-run the setup wizard |

{{% notice Note %}}
If the build fails, FWAuto reads the error output and suggests a fix. You can also type `/log` to analyze UART output if the board does not respond correctly after flashing.
{{% /notice %}}

With the firmware deployed and verified, you are ready to start the web server and interact with the model from your browser.
