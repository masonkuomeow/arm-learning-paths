---
title: Install tools and set up hardware
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Install tools and set up hardware

This section walks you through installing every tool and preparing the [Alif E8 DevKit](https://alifsemi.com/support/kits/ensemble-e8devkit/).

### Hardware setup

1. Connect the Alif E8 DevKit to your Windows PC using the USB cable.

2. Use the port labeled **JLINK**. This provides both J-Link debug and a virtual COM port.

3. Open **Device Manager** and verify you see:
   - **J-Link CDC UART** under Ports (COM & LPT) -- note the COM port number (usually COM3)
   - **SEGGER J-Link** under USB devices

4. Locate the **EN/DIS switch** on the board. Set it to **EN**.

   This switch stays in the EN position at all times. The flashing process enters SE maintenance mode automatically through software -- you never need to change this switch.

### Install Python

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

You should see `Python 3.10.x` or later.

### Install Node.js

FWAuto requires [Node.js](https://nodejs.org/) 20 or later.

1. Go to [nodejs.org/en/download](https://nodejs.org/en/download)
2. Download the **Windows Installer (.msi)** for version 20 or later
3. Run the installer with default settings. Make sure **"Add to PATH"** is selected.
4. Close and reopen your terminal after installation.

Verify:

```bash
node --version
```

You should see `v20.x.x` or later.

### Install uv

[uv](https://docs.astral.sh/uv/) is a Python package manager that FWAuto uses. Install it in PowerShell (run as Administrator):

```bash
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Close and reopen PowerShell after installation. Verify:

```bash
uv --version
```

### Install FWAuto

Install FWAuto using the official install script. In PowerShell:

```bash
powershell -ExecutionPolicy ByPass -c "irm https://fwauto.ai/install.ps1 | iex"
```

The script installs the `fwauto` CLI and the AI CLI tools. Verify:

```bash
fwauto --help
```

You should see the FWAuto banner and a list of available commands.

### Authenticate with FWAuto

FWAuto uses Google OAuth for authentication. Run:

```bash
fwauto auth login
```

A browser window opens. Select your Google account and authorize access. After login, verify:

```bash
fwauto auth status
```

You should see `Status: Logged in` with your email address.

### Install SEGGER J-Link software

The [SEGGER J-Link](https://www.segger.com/downloads/jlink/) software provides the debug probe driver and tools for communicating with the Alif E8 board.

1. Go to [segger.com/downloads/jlink](https://www.segger.com/downloads/jlink/)
2. Download the **J-Link Software and Documentation Pack** for Windows
3. Run the installer with default settings

Verify:

```bash
JLinkExe --version
```

### Install build tools

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

### Clone the project repository

Open a Command Prompt and clone the project:

```bash
git clone https://github.com/odincodeshen/alif_slm_r.git
cd alif_slm_r
```

Install the Python dependencies for the web server:

```bash
pip install flask pyserial
```

### Initialize FWAuto in the project

Navigate to the project root and run any FWAuto command to start the setup wizard:

```bash
fwauto build
```

The wizard asks you to configure:
1. **SDK Configuration** -- press Enter to accept the default path
2. **Build Configuration** -- select `command` and enter the CMake build command
3. **Deploy Configuration** -- select `command` and enter the deploy command

After the wizard completes, a `.fwauto/` directory is created:

```console
.fwauto/
├── config.toml     # Project configuration
├── build/          # Build scripts
└── logs/           # Log directory
```

{{% notice Note %}}
If the wizard does not appear, check that you are inside the `alif_slm_r` directory. FWAuto searches upward for a `.fwauto/` directory.
{{% /notice %}}

### Verify your setup

Before proceeding, verify that:

- [ ] Alif E8 board is connected via USB (JLINK port)
- [ ] COM port appears in Device Manager (for example, COM3)
- [ ] EN/DIS switch is set to EN
- [ ] Python 3.10+ is installed and on PATH
- [ ] Node.js 20+ is installed and on PATH
- [ ] uv is installed
- [ ] FWAuto is installed and authenticated
- [ ] J-Link software is installed
- [ ] CMake, Ninja, and Arm GCC are installed
- [ ] Project repository is cloned

## Next steps

Click **Next** to build and flash the firmware.
