---
name: contagion-analysis
description: Incidence-matrix and dependency-matrix coupling analysis from Residuality Theory (O'Reilly's contagion analysis). Use to find where one external fact breaks several parts, which parts are overloaded, which are one part under two names, and whether a change adds coupling. Works on a design, an existing codebase, or an MR diff, with or without a full stressor walk.
---

# Contagion analysis

Contagion is a stressor crossing from one part of a system into another. This skill finds it with two small matrices and reads them with O'Reilly's heuristics. It is the second step of residual analysis (see the `residuality` skill), and it stands alone when the question is "where is this design coupled" or "does this change add coupling".

Fill `assets/incidence-matrix.xlsx` (sheets Incidence and DSM; totals and K are formulas) or reproduce the tables in markdown.

## Procedure

1. **Columns.** List the parts: flows of information, functions or components. Prefer flows and functions over deployment units. A person who operates the system is a part. Vendors you depend on (runtime, model provider, cloud preview services) are parts.
2. **Rows.** Stressors. With a residuality run, take them from the stressor sheet. Without one, write 20 to 40 fast: each assumption the design rests on, each dependency going away, each dependency lying or slowing, the business changing its mind, the operator being absent. No probability.
3. **Mark.** 1 where the stressor touches the part: it breaks, degrades, or needs a change. Sum rows and columns. K ≈ total marks ÷ parts.
4. **DSM (optional).** Parts × parts, 1 where the row depends on the column. Shapes: diagonal only is independent; a few off-diagonal marks is interdependent; dense is complex; full is Markov, where everything depends on everything.
5. **Read** (below) and **move boundaries**. Re-mark after each move. Stop when long rows have shortened, heavy columns have thinned and K has fallen without the DSM going diagonal-only (that is rigid, not critical).
6. **For an MR or diff.** Mark only the rows the change touches, before and after. A row that gained a mark is new coupling; name the external fact that now breaks one more part. A column that gained marks is a part that took on an assumption it did not have.

## Reading the matrix

- Long row: one stressor spreads through many parts. Put a boundary in its path so it stays inside one residue.
- Heavy column: an overloaded or load-bearing part. Split it, or isolate what makes it load-bearing.
- Two identical columns: one part with two names. Merge, or admit they fail together.
- Two 1s in one row: hyperliminal coupling, one external fact breaks both. Separate them or put the fact behind one of them.
- Empty column: not stressed enough, or genuinely independent. Add rows before believing the second.
- Marks ÷ parts ≈ K. Near 1 is rigid, large is chaotic, around 2 is the critical region. Use it as a trend across rounds, not a target.

## Output

The two tables, the readings as a short list (each naming the stressor, the parts, and the boundary move), and the K before and after. For an MR, one line per row that changed.
