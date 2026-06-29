---
title: Overview and install dependencies
weight: 2
layout: "learningpathall"
---

## Overview

In this Learning Path, you deploy a small language model (SLM) on the [Alif E8 Development Kit](https://alifsemi.com/support/kits/ensemble-e8devkit/) and interact with it through a web browser.

The Alif E8 DevKit contains an [Arm Cortex-M55](https://developer.arm.com/Processors/Cortex-M55) microcontroller with enough memory to run a tiny 260,000-parameter language model. You install all required tools, build and flash the firmware, then start a web server to send prompts from your browser.

### What is stories260K?

[stories260K](https://github.com/karpathy/llama2.c) is a tiny language model created by Andrej Karpathy as part of the llama2.c project. It has 260,000 parameters, 5 transformer layers, and a 512-token vocabulary. It is trained on simple children's stories, so it generates short, coherent text continuations.

The model is small enough to run entirely on the Cortex-M55 HE core of the Alif E8. The KV cache sits in SRAM and the model weights sit in MRAM.

### Architecture

The system connects a browser dashboard to the Alif E8 board through a [Flask](https://flask.palletsprojects.com/) web server. The web GUI provides a user-friendly interface where you type prompts and view model-generated responses in real time, without needing a serial terminal.

```console
  Browser (localhost:5000)
      |
      | HTTP / SSE
      v
  Flask web server (web_demo_server.py)
      |
      | UART serial (COM3, 115200 baud)
      v
  Alif E8 Board (Cortex-M55 HE)
      |
      | stories260K inference engine
      v
  Model weights (MRAM) + KV cache (SRAM)
```

The browser connects to the Flask server via [Server-Sent Events (SSE)](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events) for real-time streaming. The server sends prompts to the board over UART and streams model output back to the browser.

## Install Python

[FWAuto](https://fwauto.ai/) requires [Python](https://www.python.org/) 3.10 or later.

1. Go to [python.org/downloads](https://www.python.org/downloads/)
2. Download the latest Python 3.x installer for Windows
3. Run the installer
4. **Check "Add Python to PATH"** during installation
5. Click "Install Now"

Verify in a new Command Prompt:

```bash
python --version
```

You should see output similar to:

```output
Python 3.12.4
```

## Install Node.js

FWAuto requires [Node.js](https://nodejs.org/) 20 or later.

1. Go to [nodejs.org/en/download](https://nodejs.org/en/download)
2. Download the **Windows Installer (.msi)** for version 20 or later
3. Run the installer with default settings. Make sure **"Add to PATH"** is selected.
4. Close and reopen your terminal after installation.

Verify:

```bash
node --version
```

You should see output similar to:

```output
v20.15.0
```

## Install uv

[uv](https://docs.astral.sh/uv/) is a Python package manager that FWAuto uses. Install it in PowerShell (run as Administrator):

```bash
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Close and reopen PowerShell after installation. Verify:

```bash
uv --version
```

You should see output similar to:

```output
uv 0.4.0
```

## Install SEGGER J-Link software

The [SEGGER J-Link](https://www.segger.com/downloads/jlink/) software provides the debug probe driver and tools for communicating with the Alif E8 board.

1. Go to [segger.com/downloads/jlink](https://www.segger.com/downloads/jlink/)
2. Download the **J-Link Software and Documentation Pack** for Windows
3. Run the installer with default settings

Verify:

```bash
JLinkExe --version
```

## Install build tools

The firmware build requires [CMake](https://cmake.org/), [Ninja](https://ninja-build.org/), and the [Arm GNU Toolchain](https://developer.arm.com/downloads/-/gnu-rm).

1. Install CMake from [cmake.org/download](https://cmake.org/download/). Select "Add CMake to the system PATH" during installation.

2. Install Ninja:

```bash
winget install Ninja-build.Ninja
```

3. Install the Arm GNU toolchain:
   - Go to [developer.arm.com/downloads](https://developer.arm.com/downloads/-/gnu-rm)
   - Download the **Arm GNU Toolchain** installer for Windows
   - Run the installer and make sure "Add to PATH" is selected

Verify all three tools:

```bash
cmake --version
ninja --version
arm-none-eabi-gcc --version
```

## Verify your setup

Before proceeding, verify that all tools are installed:

```bash
python --version
node --version
uv --version
cmake --version
ninja --version
arm-none-eabi-gcc --version
JLinkExe --version
```

With all dependencies installed, you are ready to set up FWAuto and prepare the hardware.

