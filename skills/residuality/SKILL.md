---
name: residuality
description: "Residuality Theory (Barry O'Reilly). Use to stress-test an architecture with stressors and keep what survives, or for a quick contagion check: an incidence matrix of stressors against parts that shows where a design, codebase or PR (or MR) is coupled and whether a change adds coupling. Not a general code review, and not for drawing module boundaries from nothing; it stresses a design that already exists."
---

# Residuality

Design by subtraction: hit a first design with stressors you never rank by likelihood, note what is left each time, strengthen that, then fold the survivors into one structure where no stressor crosses from one residue into another. The architecture is what remains. Correctness is the programmer's goal; the architect's goal is criticality: a structure that can move when the context changes.

## Pick the mode

- **Full residual analysis** for a new system or a major redesign: the [procedure](#procedure) below. Hours of work and a stakeholder conversation.
- **Contagion check** for "where is this coupled?" on a design, a codebase or one PR: go straight to [references/contagion.md](references/contagion.md). Minutes of work, 20 to 40 stressors or only the rows a diff touches.

If the request doesn't say which, use the contagion check for code and PRs and the full analysis for designs, and say which you chose.

Read [references/theory.md](references/theory.md) once per session for the definitions and the reasoning. [references/sources.md](references/sources.md) has the primary material. Assets: `assets/stressor-sheet.xlsx` (or the CSV twin) for the full analysis, `assets/incidence-matrix.xlsx` for the matrices.

## Terms

- **Stressor:** any fact about the context that is currently unknown. Failures, but also market, vendor, regulatory, people and absurd events. Never carries a probability.
- **Attractor:** a state the business keeps falling into under stress. Many stressors lead to one attractor; one stressor is enough to find it.
- **Residue:** what is left of the system (people, flows, functions, infrastructure) after a stressor, plus the augmentation that lets it survive there.
- **Delta:** the change the augmentation makes to the naive design.
- **Contagion:** a stressor crossing a residual boundary and breaking another residue. Hyperliminal coupling is two parts that one external fact breaks at once.

## Procedure

1. **Naive architecture.** The first design that solves the functional problem, as flows between actors. A hypothesis, not a draft to defend. Write down the assumptions it rests on; each is a stressor.
2. **Stressors.** Aim for 150 to 250 rows from at least five sources: assumptions ("what if this is wrong"), ordinary failures, the business model (the richest source), regulation, vendors, people, and at least ten absurd ones. Take them to stakeholders and record how they would respond. Columns: Stressor, Impacts the ability to (a business capability), Detection, Mitigation (business), Technical mitigation. Write a response for every row, even one you would never build. When a new row is already covered by earlier rows, note it: that is convergence.
3. **Attractors.** Group rows by the state they push the business into. Two-line narrative per attractor. Effort is per attractor; coverage is per stressor.
4. **Residues and deltas.** Per attractor: what survives, and what is needed so the largest possible part keeps working. Expect overlap and repetition.
5. **Contagion analysis.** Follow [references/contagion.md](references/contagion.md) with the stressors from step 2 as rows. Then boil the residues into as few components as possible without letting one stressor cross a residual boundary. List the trade-offs; each needs one decision.
6. **Held-out test.** Write 15 to 30 new stressors before looking at the residues again. Score naive versus residual. Residual index = (residual survivors − naive survivors) ÷ count. Positive means the residues generalise; near zero across rounds means stop.
7. **Discard the stressors.** Keep the attractors, the matrix, the trade-off list and the index. Normal risk management, FMEA, ATAM, tests and telemetry come after, to keep the built system at the N, K and P the analysis chose.

## Rules

- No probability, no cost, until the fault lines are known.
- Every stressor goes on the list, however odd. Dismissing one without a written response is sweeping it under the rug.
- Business stressors are thought experiments; technical ones may later become probes. Neither replaces the walk.
- Do not reuse a stressor list between projects, and do not treat the method as risk management, edge-case analysis or chaos engineering. Those check a chosen design; this chooses it.
- A person who operates the system is a component. Give them a column.

## Output

- **Full analysis:** a short report with the naive architecture, attractors with narratives, residues and deltas, the matrix with its readings, the trade-off list, the held-out score, and what the analysis could not reach. Attach the filled sheets.
- **Contagion check:** the output described in [references/contagion.md](references/contagion.md#output).
