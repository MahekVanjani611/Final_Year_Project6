# 🛡️ PSL Logic-Driven Digital Twin Model of MiCOM P642 Transformer Protection Relay

A full digital twin of the MiCOM P642 transformer protection relay integrating:

- PSL logic extraction & trip mapping  
- Neural BlackBox modelling (TripNet–TowerNet)  
- Real-time Modbus communication  
- MATLAB/Simulink-based differential protection (Idiff)  

This repository provides a hardware-independent environment for relay testing, analysis, and ML-driven protection research.

## 🚀 Overview

This project reconstructs the behaviour of the MiCOM P642 relay across three layers:

1. **Static PSL Layer** – Rebuilds complete logical pathways, timers, and DDB mappings.  
2. **Dynamic ML Layer** – A neural digital twin (TripNet–TowerNet) learns trip behaviour.  
3. **Numerical Protection Layer** – A Simulink model implements Idiff differential protection.  
4. **Communication Layer** – Modbus interface streams real relay states into the digital twin.


## 🧩 System Architecture

```

PSL Parsing → Trip Mapping → TripNet → TowerNet → Digital Twin
↑                ↓
Modbus DDB Feeds     MATLAB Idiff Model

```



## ⭐ Key Features

- Full PSL logic extraction with hybrid PDF + OCR pipeline  
- Real-time Modbus TCP/RTU data acquisition  
- Neural surrogate model achieving **~85% multi-label accuracy**  
- Class-imbalance handling with weighted loss scaling  
- Incremental fine-tuning when PSL logic updates  
- Complete Simulink model of MiCOM-style differential protection  
- Harmonic blocking (2H, 5H), triple-slope operating characteristic, CT saturation logic  


# 📁 Project Modules


## 1️⃣ PSL Logic Extraction (`/psl_extraction/`)

- Extracts DDBs, gates, and trip dependencies from PSL diagrams  
- Uses `pdfplumber`, `pytesseract`, regex parsing  
- Removes output DDBs to prevent feedback loops  
- Validates structure using DeepDiff  
- Produces **Trip Mapping Dictionaries** for ML training  


## 2️⃣ Modbus Interface (`/modbus_interface/`)

Real-time reading of:

- Digital Data Bits (DDBs)  
- Differential & bias currents  
- Harmonic ratios  
- PSL trip states  

Used for:

- Dataset generation  
- Real-time ML prediction  
- PSL–ML behaviour validation  

Modbus Specs:
- Baud: 19200  
- Data bits: 8  
- Parity: None  
- Timeout: 15 min  


## 3️⃣ Neural Digital Twin (`/neural_twin/`)

### **TripNet**  
Per-trip local logic learner:

```

Linear(n → 32) → ReLU → Linear(32 → 32) → ReLU → Linear(32 → 1) → Sigmoid

```

### **TowerNet**  
Aggregates TripNet outputs for final multi-trip prediction:

```

Linear(K → 64) → ReLU → Linear(64 → 32) → ReLU → Linear(32 → K)

````

### 🔥 ML Enhancements

- Handles **severe class imbalance** with weighted scaling  
- Learns **temporal PSL dynamics** (dwell timers, drop timers)  
- **Fine-tunes only affected TripNets** when PSL logic changes  
- TowerNet retrains automatically to realign global logic  

### 🎯 Accuracy  
**Precision: 0.845 | Recall: 0.845 | F1 Score: 0.845**


## 4️⃣ Simulink Differential Protection Model (`/simulink_idiff_model/`)

Implements MiCOM P642 differential protection:

- Per-cycle phasor extraction (DFT)  
- Idiff, Ibias, Idiff_peak calculation  
- Triple-slope percentage characteristic  
- 2nd/5th harmonic blocking  
- CT saturation & external-fault restraint  
- High-set elements HS1 and HS2  
- Final trip decision logic  

Validated against:

- Internal faults  
- External faults  
- Inrush  
- Overfluxing  
- CT saturation  

# 📊 Results

### ✔ Neural twin matches PSL outputs closely  
### ✔ Modbus polling stable & accurate  
### ✔ Simulink model reproduces MiCOM behaviour  
### ✔ Class-imbalance mitigation significantly improves learning  
### ✔ Fine-tuning seamlessly adapts model to new PSL logic  



# 🔭 Future Work

* Full Logic Design Layer (latches, interlocks, gating logic)
* Raw waveform-level digital twin (anti-aliasing, LPF, resampling)
* Trip reason classification
* Multi-winding transformer support
* HIL testing with RTDS / Typhoon HIL
* Web-based dashboard for real-time analytics




