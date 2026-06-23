---
title: Understanding FWAuto
weight: 3

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Understanding FWAuto

In the next sections you install FWAuto and use it to build and flash firmware with the `/build` and `/deploy` commands. This page explains what FWAuto does so you understand what happens behind those commands.

### What FWAuto does

FWAuto is an AI-assisted firmware development tool. It provides a chat interface that can run shell commands, read files, and manage the build-flash-test workflow. You interact with it through natural language or slash commands.

In this Learning Path you use FWAuto for two tasks:

| Command | What it does |
|---------|--------------|
| `/build` | Runs the CMake build to compile firmware for the Cortex-M55 HE core |
| `/deploy` | Runs the deploy script to flash firmware to the Alif E8 board via SETOOLS |

You can also use `/log` to analyze UART output and `/help` to see all available commands.

### What makes FWAuto different

Unlike a general code assistant that only sees individual files, FWAuto understands the whole firmware project and automates the full loop:

| Capability | What it means |
|------------|---------------|
| Context awareness | Reads the SoC datasheet, SDK API, BSP, Device Tree, RTOS, and driver dependencies, so generated code fits the platform |
| Demo code understanding | Follows the driver flow, init order, and API relationships to extend an existing demo instead of guessing |
| Closed SDK support | Import an SDK, headers, demo, and datasheet to build a project knowledge base (for example Novatek, TI, NXP, MediaTek, Realtek) |
| Build-aware | Understands Makefile, CMake, Kconfig, and Ninja; on failure it locates the file and suggests a fix |
| Flash-aware | Detects the board, switches port, retries, and verifies the flash result |
| Log-aware | Reads UART, kernel, stack trace, assert, panic, HardFault, and WDT logs and explains the cause |
| Auto verification | Runs compile, runtime, log, register, and performance checks after generating code |

Because FWAuto carries project context, verifies its own output, and runs the loop end to end, it can complete several steps in one pass — reducing the back-and-forth needed to finish a task.

### From requirement to firmware

A traditional workflow is manual at every step:

```
Requirement -> Datasheet -> SDK -> Code -> Build -> Flash -> Debug -> Fix
```

FWAuto automates the loop and closes it with root-cause analysis:

```
Requirement -> FWAuto -> Code -> Build -> Flash -> Debug -> Root cause -> Fix
```

For a failure, it connects evidence the way an experienced FAE does, rather than giving generic advice:

```
Log -> Symbol -> SDK API -> Register -> Datasheet -> Root cause
```

### How FWAuto understands your project

FWAuto reads your project configuration from `.fwauto/config.toml`. This file tells FWAuto which build system to use, which build target to compile, and how to deploy the firmware. When you run `/build` or `/deploy`, FWAuto reads the config and executes the correct commands.

As a project's knowledge base grows (datasheets, demos, drivers, recorded root causes), later work starts from a stronger baseline — which also lowers the team's bus factor and speeds up onboarding.

### Chat mode and slash commands

FWAuto supports two ways to trigger actions:

| Method | Example |
|--------|---------|
| Slash commands | `/build`, `/deploy`, `/log` |
| Natural language | "Build the firmware", "Flash the board" |

Both methods produce the same result. Slash commands are shorter; natural language is useful when you want to describe a task in your own words.

{{% notice Note %}}
The `/build` and `/deploy` commands you use later in this Learning Path invoke CMake and the SETOOLS deploy script respectively. Both are configured in `.fwauto/config.toml` in the project repository.
{{% /notice %}}

## Next steps

Click **Next** to install the tools and set up your hardware.
