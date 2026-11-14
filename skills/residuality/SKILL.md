---
name: residuality
description: Residual analysis after Barry O'Reilly's Residuality Theory. Stress a naive architecture with many random, probability-free stressors, find the attractors, keep what survives, integrate the residues without contagion, and prove it on held-out stressors. Use when designing or reviewing an architecture, when asked to stress-test or "burn down" a design, or when residuality, stressors, attractors or residues come up.
---

# Residuality

Design by subtraction: hit a first design with stressors you never rank by likelihood, note what is left each time, strengthen that, then fold the survivors into one structure where no stressor crosses from one residue into another. The architecture is what remains. Correctness is the programmer's goal; the architect's goal is criticality: a structure that can move when the context changes.

The matrix step has its own skill, `contagion-analysis`, usable alone on a codebase or an MR. Read `references/theory.md` once per session for the definitions and the reasoning. `references/sources.md` has the primary material. Fill `assets/stressor-sheet.xlsx` (or the CSV twin) as you go; it has the sheets below in order.

## Terms

- **Stressor:** any fact about the context that is currently unknown. Failures, but also market, vendor, regulatory, people and absurd events. Never carries a probability.
- **Attractor:** a state the business keeps falling into under stress. Many stressors lead to one attractor; one stressor is enough to find it.
- **Residue:** what is left of the system (people, flows, functions, infrastructure) after a stressor, plus the augmentation that lets it survive there.
- **Delta:** the change the augmentation makes to the naive design.
- **Contagion:** a stressor crossing a residual boundary and breaking another residue. Hyperliminal coupling is two parts one external fact breaks at once.

## Procedure

1. **Naive architecture.** The first design that solves the functional problem, as flows between actors. A hypothesis, not a draft to defend. Write down the assumptions it rests on; each is a stressor.
2. **Stressors.** Aim for 150 to 250 rows from at least five sources: assumptions ("what if this is wrong"), ordinary failures, the business model (the richest source), regulation, vendors, people, and at least ten absurd ones. Take them to stakeholders and record how they would respond. Columns: Stressor, Impacts the ability to (a business capability), Detection, Mitigation (business), Technical mitigation. Write a response for every row, even one you would never build. When a new row is already covered by earlier rows, note it: that is convergence.
3. **Attractors.** Group rows by the state they push the business into. Two-line narrative per attractor. Effort is per attractor; coverage is per stressor.
4. **Residues and deltas.** Per attractor: what survives, and what is needed so the largest possible part keeps working. Expect overlap and repetition.
5. **Contagion analysis.** Fill the incidence matrix (stressors by flows, functions or components). Read it (below), move boundaries, repeat until long rows shorten and heavy columns thin. Boil the residues into as few components as possible without letting one stressor cross a residual boundary. List the trade-offs; each needs one decision.
6. **Held-out test.** Write 15 to 30 new stressors before looking at the residues again. Score naive versus residual. Residual index = (residual survivors − naive survivors) ÷ count. Positive means the residues generalise; near zero across rounds means stop.
7. **Discard the stressors.** Keep the attractors, the matrix, the trade-off list and the index. Normal risk management, FMEA, ATAM, tests and telemetry come after, to keep the built system at the N, K and P the analysis chose.

## Reading the matrix

- Long row: a stressor that spreads. Put boundaries in its path.
- Heavy column: overloaded or load-bearing part. Split it or isolate it.
- Two identical columns: one component with two names. Merge.
- Two 1s in one row: hyperliminal coupling. Separate them.
- Empty column: not stressed enough.
- Total marks ÷ components ≈ K. Aim to lower it, not to zero.

## Rules

- No probability, no cost, until the fault lines are known.
- Every stressor goes on the list, however odd. Dismissing one without a written response is sweeping it under the rug.
- Business stressors are thought experiments; technical ones may later become probes. Neither replaces the walk.
- Do not reuse a stressor list between projects, and do not treat the method as risk management, edge-case analysis or chaos engineering. Those check a chosen design; this chooses it.
- A person who operates the system is a component. Give them a column.

## Output

A short report: naive architecture, attractors with narratives, residues and deltas, the matrix with its readings, the trade-off list, the held-out score, and what the analysis could not reach. Attach the filled sheet.
