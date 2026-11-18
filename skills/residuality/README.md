# residuality

An agent skill for designing and checking software architecture with Barry O'Reilly's [Residuality Theory](https://www.jfokus.se/jfokus22-preso/Residuality-Theory.pdf). It merges the former `residuality` and `contagion-analysis` skills.

## What it is

Most design methods try to predict what will happen and plan for it. Residuality does the opposite. You throw many unranked "what ifs" (**stressors**) at a first, naive design, and for each one ask what is left standing (the **residue**) and what it would take to keep more of it working. Stressors cluster into a few states the business keeps falling into (**attractors**), so fixing the design for one stressor quietly covers many you never listed. The architecture is what survives.

The second half is **contagion analysis**: a small matrix of stressors against the parts of the system. It shows where one external fact breaks several parts at once, which parts carry too much, and which two parts are really one. That half is useful on its own, on any design, codebase or merge request.

## Two ways to use it

| | Full residual analysis | Contagion check |
|---|---|---|
| **Use it for** | A new system, or a major redesign | "Where is this coupled?" on a design, a module or one MR |
| **Effort** | Hours: 150–250 stressors, plus a stakeholder conversation | Minutes: 20–40 stressors, or only the rows a diff touches |
| **You get** | Attractors, residues, the matrix, trade-offs to decide, and a held-out score showing whether the design generalises | The matrix, a list of boundary moves, and a coupling number (K) before and after |
| **Files** | `assets/stressor-sheet.xlsx` and `assets/incidence-matrix.xlsx` | `assets/incidence-matrix.xlsx` |

## Example prompts

- "Run a residual analysis on this design doc."
- "Burn down the architecture in `docs/architecture.md`. Stakeholders are ops and finance."
- "Run a contagion check on this MR. Does it add coupling?"
- "Where is `src/billing` coupled? Use contagion analysis."

If you don't say which mode, the agent uses the contagion check for code and MRs and the full analysis for designs, and tells you which it picked.

## Reading the result

- **Long row:** one "what if" breaks many parts. A boundary is missing.
- **Heavy column:** a part too much depends on. Split or isolate it.
- **Two identical columns:** one part with two names.
- **K (marks ÷ parts):** about 1 is rigid, large is chaotic, about 2 is the healthy middle. Watch the trend, not the number.

## Files

- `SKILL.md`: what the agent follows.
- `references/contagion.md`: the matrix method, also used on its own.
- `references/theory.md`: definitions and reasoning, with quotations.
- `references/sources.md`: primary and secondary sources.
- `assets/`: the stressor sheet and the incidence matrix, as spreadsheets.
