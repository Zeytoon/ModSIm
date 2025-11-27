ModSim System
│
├── 1. USER INTERFACE LAYER (VB6 GUI)
│     ├── Modsimvb/        ← Visual Basic 6.0 project
│     │     ├── Forms (Flowsheet editor, dialogs, menus)
│     │     ├── Data input screens
│     │     ├── Stream/property panels
│     │     ├── Model parameter entry UI
│     │     └── Simulation control (Run, Stop, Report, Save)
│     │
│     └── Responsibilities:
│           • Manage user interaction
│           • Allow users to create/edit flowsheets
│           • Load/save flowsheet files
│           • Prepare model parameters for DLL input
│           • Display results (tables, plots)
│           • Call DLL functions (INORDCALC, SIMOP)
│
├── 2. GRAPHICS + FLOWSHEET EDITOR LAYER (Vetgraph VB6)
│     ├── Vetgraph/        ← Visual Basic 6.0 graphics library
│     │     ├── GDI-based drawing engine
│     │     ├── Custom controls for:
│     │     │     • Unit icons
│     │     │     • Streams (arrows/lines)
│     │     │     • Plotting graphs
│     │     └── User interaction (drag-drop, connect streams)
│     │
│     └── Responsibilities:
│           • Render flowsheet diagrams visually
│           • Provide a graphical surface inside the ModSim GUI
│           • Allow user interaction (add units, connect lines)
│           • Draw plots, histograms, size distributions
│           • NOT involved in simulation logic
│
├── 3. DLL INTERFACE LAYER (VB6 ↔ Fortran bridge)
│     ├── VB6 DECLARE statements (inside Modsimvb)
│     │     Example:
│     │     Declare Function INORDCALC Lib "modsim.dll" (...)
│     │
│     ├── Exported Fortran routines in DLL:
│     │     • INORDCALC(JobPath)
│     │     • SIMOP(JobPath, CumOut)
│     │     • (possibly others, rarely used)
│     │
│     └── Responsibilities:
│           • Convert VB6 string paths → Fortran strings
│           • Pass VB6 arrays/parameters → Fortran COMMON blocks
│           • Call Fortran DLL for calculation & results
│           • Return status/error codes
│
├── 4. CORE SIMULATION ENGINE (Fortran DLL — Modsimdl)
│     Modsimdl/    ← Compaq Visual Fortran project
│
│     4.1 Main Entrypoints
│         ├── SIMULATE.FOR (INORDCALC)
│         └── SIMOP.FOR
│
│     4.2 Input Subsystem
│         ├── DMINP.FOR
│         ├── DATAINPT.FOR (INPT)
│         ├── PARSET.FOR
│         ├── TEMPLATE.FOR
│         ├── PLANT.DAT (metadata)
│         └── Interaction with VB6 folder structure
│
│     4.3 Global Data Model
│         ├── GLOBALS.F90
│         └── ModelVariables.f90
│
│     4.4 Ordering System (flowsheet topology)
│         └── ORDER.FOR
│
│     4.5 Solver Core
│         ├── CALC.FOR
│         ├── CALC1.FOR
│         └── GENLSUBS.FOR
│
│     4.6 Unit Dispatcher
│         ├── UNITS.FOR
│         ├── GENLMODS.FOR      (basic models)
│         └── OUTPMODS.FOR      (output/sink models)
│
│     4.7 Unit Model Families
│         ├── CRSHMODS.FOR      (crushers)
│         ├── SCRNMODS.FOR      (screens)
│         ├── MILLMODS.FOR      (mills)
│         ├── CLASMODS.FOR      (classifiers)
│         ├── FLTNMODS.FOR      (flotation)
│         ├── GRAVMODS.FOR      (gravity separation)
│         ├── WSEPMODS.FOR      (water separation)
│         ├── MAGNMODS.FOR      (magnetic)
│         └── COALMODS.FOR      (coal)
│
│     4.8 Reporting System
│         ├── GENLREPS.FOR
│         ├── UNITREPS.FOR
│         ├── CRSHREPS.FOR
│         ├── SCRNREPS.FOR
│         ├── MILLREPS.FOR
│         └── REPWRT.FOR        (master report writer)
│
│     4.9 Special / Advanced Modules (optional tools)
│         ├── LJUBAMD.FOR
│         ├── BETAAMD.FOR
│         └── TRYAMD.FOR
│
│     4.10 Fortran–IMSL Dependencies
│          (IMSL used for numerical solvers in some builds)
│
│
└── 5. FILES & DATA FLOW BETWEEN COMPONENTS
      ├── User files:
      │     • *.fls (flowsheet file from VB6)
      │     • Job folders with input data
      │
      ├── VB6 generates:
      │     • FORMAT.DAT (formatting options for SIMOP)
      │     • Working directory with model data
      │
      ├── Fortran engine reads:
      │     • PLANT.DAT (metadata)
      │     • Data produced by VB6 UI
      │
      ├── Fortran engine outputs:
      │     • Raw result files
      │     • Summary report files
      │
      └── SIMOP formats results for display in GUI
