# ModSim – Original System Architecture (VB6 + Fortran + Vetgraph)

This document describes the **original architecture** of ModSim as it was implemented with:

- A **Visual Basic 6.0 GUI** (`Modsimvb`)
- A **Fortran simulation engine** built as a DLL (`Modsimdl`)
- A **Vetgraph graphics package** (VB6) for flowsheet drawing and plotting

The goal is to capture how the historical system was structured before any modern rewrites.

---

## 1. High-Level Overview

ModSim is a **steady-state mineral processing flowsheet simulator** composed of three main subsystems:

1. **User Interface (VB6 GUI)**  
   - Project: `ModSim/Modsimvb`  
   - Provides flowsheet editing, parameter input, and result visualization.

2. **Graphics / Flowsheet Drawing (Vetgraph)**  
   - Project: `Vetgraph`  
   - VB6 graphics engine used by the GUI to draw flowsheets and plots.

3. **Simulation Engine (Fortran DLL)**  
   - Project: `ModSim/Modsimdl`  
   - Compaq Visual Fortran (originally), later Intel Visual Fortran.  
   - Performs all numerical calculations and reporting.

---

## 2. Component Interaction

At runtime, the interaction looks like this:

graph TD
    GUI[Modsimvb (VB6 GUI)] --> VG[Vetgraph Graphics]
    GUI --> DLL[Fortran DLL (modsim.dll)]

    GUI -->|writes| JOB[Job Folder (input data)]
    DLL -->|reads| JOB
    DLL --> OUT[Report & Output Files]

    GUI -->|displays| OUT
    VG -->|renders| FS[Flowsheet Diagram & Plots]

