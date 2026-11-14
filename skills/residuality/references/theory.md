# Residuality Theory: definitions and reasoning

Quotations are Barry O'Reilly's, from his Jfokus 2022 slides, his collected Cutter articles (2018–2021) and his book *Residues* (2025) via reader notes. See `sources.md`.

## The claim

"Resilience is a function of what is left over, not what is done reactively." Design proceeds "via negativa (arriving at answers by taking away that which is demonstrably wrong). Processes and components emerge when risks for stressors and the resulting contagion are taken away." Hence: "architectures should be trained, not designed."

## Hyperliminality

Software is ordered: constrained, predictable from its past, testable. Business, markets, vendors and people are disordered: unpredictable, changing patterns or none. A hyperliminal system is an ordered system inside a disordered one. Architecture is the engineering of that boundary. Requirements, risk registers and tests work on the ordered side; residuality works on the boundary.

## Stressor

"Any fact about the context that is currently unknown to you." Three sources: technical (component failure, a third party withdrawing), business model (costs, partners, competitors, regulation), architectural change. "The richest source of stress is in the business model." Rules: no probability; every stressor on the list; use many sources; a typical project yields 200 to 250. The training exercise: describe the residue when "gigantic fire-breathing lizards" destroy the headquarters city, because "any residue strengthened to resist the lizards becomes resilient in the face of war, flooding, fire, terrorist attack, or even pandemic."

## Attractor

From Kauffman's Boolean networks: N parts with K links each do not visit all 2^N states; they fall into a few repeating cycles. "In his 100,000-element example, the states drop from 2^100,000 to 317." Regimes: K≈1 ordered and rigid; K large chaotic, attractors exponential in N; K≈2 critical, attractors about √N, perturbations absorbed yet the system can still move. P is the predictability of each part; "in software, P is raised by restricting how components interact with the rest of the system." Levers: K (coupling) and P (constraint). "Each attractor represents a set of stressors. You only need to find one stressor to find the attractor. You can't find them all, but each one you find improves the quality of the architecture." Attractors are states of the business, found "often through conversations with domain experts."

Why random stressors suffice: stressors vastly outnumber attractors; every walked stressor lands in one; enough random walks sample the attractor space; fixing the design for one attractor covers every stressor that leads there, listed or not. This is a sampling claim, so "it's not a silver bullet."

## Residue

"A residue is a collection of people, software functions, and the flows of information between them. It is what we imagine to be left of the system when it is impacted by a particular stressor." "Each time the business system moves to another attractor the architecture will usually fail. But some part of the architecture will survive. This is the residue." "A residue is a fuzzy view of the system under a particular form of stress." Then: "For every stressor, a residue is created and augmented to be better able to survive the stressor." The question per residue: "What is needed here to make sure that the system is still working, or that the largest possible part of the system is still working?" "Flooding destroys the basement, but the upper floors are OK. Fire destroys the entire building, but the fireproof safes are OK."

"An architecture is then a collection of residues, one for each attractor that we are aware of. We have to find the residues and then integrate them." "Fragile systems will have very few residues, and those will mostly look the same, with widespread contagion in the event of stress."

## Residual analysis: two steps

"Stressor analysis (random simulation)" and "contagion analysis (network analysis)". Contagion analysis: "deciding how to boil the residues down into as few components as possible, whilst retaining their ability to survive the stressors. We need to prevent contagion when we integrate: one stressor cannot cross a residual boundary and cause issues for another residue's ability to meet stress. Here we will produce a list of trade-off decisions to be made." Integration decides "which functions should be general and which should remain isolated inside the residue to prevent contagion." Parnas: decompose by what is likely to change, so a stressor stays inside one module; residuality finds what is likely to change without predicting it.

Tools: the incidence matrix (stressors × flows/functions/components) and the design structure matrix (components × components; shapes: independent, interdependent, complex, Markov). FMEA and ATAM for the trade-offs.

## Empirical test

Hold out stressors not used in design, "analogous to the training and testing set in machine learning." Residual index = (stressors the residual design survives − stressors the naive design survives) ÷ total. "If you get a positive number you're on the right track." Vary the order and chain stressors ("bagging and boosting, changing the order of impact"). Then "discard the stressors afterward"; they only had to reveal fault lines.

## Cost

"Moving component boundaries is a design change, not an expensive one." About "a couple of hours for about 200 stressors." Ignoring a stressor is fine if the response is written down: "don't sweep it under the rug."

## Not the same as

Risk management (ranks known risks by probability; residuality bans probability and designs structure first). Threat modelling (one-to-one mitigation). Edge cases (assume the structure is right; residuality "destroys the structure from within"). Chaos engineering and fault injection (check a built system for faults with an injector; "you cannot force a competitor to cut prices"). Checklists and best practices (someone else's residues for someone else's attractors).

## Mistakes

Trying to be correct. Treating stressors as predictions. Dismissing a stressor as unlikely. A fixed stressor list. Stopping at technical stressors. Being unable to say why the design has the components it has.
