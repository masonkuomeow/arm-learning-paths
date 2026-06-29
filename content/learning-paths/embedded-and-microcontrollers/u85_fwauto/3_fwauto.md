---
title: Understanding FWAuto
weight: 3

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Understanding FWAuto

In the next sections you install [FWAuto](https://fwauto.ai/) and use it to build and flash firmware with the `/build` and `/deploy` commands. This page explains what FWAuto does so you understand what happens behind those commands.

### What FWAuto does

FWAuto is an AI-assisted firmware development tool. It provides a chat interface that can run shell commands, read files, and manage the build-flash-test workflow. You interact with it through natural language or slash commands.

In this Learning Path you use FWAuto for two tasks:

| Command | What it does |
|---------|--------------|
| `/build` | Runs the [CMake](https://cmake.org/) build to compile firmware for the [Cortex-M55](https://developer.arm.com/Processors/Cortex-M55) HE core |
| `/deploy` | Runs the deploy script to flash firmware to the Alif E8 board via SETOOLS |

You can also use `/log` to analyze UART output and `/help` to see all available commands.

### How FWAuto understands your project

FWAuto reads your project configuration from `.fwauto/config.toml`. This file tells FWAuto which build system to use, which build target to compile, and how to deploy the firmware. When you run `/build` or `/deploy`, FWAuto reads the config and executes the correct commands.

FWAuto also understands common build systems such as Makefile, CMake, and Ninja. If a build fails, it reads the error output, locates the source of the problem, and suggests a fix. For flash failures it can detect the board, switch ports, and retry.

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
