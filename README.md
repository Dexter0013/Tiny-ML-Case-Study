# TinyML DDoS & Network Threat Detection Framework for Microcontrollers

![Build Status](https://img.shields.io/badge/Build-Passing-brightgreen)
![Platform](https://img.shields.io/badge/Platform-ESP32%20%7C%20Arduino-blue)
![License](https://img.shields.io/badge/License-MIT-green)
![TinyML](https://img.shields.io/badge/TinyML-eBPF%20%2F%20TFLite%20Micro%20%2F%20emlearn-orange)

An ultra-low-latency, memory-efficient TinyML framework designed to detect and mitigate Distributed Denial of Service (DDoS) and network traffic anomalies directly on embedded microcontrollers (ESP32, ESP32-S3, Arduino Nano 33 BLE). By offloading model execution to microsecond-level inline C logic or quantized INT8 kernels, this framework eliminates control plane saturation and shields low-power gateways from volumetric attacks.

---
<img width="1524" height="685" alt="image" src="https://github.com/user-attachments/assets/c2e42dd2-603d-4ced-89cc-ead9ba0e8467" />
Link to project: https://wokwi.com/projects/477016569256088577
---

## Table of Contents
- [Hardware Architecture & Specs](#hardware-architecture--specs)
- [SOTA TinyML Model Matrix](#sota-tinyml-model-matrix)
- [Deep-Dive Model Architectures](#deep-dive-model-architectures)
- [Repository Structure](#repository-structure)
- [Getting Started](#getting-started)
  - [1. Prerequisites](#1-prerequisites)
  - [2. Model Training & Export (Python)](#2-model-training--export-python)
  - [3. Microcontroller Firmware Deployment (ESP32/Arduino)](#3-microcontroller-firmware-deployment-esp32arduino)
- [Performance & Latency Benchmarks](#performance--latency-benchmarks)
- [License](#license)

---

## Hardware Architecture & Specs

| Hardware Platform | CPU & Speed | SRAM | Flash | Recommended Model Engine |
| :--- | :--- | :--- | :--- | :--- |
| **ESP32-S3** | 240 MHz (Dual-Core Xtensa LX7) | 512 KB | 4–16 MB | Quantized INT8 Autoencoder / 1D-CNN |
| **ESP32 (Standard)** | 240 MHz (Dual-Core Xtensa LX6) | 320 KB | 4–8 MB | `emlearn` Decision Trees / TFLite Micro |
| **Arduino Nano 33 BLE** | 64 MHz (ARM Cortex-M4) | 256 KB | 1 MB | Micro-Autoencoder / Random Forest |
| **Arduino SAMD21 / AVR**| 16–48 MHz (8-bit / 32-bit) | 2–32 KB | 32–256 KB | Microsoft Bonsai / Branchless C Trees |

---

## SOTA TinyML Model Matrix

| Model Architecture | Quantization / Strategy | Execution Engine | SRAM Footprint | ESP32 Latency | Operational Advantage |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Quantized INT8 Autoencoder** | Full Integer PTQ (INT8) | TFLite Micro | 12–30 KB | 0.15–0.35 ms | **Unsupervised Detection:** Learns normal traffic; flags zero-day attacks via MSE error spikes. |
| **Microsoft Bonsai** | Sparse Matrix Projection | EdgeML (Pure C) | **< 2–8 KB** | **~0.05 ms** | **Ultra-Low Memory:** Fits inside highly constrained RAM without requiring an interpreter engine. |
| **C-Compiled Decision Forest** | Hardcoded C Branching Logic | `emlearn` / Treelite | **< 4 KB** | **< 0.01 ms (10 µs)** | **Microsecond Line-Rate Drop:** Replaces tensor runtime with pure nested `if-else` C loops. |
| **1D-CNN (Temporal Window)** | INT8 Weight & Activation | TFLite Micro / Edge Impulse | 25–60 KB | 0.30–0.80 ms | **Sequence Tracking:** Captures temporal correlations across sliding inter-arrival time ($IAT$) windows. |
| **Binary Neural Network (BNN)** | 1-bit Weights & Activations | Custom C / Larq | < 5 KB | 0.02–0.04 ms | **Bitwise Processing:** Replaces heavy MAC units with native bitwise `XNOR` and `POPCNT` calls. |

---
