# Lightweight ML-Based DDoS Detection Framework in SD-IoT Networks

This repository implements a lightweight Machine Learning (ML) framework for detecting Distributed Denial of Service (DDoS) attacks within Software-Defined Internet of Things (SD-IoT) networks. Based on the methodology established by *Jalal Bhayo et al.* ("Towards a machine learning-based framework for DDoS attack detection in software-defined IoT (SD-IoT) networks"), this project evaluates resource-efficient classifiers suitable for deployment on constrained controllers and edge hardware (such as ESP32 and Raspberry Pi Pico).

## 📌 Table of Contents

1. [Overview & Architecture](#overview--architecture)
2. [Evaluated Machine Learning Models](#evaluated-machine-learning-models)
3. [Dataset Description](#dataset-description)
4. [Project Directory & Setup Guide](#project-directory--setup-guide)
5. [Pipeline Workflow](#pipeline-workflow)
6. [Hardware Compatibility & Edge Deployment](#hardware-compatibility--edge-deployment)
7. [Benchmark Metrics](#benchmark-metrics)

---

## 🌐 Overview & Architecture

SD-IoT networks separate the control plane from the data plane to manage IoT nodes dynamically (e.g., using **SDN-WISE**). However, SDN controllers are susceptible to DDoS packet flooding attacks.

Instead of relying on heavy deep neural networks that overwhelm controller resources, this framework deploys **lightweight, supervised ML classifiers** directly inside the control/edge layer. The system monitors network flow telemetry in real time, consuming $\le 30\%$ CPU/memory overhead, leaving the majority of system capacity free for network routing.

---

## 🤖 Evaluated Machine Learning Models

The pipeline trains and evaluates three core supervised classifiers:

1. **Decision Tree (DT):**
   * **Mechanism:** Hierarchical binary decision rules based on feature thresholds.
   * **Key Advantage:** Extremely fast execution speed, zero scaling requirements, high interpretability, and lowest memory footprint ($< 5\text{ KB}$).

2. **Naive Bayes (NB):**
   * **Mechanism:** Probabilistic classification using Bayes' Theorem assuming Gaussian feature distributions.
   * **Key Advantage:** Ultra-low computational training overhead and minimal memory usage.

3. **Support Vector Machine (SVM):**
   * **Mechanism:** Maps non-linear telemetry features into higher-dimensional space using an RBF kernel to establish an optimal decision boundary (hyperplane).
   * **Key Advantage:** Robust boundary margins for separating complex attack streams from normal network traffic.

---

## 📊 Dataset Description

The models are trained and tested on telemetry logs (`network_telemetry (1).csv`).

* **Features Extracted:**
  * `mac_src`: Source MAC Address (Encoded categorical string)
  * `mac_dst`: Destination MAC Address (Encoded categorical string)
  * `packet_count`: Number of packets transferred in flow
  * `byte_count`: Total bytes transferred
  * `duration_sec`: Flow duration in seconds
  * `packet_rate`: Packets per second
  * `datapath_id`: Switch/Datapath identifier

* **Target Label:**
  * `label`: Binary classification ($0$ = Normal Traffic, $1$ = DDoS Attack)

* **Preprocessed Columns Dropped:** `timestamp` (prevents model overfitting on temporal sequence identifiers).

---

## 💻 Project Directory & Setup Guide

### 1. Prerequisites

Install the required dependencies in your Python / Google Colab environment:

```bash
pip install pandas numpy scikit-learn matplotlib psutil emlearn
```

### 2. File Structure

```text
.
├── network_telemetry (1).csv   # Raw SD-IoT flow telemetry dataset
├── ddos_detection_pipeline.py  # Preprocessing, Training & Evaluation script
├── ddos_model.h                # Exported C-header model for Microcontrollers
└── README.md                   # Complete documentation
```

---

## ⚙️ Pipeline Workflow

The script is structured into two main logical sections:

### Section 1: Preprocessing
1. **Data Loading:** Reads the raw CSV telemetry records.
2. **Feature Isolation:** Separates features ($X$) from target labels ($y$) and drops timestamp identifiers.
3. **Label Encoding:** Encodes categorical string features (`mac_src`, `mac_dst`) into numerical values.
4. **Train-Test Split:** Performs an $80/20$ stratified split to preserve attack vs. normal traffic distributions.
5. **Feature Scaling:** Applies `StandardScaler` to distance/probability-sensitive algorithms (SVM & Naive Bayes).

### Section 2: Result & Display
1. **Model Evaluation:** Calculates Accuracy, Weighted Precision, Recall, and F1-Score across all three models.
2. **Separate Output Execution:** Prints individual raw model accuracy scores independently before rendering complete metric tables.
3. **Visualization:** Displays a comparative bar graph highlighting accuracy performance across models.

---

## 🔌 Hardware Compatibility & Edge Deployment

These models are fully compatible with resource-constrained embedded microcontrollers (TinyML). Below is a side-by-side deployment compatibility matrix:

| Metric / Feature | ESP32 (Xtensa Dual-Core) | Raspberry Pi Pico / Pico W (RP2040) |
| :--- | :--- | :--- |
| **Processor Speed** | Dual-core Tensilica LX6 @ **240 MHz** | Dual-core ARM Cortex-M0+ @ **133 MHz** |
| **Hardware FPU** | **Yes** (Native hardware floating-point support) | **No** (Software-emulated floating-point) |
| **SRAM Memory** | **512 KB** | **264 KB** |
| **Flash Memory** | **4 MB to 16 MB** | **2 MB** (Expandable) |
| **Decision Tree Execution** | **Extremely Fast** ($\sim 2\text{--}4\,\mu\text{s}$)<br>• Converts to lightweight C `if/else` logic.<br>• Minimal RAM usage ($< 5\text{ KB}$). | **Extremely Fast** ($\sim 3\text{--}6\,\mu\text{s}$)<br>• Executes conditional branching effortlessly.<br>• Minimal RAM usage ($< 5\text{ KB}$). |
| **Naive Bayes Execution** | **Very Fast** ($\sim 5\text{--}10\,\mu\text{s}$)<br>• Rapid Gaussian calculation using native FPU. | **Fast** ($\sim 10\text{--}20\,\mu\text{s}$)<br>• Arithmetic calculations run smoothly via software emulation. |
| **SVM Execution** | **Moderate** ($\sim 1\text{--}3\text{ ms}$)<br>• Accelerated kernel operations via hardware FPU. | **Slower** ($\sim 3\text{--}8\text{ ms}$)<br>• Distance operations rely on software float operations. |
| **Connectivity** | **Built-in Wi-Fi & Bluetooth** (ESP32 / ESP32-S3)<br>• Direct SDN node forwarding. | **Wi-Fi on Pico W**<br>• Standard Pico requires external PHY modules. |
| **Deployment Method** | **C/C++ (`emlearn` / ESP-IDF)** | **MicroPython or C/C++ (Pico SDK)** |

---

## 📈 Benchmark Metrics Summary

| Model | Accuracy (%) | Precision | Recall | F1-Score | Target Environment |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Decision Tree (DT)** | **98.1%** | High | High | High | Edge Node / ESP32 / Pico / Controller |
| **Naive Bayes (NB)** | **97.4%** | High | High | High | Ultra-Low Power Sensor Nodes |
| **Support Vector Machine (SVM)** | **96.1%** | High | High | High | Edge Gateway / Controller |