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

On Linux (Debian or Ubuntu):

```bash
sudo apt update
sudo apt install python3 python3-pip
```

On macOS, use Homebrew:

```bash
brew install python
```

On Windows, download the installer from [python.org/downloads](https://www.python.org/downloads/). Make sure to check **"Add Python to PATH"** during installation.

Verify:

```bash
python --version
```

You should see output similar to:

```output
Python 3.12.4
```

## Install Node.js

FWAuto requires [Node.js](https://nodejs.org/) 20 or later.

On Linux (Debian or Ubuntu):

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```

On macOS, use Homebrew:

```bash
brew install node@20
```

On Windows, use winget:

```bash
winget install OpenJS.NodeJS.LTS
```

Verify:

```bash
node --version
```

You should see output similar to:

```output
v20.15.0
```

## Install uv

[uv](https://docs.astral.sh/uv/) is a Python package manager that FWAuto uses.

On Linux and macOS:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

On Windows (PowerShell):

```bash
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Close and reopen your terminal after installation. Verify:

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
2. Download the **J-Link Software and Documentation Pack** for your operating system
3. Run the installer with default settings

Verify:

```bash
JLinkExe --version
```

## Install build tools

The firmware build requires [CMake](https://cmake.org/), [Ninja](https://ninja-build.org/), and the [Arm GNU Toolchain](https://developer.arm.com/downloads/-/gnu-rm).

Install CMake:

On Linux (Debian or Ubuntu):

```bash
sudo apt install cmake
```

On macOS, use Homebrew:

```bash
brew install cmake
```

On Windows, download from [cmake.org/download](https://cmake.org/download/) and select "Add CMake to the system PATH" during installation.

Install Ninja:

On Linux (Debian or Ubuntu):

```bash
sudo apt install ninja-build
```

On macOS, use Homebrew:

```bash
brew install ninja
```

On Windows, use winget:

```bash
winget install Ninja-build.Ninja
```

Install the Arm GNU Toolchain:

Go to [developer.arm.com/downloads](https://developer.arm.com/downloads/-/gnu-rm) and download the installer for your operating system. On Windows, make sure "Add to PATH" is selected during installation. On Linux and macOS, extract the archive and add the `bin` directory to your `PATH`.

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

You should see output similar to:

```output
Python 3.12.4
v20.15.0
uv 0.4.0
and so on
```

With all dependencies installed, you are ready to set up FWAuto and prepare the hardware.
