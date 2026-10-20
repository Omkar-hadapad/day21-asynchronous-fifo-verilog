# Day 21 — Asynchronous FIFO Design and Verification

<p align="center">
  **Digital VLSI • Verilog RTL • FIFO Architecture • CDC • Cadence Genus**
</p>

<p align="center">
  `Specification → RTL → Testbench → Simulation → Synthesis → Area → Power → Timing → PPA Review`
</p>

![Project](https://img.shields.io/badge/Project-Asynchronous%20FIFO-blue)
![HDL](https://img.shields.io/badge/HDL-Verilog-8A2BE2)
![Synthesis](https://img.shields.io/badge/Synthesis-Cadence%20Genus-orange)
![Verification](https://img.shields.io/badge/Verification-24%20Pass%20%7C%200%20Fail-success)

---

## 1. Project Information

| Parameter | Details |
|---|---|
| **Project** | Asynchronous FIFO Design and Verification |
| **Author** | Omkar Kalmesh Hadapad |
| **Domain** | Digital VLSI / RTL Design / Functional Verification |
| **Design Type** | Asynchronous FIFO |
| **HDL** | Verilog HDL |
| **Memory Configuration** | 8 words × 8 bits, based on the `fifo_memory_8x8` module name |
| **Storage Capacity** | 64 bits |
| **Pointer Width** | 4 bits, inferred from the Gray-conversion and synchronizer module names |
| **Clock Domains** | Write clock and read clock |
| **Pointer Encoding** | Binary and Gray code |
| **Simulation Tool** | Cadence NCSim |
| **Synthesis Tool** | Cadence Genus 21.14-s082_1 |
| **Technology Library** | `tsmc18` |
| **Top Module** | `day21_async_fifo_top` |
| **FIFO Instance** | `U_ASYNC_FIFO` (`async_fifo_structural`) |
| **Reported Functional Checks** | 24 pass, 0 fail |
| **Simulation Completion** | `$finish` at 326 ns |
| **Synthesis Cell Count** | 222 standard-cell instances |
| **Reported Cell Area** | 8751.758 library area units |
| **Estimated Total Power** | 195.918 µW |
| **Timing Status** | A reported path is marked unconstrained |
| **Physical Design Status** | Post-route PPA closure not established |

> **Measurement note:** The cell-area unit has not been confirmed as µm². The power result is an estimate associated with activity frame `/stim#0/frame#0`. The available reports do not establish routed, extracted-parasitic PPA closure.

---

## 2. Project Overview

This project implements and verifies an asynchronous FIFO intended to transfer data between separate write-clock and read-clock domains.

The reported hierarchy contains a FIFO memory block, binary-to-Gray conversion blocks, and two Gray-code synchronizer blocks. The simulation transcript reports 24 passing checks and zero failures, with the testbench finishing at 326 ns.

The project covers:

- Asynchronous FIFO architecture.
- Independent write and read clock domains.
- FIFO data storage.
- Binary-to-Gray pointer conversion.
- Gray-coded pointer synchronization.
- Full and empty status handling.
- Functional simulation using Cadence NCSim.
- Standard-cell synthesis using Cadence Genus.
- Hierarchical area analysis.
- Power report interpretation.
- Setup-timing investigation and CDC considerations.

---

## 3. Objectives

1. Understand asynchronous FIFO architecture.
2. Implement FIFO storage and independent read/write interfaces.
3. Understand binary pointer progression and wraparound.
4. Convert binary pointers to Gray code.
5. Synchronize Gray-coded pointers across clock domains.
6. Verify FIFO empty and full status behavior.
7. Verify write and read data integrity.
8. Verify asynchronous reset behavior.
9. Run a self-checking Verilog testbench.
10. Inspect simulation results and waveforms.
11. Synthesize the design using Cadence Genus.
12. Analyze cell count and area distribution.
13. Interpret internal, switching and leakage power.
14. Investigate unconstrained timing paths.
15. Identify the additional evidence required for timing and physical-design closure.

---

## 4. FIFO Architecture

## 6. Project Flowchart

The following flowchart illustrates the simulation and verification process of the Day 21 Asynchronous FIFO design, including asynchronous reset, write and read operations, data comparison, and final pass/fail reporting.

```mermaid
flowchart TD
    A([Start Simulation]) --> B[Initialize Clocks and Signals]
    B --> C[Apply Asynchronous Reset]
    C --> D[Reset Pointers and Synchronizers]
    D --> E{FIFO Empty?}

    E -- >Yes --> F[Verify Initial Status]
    F --> G[Prepare Character Data]
    G --> H{FIFO Full?}

    H -- >No --> I[Assert Write Enable]
    I --> J[Write Character to FIFO]
    J --> K[Update Write Pointer]
    K --> L[Synchronize Gray Pointer]
    L --> M{All Characters Written?}

    M -- >No --> G
    M -- >Yes --> N[Prepare Read Operation]
    N --> O{FIFO Empty?}

    O --> No --> P[Assert Read Enable]
    P --> Q[Read Character from FIFO]
    Q --> R[Compare Read Data with Expected Data]
    R --> S{Data Match?}

    S --> Yes --> T[Increment Pass Count]
    S -- >No --> U[Increment Fail Count]

    T --> V{All Characters Read?}
    U --> V
    V --> No --> O
    V --> Yes --> W[Verify Final FIFO Status]
    W --> X{Fail Count = 0?}

    X --> Yes --> Y[Display FINAL RESULT PASS]
    X --> No --> Z[Display FINAL RESULT FAIL]

    Y --> AA([End Simulation])
    Z --> AA

    ## 6. Project Flowchart

This flowchart summarizes the asynchronous FIFO verification process, from reset and data transfer to final result reporting.

```mermaid
flowchart LR
    A([Start]) --> B[Initialize and Reset]
    B --> C[Check FIFO Empty]
    C --> D[Write OMKAR]
    D --> E[Update Write Pointer]
    E --> F[Synchronize Gray Pointer]
    F --> G[Read FIFO Data]
    G --> H{Data Matches?}
    H -- Yes --> I[Increment Pass]
    H -- No --> J[Increment Fail]
    I --> K[Check Final FIFO Status]
    J --> K
    K --> L{Fail Count = 0?}
    L -- Yes --> M([PASS])
    L -- No --> N([FAIL])
```

**Verification result:** 24 passes, 0 failures. The expected string `OMKAR` was written and read successfully.
```

**Verification result:** 24 passes and 0 failures, with the expected string `OMKAR` successfully written to and read from the FIFO.
### Block diagram

```text
                  day21_async_fifo_top
                           |
                           v
                     U_ASYNC_FIFO
                   async_fifo_structural
                           |
          +----------------+----------------+
          |                |                |
          v                v                v
    FIFO Memory      Write Pointer     Read Pointer
     8 words × 8       Binary/Gray      Binary/Gray
          |                |                |
          |                v                v
          |         Write-side Gray   Read-side Gray
          |          synchronizer      synchronizer
          |                |                |
          +----------------+----------------+
                           |
                    Full / Empty Logic
                           |
                    Read and Write Data
```

This is a conceptual architecture. The exact signal connectivity and full/empty equations must be confirmed against the RTL.

### Reported hierarchy

```text
day21_async_fifo_top
└── U_ASYNC_FIFO (async_fifo_structural)
    ├── U_FIFO_MEMORY   (fifo_memory_8x8)
    ├── U_RD_GRAY_NEXT  (fifo_bin_to_gray4_22)
    ├── U_SYNC_RD_TO_WR (fifo_gray_sync4)
    ├── U_SYNC_WR_TO_RD (fifo_gray_sync4_23)
    └── U_WR_GRAY_NEXT  (fifo_bin_to_gray4)
```

### Hierarchy block summary

| Instance | Module | Function indicated by name |
|---|---|---|
| `U_FIFO_MEMORY` | `fifo_memory_8x8` | FIFO data storage |
| `U_WR_GRAY_NEXT` | `fifo_bin_to_gray4` | Write-pointer Gray conversion |
| `U_RD_GRAY_NEXT` | `fifo_bin_to_gray4_22` | Read-pointer Gray conversion |
| `U_SYNC_RD_TO_WR` | `fifo_gray_sync4` | Read-pointer synchronization into write domain |
| `U_SYNC_WR_TO_RD` | `fifo_gray_sync4_23` | Write-pointer synchronization into read domain |

The synchronization directions are inferred from instance names and the conventional asynchronous FIFO architecture.

---

## 5. FIFO Operation

### Write operation

```text
Write data + Write enable + Write address
                    |
                    v
             Write-clock domain
                    |
                    v
              FIFO memory
                    |
                    v
          Update write pointer
                    |
                    v
          Binary-to-Gray conversion
                    |
                    v
       Synchronize pointer to read domain
```

### Read operation

```text
Read enable + Read address
            |
            v
      Read-clock domain
            |
            v
        FIFO memory
            |
            v
        Read data output
            |
            v
       Update read pointer
            |
            v
       Binary-to-Gray conversion
            |
            v
  Synchronize pointer to write domain
```

The actual read latency, write acceptance conditions and pointer-update rules depend on the implemented RTL.

### Empty and full conditions

An asynchronous FIFO commonly determines:

- **Empty:** No unread data is available to the reader.
- **Full:** The writer cannot safely accept another word without overwriting unread data.

These conditions are normally derived from the next pointer and a synchronized pointer from the opposite clock domain. The exact equations and pointer bit manipulation must be verified against the design source.

---

## 6. Functional Verification

### Reported NCSim results

| Test | Result |
|---|---|
| AND gate | PASS |
| OR gate | PASS |
| NOT gate | PASS |
| XOR gate | PASS |
| Binary-to-Gray conversion | PASS |
| Asynchronous reset | PASS |
| Initial FIFO status | PASS |
| Write string `OMKAR` | All 5 characters passed |
| Read and verify string `OMKAR` | All 5 characters passed |
| Final FIFO status | PASS |

### Verification summary

| Metric | Reported result |
|---|---:|
| Total pass count | **24** |
| Total fail count | **0** |
| Expected string | `OMKAR` |
| Received string | `OMKAR` |
| Final `FULL` | `0` |
| Final `EMPTY` | `1` |
| Simulation completion | **326 ns** |
| Simulator termination | `$finish` |

The transcript shows the expected string matching the received string and a clean testbench completion.

### Verification limitations

The transcript establishes the checks reported by the testbench. It does not independently establish:

- Exhaustive FIFO overflow and underflow behavior.
- Correct operation for every relative clock frequency or phase.
- Metastability behavior or complete CDC safety.
- Formal equivalence or exhaustive functional coverage.

For stronger verification, add or inspect tests for FIFO capacity, pointer wraparound, full and empty boundary transitions, simultaneous read/write activity, independent clock periods, and reset assertion/deassertion.

---

## 7. Genus Synthesis Results

The supplied Cadence Genus hierarchy and cell reports provide the following results.

| Metric | Reported value |
|---|---:|
| Top module | `day21_async_fifo_top` |
| Standard-cell instances | **222** |
| Total reported cell area | **8751.758 library area units** |
| Technology library | `tsmc18` |
| Operating condition | `slow (balanced_tree)` |
| Wireload mode | `enclosed` |
| Synthesis tool | Cadence Genus 21.14-s082_1 |

These values are from synthesis-stage reports. The available evidence does not establish placement, routing or extracted-parasitic timing closure.

---

## 8. Standard-Cell Mapping Analysis

Selected cell categories from the supplied Genus report:

| Standard cell | Instances | Reported area |
|---|---:|---:|
| `SDFFHQX1` | 64 | 4257.792 |
| `DFFRHQX1` | 31 | 2165.486 |
| `NAND2XL` | 27 | 269.438 |
| `AOI22XL` | 16 | 266.112 |
| `DFFHQX1` | 8 | 425.779 |
| `XOR2XL` | 8 | 212.890 |
| **Total** | **222** | **8751.758** |

### Area distribution

| Category | Instances | Area | Reported share |
|---|---:|---:|---:|
| Sequential | 104 | 6912.259 | 79.0% |
| Inverter | 10 | 66.528 | 0.8% |
| Logic | 108 | 1772.971 | 20.3% |
| Physical cells | 0 | 0.000 | 0.0% |
| **Total** | **222** | **8751.758** | **100.0%** |

### Interpretation

- Sequential cells account for most of the reported cell area.
- The `SDFFHQX1` category is the largest individual contributor in the supplied cell summary.
- The standard-cell implementation is consistent with a register-based storage structure, but the exact function of every mapped cell requires library documentation and netlist inspection.
- The area report alone does not establish whether a memory macro was considered or whether an alternative memory implementation is available.

---

## 9. Hierarchical Area Analysis

The hierarchy area report gives:

| Hierarchy block | Cell count | Reported area | Share of total area |
|---|---:|---:|---:|
| `U_FIFO_MEMORY` | 151 | 5841.158 | 66.75% |
| `U_SYNC_RD_TO_WR` | 9 | 565.488 | 6.46% |
| `U_SYNC_WR_TO_RD` | 9 | 565.488 | 6.46% |
| `U_RD_GRAY_NEXT` | 3 | 79.834 | 0.91% |
| `U_WR_GRAY_NEXT` | 3 | 79.834 | 0.91% |
| Other top-level/control logic, calculated remainder | 47 | 1540.156 | 17.60% |
| **Total** | **222** | **8751.758** | **100.00% approximately** |

The other top-level/control figure is calculated by subtracting the five listed submodules from the top-level total. It is not a separately named instance in the supplied report.

### Main observation

The FIFO memory is the largest reported area contributor, accounting for approximately **66.75%** of the total cell area.

The two synchronizers together account for approximately **12.92%** of the reported area. The Gray conversion modules contribute relatively little area.

### Optimization priorities

1. Inspect the FIFO memory implementation.
2. Confirm whether the target flow supports an appropriate memory macro.
3. Preserve required synchronizer stages and CDC behavior.
4. Review the full/empty control logic.
5. Compare area reports only after re-running synthesis under consistent settings.

A memory macro is not automatically a drop-in replacement for a standard-cell implementation; port behavior, latency, reset behavior and technology support must match the design requirements.

---

## 10. Power Analysis

The supplied power report is for:

```text
Instance: /day21_async_fifo_top
Power Unit: W
PDB Frame: /stim#0/frame#0
```

### Power by category

| Category | Total power | Share |
|---|---:|---:|
| Register | 158.614 µW | 80.96% |
| Logic | 37.3042 µW | 19.04% |
| Memory | 0 µW | 0% |
| Clock | 0 µW | 0% |
| **Total** | **195.918 µW** | **100%** |

### Internal, switching and leakage power

| Component | Reported power | Share |
|---|---:|---:|
| Internal | 165.110 µW | 84.28% |
| Switching | 30.5378 µW | 15.59% |
| Leakage | 0.269756 µW | 0.14% |
| **Total** | **195.918 µW** | **100% approximately** |

### Interpretation

- Register-category power dominates the reported estimate.
- Internal power is the largest component of the total.
- Switching power depends on signal activity and capacitive loading.
- Zero power attributed to the separate memory category does not mean the design has no storage-related power; storage implemented using standard cells may be accounted for in the register category.
- The zero clock-category result does not establish that real clock networks consume no power.

Before comparing designs, confirm the activity annotation, clock definitions, operating conditions, and whether clock-tree and physical parasitic effects are included.

---

## 11. Timing Analysis

The previously supplied timing report contains the following path:

| Timing item | Reported value |
|---|---|
| Path status | **UNCONSTRAINED** |
| Startpoint | `U_ASYNC_FIFO/wr_bin_reg[0]/CK` |
| Endpoint | `U_ASYNC_FIFO/full_reg/D` |
| Reported setup field | `-361` |
| Data path delay | 2392 ps = 2.392 ns |
| Operating condition | `slow (balanced_tree)` |

### Reported data path

```text
wr_bin_reg[0]/CK
        |
        v
    DFFRHQX1
        |
        v
      AND2X1
        |
        v
      AND2X1
        |
        v
      NAND2XL
        |
        v
     OAI2BB2XL
        |
        v
       XOR2XL
        |
        v
       XOR2XL
        |
        v
       AND4X1
        |
        v
     full_reg/D
```

The path is consistent with pointer-related combinational logic feeding the full-status register. The exact Boolean function requires RTL or mapped-netlist confirmation.

### Timing interpretation

- The reported data-path delay is **2.392 ns**.
- The path is explicitly marked **UNCONSTRAINED**.
- The reported `-361` field must not be treated as validated negative slack without the full timing context.
- The report does not establish whether the launch and capture clocks are correctly constrained.
- A path marked unconstrained cannot be used to claim that the design passes or fails setup timing.

### Required timing checks

1. Inspect the SDC file for `create_clock` definitions for both write and read clocks.
2. Confirm that the launch and capture registers use the intended clocks.
3. Review generated clocks, input/output delays and timing exceptions.
4. Obtain the complete timing report, including launch/capture clock paths, arrival time, required time and slack.
5. Review all setup and hold timing summaries after correcting constraints.
6. Do not add false paths merely to hide an unconstrained timing warning.

**Timing closure is not established by the available report.**

---

## 12. Recommended Optimization Plan

| Priority | Investigation | Reason |
|---|---|---|
| 1 | Review `fifo_memory_8x8` | It accounts for 66.75% of reported cell area. |
| 2 | Correct and validate timing constraints | The reported full-flag path is unconstrained. |
| 3 | Validate power activity and clock accounting | The reported estimate depends on analysis assumptions. |
| 4 | Inspect pointer and full/empty logic | These affect FIFO correctness and timing. |
| 5 | Preserve CDC synchronizer structure | Area reduction must not compromise clock-domain safety. |
| 6 | Re-run simulation after RTL changes | Confirm that behavior remains correct. |
| 7 | Re-run Genus area, power and timing reports | Enables a consistent before/after comparison. |

Do not remove required synchronizer stages or change FIFO full/empty behavior merely to improve PPA.

---

## 13. Overall PPA Summary

| Metric | Result | Qualification |
|---|---|---|
| Functional simulation | 24 pass / 0 fail | Reported testbench checks |
| Simulation termination | `$finish` at 326 ns | Clean completion shown |
| Mapped cell count | 222 | Genus synthesis report |
| Reported cell area | 8751.758 area units | Physical unit not confirmed |
| Estimated total power | 195.918 µW | Activity-frame-based estimate |
| Largest area block | FIFO memory | 5841.158 area units |
| Largest power category | Register | 158.614 µW |
| Timing path delay | 2.392 ns | Reported path |
| Timing status | Unconstrained | Timing closure not established |
| Post-route PPA closure | Not established | Physical implementation evidence not supplied |

---

## 14. Suggested Repository Structure

```text
day21-async-fifo/
├── README.md
├── rtl/
│   ├── day21_async_fifo_top.v
│   ├── async_fifo_structural.v
│   ├── fifo_memory_8x8.v
│   ├── fifo_bin_to_gray4.v
│   └── fifo_gray_sync4.v
├── tb/
│   └── day21_tb.v
├── sim/
│   ├── simulation.log
│   └── waves.shm/
├── genus/
│   ├── constraints.sdc
│   ├── reports/
│   │   ├── hierarchy.rpt
│   │   ├── area.rpt
│   │   ├── power.rpt
│   │   └── timing.rpt
│   ├── mapped_netlist.v
│   └── synthesis.log
├── images/
│   └── async_fifo_architecture.png
└── docs/
    └── ppa_analysis.md
```

This is a suggested structure. Keep only files that exist in your repository and update filenames to match your actual source files.

---

## 15. Industry Relevance

This project demonstrates concepts relevant to:

- Synthesizable Verilog RTL.
- FIFO architecture and memory organization.
- Binary and Gray-code pointer logic.
- Clock-domain crossing fundamentals.
- Self-checking testbench development.
- Functional simulation using Cadence NCSim.
- Cadence Genus synthesis and technology mapping.
- Standard-cell area analysis.
- Power estimation and activity interpretation.
- Timing constraints and critical-path investigation.
- Technical documentation and engineering reporting.

These topics are relevant to entry-level RTL Design, ASIC Design, Design Verification, FPGA Design and Digital VLSI roles.

---

## 16. Interview Questions

1. What is an asynchronous FIFO?
2. Why are separate read and write clocks used?
3. What is the difference between binary code and Gray code?
4. Why is Gray code commonly used for asynchronous FIFO pointers?
5. What is metastability, and how do synchronizer stages help?
6. How are FIFO full and empty conditions determined?
7. Why does an asynchronous FIFO require pointer synchronization?
8. What happens when the FIFO is full and a write is requested?
9. What happens when the FIFO is empty and a read is requested?
10. Why might a small FIFO be implemented using standard-cell registers?
11. What does technology mapping do?
12. Why does the memory block dominate the reported area?
13. What are internal, switching and leakage power?
14. What does an unconstrained timing path mean?
15. Why is a reported path delay different from setup slack?
16. Why must CDC synchronizers not be removed as a simple area optimization?
17. How would you verify pointer wraparound?
18. What additional evidence is required to claim post-route PPA closure?

---

## 17. Project Status

| Stage | Status |
|---|---|
| Functional testbench | 24 pass / 0 fail reported |
| Simulation completion | Completed at 326 ns |
| Hierarchy report | Reviewed |
| Standard-cell area report | Reviewed |
| Hierarchical area report | Reviewed |
| Power report | Reviewed; activity assumptions need validation |
| Timing path | Reviewed; marked unconstrained |
| Full setup/hold timing closure | Not established |
| Post-route physical implementation | Not established |
| Final PPA closure | Pending further analysis |

---

## 18. Author

**Omkar Kalmesh Hadapad**  
Electronics and Communication Engineering

Digital VLSI • Verilog RTL • Functional Verification • Cadence Genus

---

<p align="center">
  **Day 21 — Asynchronous FIFO Design, Verification and Synthesis Analysis**
</p>
