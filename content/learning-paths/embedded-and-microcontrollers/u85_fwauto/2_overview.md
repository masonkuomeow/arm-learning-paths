---
title: Overview
weight: 2

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Overview

In this Learning Path you deploy a small language model (SLM) on the Alif E8 Development Kit and interact with it through a web browser.

The Alif E8 DevKit contains an Arm Cortex-M55 microcontroller with enough memory to run a tiny 260,000-parameter language model. You install all required tools, build and flash the firmware, then start a web server to send prompts from your browser.

### What is stories260K?

stories260K is a tiny language model created by Andrej Karpathy as part of the [llama2.c](https://github.com/karpathy/llama2.c) project. It has 260,000 parameters, 5 transformer layers, and a 512-token vocabulary. It is trained on simple children's stories, so it generates short, coherent text continuations.

The model is small enough to run entirely on the Cortex-M55 HE core of the Alif E8. The KV cache sits in SRAM and the model weights sit in MRAM.

### What you build

By the end of this Learning Path you have:

- A firmware binary that runs the SLM inference engine on the E8 board
- A Flask web server that communicates with the board over UART
- A browser dashboard where you type prompts and see model responses in real time

### Architecture

```
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

The browser connects to the Flask server via Server-Sent Events (SSE) for real-time streaming. The server sends prompts to the board over UART and streams model output back to the browser.

## Next steps

Click **Next** to learn about FWAuto, the tool you use to build and flash the firmware.
