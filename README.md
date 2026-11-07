# Paper Phase Transition — Utah Industry Model

**Vasion Challenge · Intermountain Quantum Competition 2026 · BYU, November 6–7**

Industries don't go digital gradually. They hold out, then flip once enough of their partners flip. This project models that tipping behavior using the Ising model and quantum annealing, calibrated with real Census Bureau economic data for five major Utah employers.

---

## Table of Contents

- [Overview](#overview)
- [The Model](#the-model)
- [BTOS Data Integration](#btos-data-integration)
- [Utah Adjustment](#utah-adjustment)
- [AI Pressure Schedule](#ai-pressure-schedule)
- [Utah Industries & Flip Years](#utah-industries--flip-years)
- [CSV Schema](#csv-schema)
- [How to Run](#how-to-run)
- [References](#references)

---

## Overview

Each industry in a region is modeled as a binary node:

| State | Meaning |
|-------|---------|
| `sᵢ = −1` | Still using paper |
| `sᵢ = +1` | Gone digital |

Every year, the economy settles into the lowest-energy configuration. The first year a node comes out `+1` is its **flip year**. Plotting all flip years produces a **tipping-point map**.

---

## The Model

### Energy Function

```text
Energy(year) = − Σ Jᵢⱼ·sᵢ·sⱼ  −  Σ (A(year) − rᵢ)·sᵢ
              ───────────────    ──────────────────────
              Ties: partners     Pressure vs. resistance
              pull toward        on each individual node
              matching states

Sign Convention for Solvers
Most solvers (including D-Wave Ocean) minimize:

text

Copiar
Σ hᵢ·sᵢ + Σ Jᵢⱼ·sᵢ·sⱼ
To match our energy function, use:

text

Copiar
hᵢ = −(A(year) − rᵢ)     (pressure term)
Jᵢⱼ = −(tie strength)     (tie term — negative so matching states are lower energy)
How It Works
Ties (Jᵢⱼ) pull partner industries toward the same state. If Insurance goes digital, Healthcare (tied strongly to Insurance) feels a pull toward digital too.
Pressure vs. Resistance (A − rᵢ) determines whether going digital lowers energy for a single node. When AI pressure exceeds resistance, digital becomes the lower-energy choice.
A node flips in the first year where the global energy minimum has it in the +1 (digital) state.
BTOS Data Integration
The Census Bureau's Business Trends and Outlook Survey (BTOS) provides real-time economic sentiment across industries. We use it to calibrate our model for Utah.

Key BTOS Metrics (November 2026 Data as of Nov 1, 2026)
Index	Value	Change	Meaning
Current Performance	54.8	−1.7 ↓	Doing okay, momentum slowing
Revenues	40.7	−0.9 ↓	Weak revenue expectations
Employees	47.7	+0.0	Flat hiring
Hours	46.0	−0.2	Slightly declining workload
Input Prices	78.2	−0.4	Costs are crushing margins — the dominant signal
Future Performance	53.3	−1.5 ↓	Optimism fading
Future Demand	50.3	−1.4 ↓	Barely any growth expected
Future Employees	50.8	−0.6 ↓	No hiring plans
Future Hours	49.3	−0.8 ↓	Planning to cut hours
Fut. Input Prices	78.2	−0.4	Costs expected to stay high
All indices are centered at 50 (neutral). Above 50 = expansion/pressure; below 50 = contraction.

The standout: Input Prices at 78.2 — the highest number in the entire survey. When costs are this extreme, companies adopt digital tools faster to cut waste. This single metric drives our regional adjustment.

Utah Adjustment
We convert the excess Input Prices pressure into a boost added to every year's AI pressure for Utah nodes:

text

Copiar
boost = (Input_Price_index − 50) / 100 × scaling_factor
boost = (78.2 − 50) / 100 × 0.35
boost ≈ 0.10
The 0.35 scaling factor was chosen so that the boost shifts flip years by roughly one year — a realistic adjustment for regional cost-pressure differences.

Result: Every Utah industry flips approximately one year earlier than the national baseline. This reflects the reality that Utah businesses, facing the same extreme cost pressures shown in the BTOS, have less patience for paper-based inefficiencies.

AI Pressure Schedule
Base (National)
text

Copiar
A(year) = 0.07 × (year − 2026)
Year	A(year)
2026	0.00
2027	