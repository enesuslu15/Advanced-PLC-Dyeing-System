# 🏭 Advanced PLC Fabric Dyeing & Chemical Dosing System

![Siemens TIA Portal](https://img.shields.io/badge/Siemens-TIA_Portal_v16+-blue.svg)
![PLC](https://img.shields.io/badge/PLC-S7--1200%20%2F%201500-lightgrey.svg)
![Language](https://img.shields.io/badge/Language-LAD%20%7C%20SCL-green.svg)
![HMI](https://img.shields.io/badge/HMI-KTP700_Basic-orange.svg)
![Status](https://img.shields.io/badge/Status-Completed-success.svg)
![License](https://img.shields.io/badge/License-MIT-yellow.svg)

An advanced, industrial-grade simulated PLC project designed for the textile industry, focusing on **fabric dyeing automation** and **precise chemical dosing**. Developed using **Siemens TIA Portal**, this project demonstrates high-level PLC programming techniques including complex **State Machines**, **Cascade PID Control Loops**, and **Data Block-driven Recipe Management**.

---

## 🚀 Core Features & Technologies

| Feature | Technology | Description |
|---|---|---|
| **Recipe Management** | UDT + Data Blocks | Manages 20+ fabric/dye recipes with structured arrays (water volume, target temp, dose times) |
| **Cascade PID Control** | `PID_Compact` (Master/Slave) | Master PID controls temperature setpoint; Slave PID modulates the steam valve proportionally |
| **Precision Timer Dosing** | `TON` (Timer On Delay) | Millisecond-accurate pump control for Chemical A & B based on active recipe parameters |
| **State Machine** | `SET/RESET` Interlock | 5-phase sequential logic: Intake → Heating → Dosing → Cooling → Draining |
| **Analog Processing** | `NORM_X` / `SCALE_X` | Converts raw PT100 sensor input (0–27648) to engineering units (0.0°C – 150.0°C) |
| **Alarm System** | Set/Reset Latching | Motor overload detection with HMI-triggered alarm acknowledgment |
| **HMI Integration** | KTP700 Basic Panel | Real-time monitoring, recipe selection, trend curves, and alarm views |

---

## 🔄 Operation Logic & Workflow

The system follows a fully automated batch process controlled by a **Finite State Machine (FSM)**:

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  PHASE 1 │───▶│  PHASE 2 │───▶│  PHASE 3 │───▶│  PHASE 4 │───▶│  PHASE 5 │
│  INTAKE  │    │ HEATING  │    │  DOSING  │    │ COOLING  │    │ DRAINING │
│          │    │          │    │          │    │          │    │          │
│Water Valve│    │Cascade   │    │Chemical  │    │Cold Water│    │Drain     │
│Opens     │    │PID Active│    │Pumps Run │    │Valve Open│    │Valve Open│
└──────────┘    └──────────┘    └──────────┘    └──────────┘    └──────────┘
 Trigger:        Trigger:        Trigger:        Trigger:        Trigger:
 START Button    Water Level     Temp >= Target  Dose Timer Q    Temp <= Safe
                 >= Recipe       (PID Mode=3)    (Pumps Stop)    (e.g. 40°C)
```

1. **Preparation:** The operator selects a recipe via the HMI (e.g., Recipe ID: 5) and presses **LOAD**. The PLC copies the recipe's parameters from `DB_Recipes` into `DB_Active_Process`.
2. **Phase 1 – Water Intake:** The **START** button activates `Step_1_Su_Alma`. The water inlet valve (`Q0.1`) opens. When the flow meter reading reaches the recipe's target volume, the system transitions to Phase 2.
3. **Phase 2 – Heating:** The Cascade PID system engages (`Mode = 3`). The Master PID reads the current water temperature and drives the Slave PID, which modulates the steam valve. Once `Current_Temp >= Target_Temp`, the system advances.
4. **Phase 3 – Chemical Dosing:** PID loops are deactivated (`Mode = 0`). Chemical pumps activate for the duration defined by the recipe's `TON` timer. When the timer expires (`Q = TRUE`), pumps stop and the system transitions.
5. **Phase 4 – Cooling:** The cooling water valve (`Q0.2`) opens. The system monitors temperature until it drops below the safe threshold (e.g., 40°C).
6. **Phase 5 – Draining:** The drain valve (`Q0.3`) opens. Level sensors confirm the tank is empty, triggering a Master Reset to return the machine to an **Idle – Ready for New Batch** state.

---

## 🏭 Industrial Use Cases

While this simulation focuses on textile dyeing, the underlying **Ladder Logic / SCL architecture** is highly transferable to various industrial sectors:

| Industry | Application |
|---|---|
| **Textile & Garment** | Industrial washing machines, bleaching systems, precision fabric dyeing |
| **Food & Beverage** | Batch pasteurization, CIP (Clean-In-Place) systems, ingredient mixing |
| **Chemical Processing** | Reactor tank temperature control, sequential blending, hazardous material dosing |
| **Pharmaceuticals** | Sterile batch processing with exact thermal curves and interlocked sequential steps |
| **Water Treatment** | Multi-stage filtration and chemical treatment with PID-controlled dosing |
| **Plastics & Rubber** | Extrusion temperature profiling and additive injection control |


---

## ⚙️ How to Simulate (TIA Portal)

1. Open the project in **TIA Portal (v16 or newer)**.
2. Navigate to **PLC_1** → Right-click → **Properties** → **Protection & Security**.
3. Enable **"Support simulation during block compilation"**.
4. Right-click the project root → **Compile** → **Hardware and Software (rebuild all)**.
5. Click **Start Simulation** from the toolbar to launch **PLCSIM**.
6. Open the HMI Runtime simulation to interact with the system (select recipe, press START, monitor phases).

---

## 🛠️ Tech Stack

- **IDE:** Siemens TIA Portal V16+
- **PLC:** S7-1200 / S7-1500
- **HMI:** KTP700 Basic (PN)
- **Simulation:** PLCSIM Advanced + WinCC Runtime
- **Languages:** Ladder Logic (LAD), Structured Control Language (SCL)
- **Key Blocks:** `PID_Compact`, `MOVE`, `TON`, `NORM_X`, `SCALE_X`, `CMP`

---



