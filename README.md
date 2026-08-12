# MOPSTLLM

**MOPSTLLM: Improved Multi-Objective Particle Swarm Optimisation-Guided Spatio-Temporal Large Language Model for Traffic Flow Prediction**

MOPSTLLM is a hybrid traffic flow forecasting framework that integrates **Particle Swarm Optimisation (PSO)**, **spatial correlation modeling**, and a **Large Language Model (LLM)**. The framework combines temporal traffic patterns, spatial dependencies between sensors, and LLM-based prediction to improve multi-step traffic flow forecasting.

## Overview

The overall prediction pipeline is:

```text
Historical Traffic Flow
        ↓
Multiple Candidate Predictors
        ↓
Multi-Objective PSO Optimisation
        ↓
PSO Weighted Prediction
        ↓
Top-K Spatial Correlation Modeling
        ↓
Spatial Enhancement
        ↓
LoRA Fine-Tuned LLM
        ↓
Future Traffic Flow Prediction
```

PSO generates and optimizes multiple candidate predictions based on short-term trends, daily/weekly periodicity, historical profiles, and similar traffic patterns. Spatial information is then introduced by selecting highly correlated traffic sensors and aggregating their predictions. Finally, the spatially enhanced prediction and temporal information are provided to a LoRA fine-tuned LLM, which directly generates the final traffic flow forecasts.

## Traffic Forecasting Task

Experiments are conducted on the **PeMS03** traffic dataset.

* Number of sensors: **358**
* Sampling interval: **5 minutes**
* Historical window: **12 steps (60 minutes)**
* Prediction horizon: **12 steps (60 minutes)**

```text
Input : Past 12 traffic flow observations
Output: Future 12 traffic flow observations
```

## Method

MOPSTLLM consists of three main components:

### 1. PSO-Guided Temporal Prediction

Multiple temporal predictors are constructed using short-term trends, daily/weekly periodic patterns, traffic profiles, and historical analogues. PSO optimizes both temporal weights and candidate fusion weights to obtain a robust initial prediction.

### 2. Spatial Enhancement

A sensor correlation matrix is calculated from the training data. For each target sensor, the **Top-K correlated sensors** are selected and their PSO predictions are similarity-weighted to produce a spatial aggregated prediction.

The spatial information is used to refine the PSO prediction before it is passed to the LLM.

### 3. LoRA Fine-Tuned LLM

The LLM receives the target sensor history, PSO candidate predictions, spatial neighbor information, spatially enhanced prediction, and temporal context.

The model is fine-tuned using **LoRA** and directly generates:

```json
{
  "sensor_id": "sensor_120",
  "predicted_flow": [123, 128, 130, 129, 135, 140, 142, 138, 136, 132, 130, 128]
}
```

The generated `predicted_flow` is used as the **final forecasting result**.

## Evaluation

The model is evaluated using:

* MAE
* RMSE
* Masked MAPE
* Peak MAE
* Parse Failure Rate

The main optimization target is to achieve competitive traffic forecasting accuracy while effectively combining **temporal patterns, spatial dependencies, PSO optimization, and LLM reasoning capabilities**.

## Framework

```text
MOPSTLLM
├── Multi-Objective PSO
│   ├── Temporal Weight Optimisation
│   └── Candidate Prediction Fusion
├── Spatial Modeling
│   ├── Sensor Correlation Matrix
│   ├── Top-K Neighbor Selection
│   └── Spatial Prediction Enhancement
└── Spatio-Temporal LLM
    ├── Structured Traffic Prompt
    ├── LoRA Fine-Tuning
    └── 12-Step Traffic Prediction
```
