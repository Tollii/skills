# Architecture design and responsibility boundaries

Use this reference for architecture assessment or when a change exposes unclear boundaries. It preserves the substantive design guidance from the former architecture skill without requiring its missing companion skills.

A module is useful when its interface lets a caller do meaningful work without understanding the complexity inside it. A small file with an equally complicated interface can remain difficult to use. Judge the knowledge required at call sites, not file size alone.

## Process

### 1. Explore

**Scope before you scan — YAGNI.** Deepening a module pays off by making future changes to it easier, so put extra weight on the parts of the codebase that have recently changed. Decide _where_ to look before you look:

- If the user named a direction — a module, a subsystem, a pain point — take it, and skip the inference below.
- Otherwise, walk back a good stretch of the commit history (`git log --oneline`) to find the codebase's hot spots — the files and areas that keep coming up — and let those paths pull your attention first. If the changes are scattered with no clear hot spot, widen the net.

Read existing domain terminology and relevant architecture decisions. Use the project's document names; do not require CONTEXT.md when the project keeps this information elsewhere.

Explore the relevant code and note where understanding becomes difficult:

- Where does understanding one concept require bouncing between many small modules?
- Where are modules **shallow** — interface nearly as complex as the implementation?
- Where have pure functions been extracted just for testability, but the real bugs hide in how they're called (no **locality**)?
- Where do tightly-coupled modules leak across their seams?
- Which parts of the codebase are untested, or hard to test through their current interface?

Apply the **deletion test** to anything you suspect is shallow: would deleting it concentrate complexity, or just move it? A "yes, concentrates" is the signal you want.

## Test surfaces and collaboration

Check whether tests exercise the interface that owns the behavior. Extracting pure calculations can help, but it does not replace tests of the orchestration where failures occur. Keep tightly coupled decisions near their owner and make external collaboration explicit. A possible second implementation is not evidence that a general framework is needed; inspect actual variation before designing for it.

Apply the deletion question in both directions: would removing a wrapper eliminate indirection, or force callers to duplicate policy and understand hidden details? The answer determines whether to remove the boundary or give it a more complete responsibility.

## Compare candidates

For each substantial candidate, identify the affected files, concrete problem, proposed boundary, change in caller knowledge, expected test improvements, and confidence. Use a small before/after diagram when it explains the change. HTML is optional; choose the format the user requested.

Compare alternative interfaces when the choice matters. Explain what each hides, what it exposes, and how it changes required tests. Do not create several implementations merely to satisfy a procedure.

Respect existing architecture decisions. Revisit one only when new evidence shows a real cost; identify that evidence and the tradeoff. Record durable domain terminology and consequential decisions in the existing documents when the task calls for it. Preserve the reason for a rejected proposal when it prevents repeated work. Do not require an interview or a separate domain-modeling skill to make ordinary progress.
