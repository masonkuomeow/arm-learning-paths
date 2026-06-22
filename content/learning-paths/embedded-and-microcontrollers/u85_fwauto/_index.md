---
title: Deploy a Small Language Model on Alif E8 with an Interactive Web GUI

minutes_to_complete: 45

who_is_this_for: This Learning Path is for embedded software developers who want to deploy a small language model on an Alif E8 DevKit and interact with it through a browser-based dashboard.

description: Deploy the stories260K language model on the Alif E8 DevKit (Arm Cortex-M55 HE core), build firmware with FWAuto, flash it via SETOOLS, and interact with it through a real-time web GUI.

learning_objectives:
    - Set up the Alif E8 DevKit and FWAuto development environment on Windows
    - Build and flash SLM firmware using FWAuto, CMake, and SETOOLS
    - Run a Flask web server that connects to the board over UART
    - Send text prompts from a browser and view model-generated responses in real time

prerequisites:
    - An [Alif E8 Development Kit](https://alifsemi.com/support/kits/ensemble-e8devkit/) with USB cable
    - A Windows PC (Windows 10 or 11)
    - Internet connection for downloading tools
    - A [FWAuto](https://fwauto.ai/) account (free registration)

author:
    - Mason Kuo
    - Odin Shen

generate_summary_faq: true
rerun_summary: false
rerun_faqs: false

### Tags
skilllevels: Introductory
subjects: ML
armips:
    - Cortex-M
tools_software_languages:
    - Python
    - C
    - Flask
    - CMake
    - GCC

operatingsystems:
    - Windows

further_reading:
    - resource:
        title: Alif E8 DevKit Documentation
        link: https://alifsemi.com/support/kits/ensemble-e8devkit/
        type: website
    - resource:
        title: stories260K - Karpathy's tiny LLM
        link: https://github.com/karpathy/llama2.c
        type: github
    - resource:
        title: FWAuto Documentation
        link: https://fwauto.ai/
        type: website

### FIXED, DO NOT MODIFY
# ================================================================================
weight: 1
layout: "learningpathall"
learning_path_main_page: "yes"
---
