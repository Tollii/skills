---
name: type-design
description: Use before writing a type, schema or contract with a `state`, `status` or `kind` field, or when state hides in optional fields, flags, sentinels or the same check repeated across callers. Gives each responsibility a type whose shape makes the wrong state impossible to write.
---

# Type design

A responsibility without a type has no place to live, so it spreads into every caller as a check, a flag or a null. This skill finds such responsibilities and gives each one a type whose shape makes the wrong state impossible to write. The most common case is a lifecycle: a value that moves through states, where each state knows different facts.

This is type-driven design in the sense of [Parse, don't validate](https://lexi-lambda.github.io/blog/2019/11/05/parse-don-t-validate/): a parser returns a type that keeps what it checked, so no caller checks again. Read a sequence of transitions like an F# computation expression: each step takes the previous step's variant and returns the next one or a typed failure, so no step runs on a value that has not passed the steps before it.

If the project has a glossary, principles or architecture docs, read them first. Name types and operations in the domain's vocabulary, not in the vocabulary of a pattern.

## Signs of a missing type

Look for these in types, schemas (zod, OpenAPI, Prisma, JSON Schema), parsers and the code that reads them:

- A `state`, `status`, `kind` or `resolution` field beside fields that only some of its values have, such as `status` with `endedAt`, `stopReason` and `pendingApproval`.
- Nullable fields that are always set or unset together: `completedAt`, `resolution`, `mergedAt`, `mergedBy`.
- A comment such as "null while open", "only set when …" or "absent until …".
- A status check followed by `?.`, `??` or `!` on a field of that state: `run.status === 'awaiting_approval' ? run.result?.pendingApproval : undefined`.
- A constructor or helper that fills several fields with `null` or `undefined` for one case.
- A schema refinement (zod `.refine`) that ties a status to another field. The parser proves the fact, and the inferred type forgets it, so callers check again or throw.
- A flag or sentinel that callers must interpret: `isValid`, `hasError`, an empty string for "none", `-1` for "unknown". A success flag beside optional data and error. A null that means both "absent" and "failed".
- The same check in three or more places. The check is protecting something; that something is the responsibility.
- A broad `try`/`catch` around a sequence, or an orchestrator that patches each step's failure. Failure ownership has no type.

## Procedure

1. **Name the responsibility.** Say in one sentence, in domain words, what the scattered code is responsible for, for example "how a case completed". If you cannot name it, keep reading the callers until you can.
2. **List the states and what each one knows.** Write a small table: state, the fields that state always has, the fields that are genuinely unknown in it. Read the writers, not only the type: the states are the ones the code actually produces.
3. **Write one variant per state.** Tag the union with the field that already exists (`state`, `status`, `kind`, `resolution`). Each variant carries only its own fields. A field stays optional inside a variant only when it is unknown in that state, such as who merged when the code host does not say. Use `z.discriminatedUnion` for zod and `oneOf` for OpenAPI; in other languages, a closed sum type (F# union, Rust enum, Kotlin/Java sealed type).
4. **Make each transition a gate.** A transition is a function from the variant it starts in to the variant it ends in. Code that needs a state's facts takes that variant as its parameter, so the only ways to obtain one are its transition and the parser of its stored form. Keep construction controlled: a public constructor or a cast defeats the invariant.
5. **Parse once at the boundary.** A database row, a webhook payload or a contract response may need nullable columns. Its parser turns them into the union, and the core never sees the nullables. A stronger type must be earned by the operation that establishes it: parsing syntax does not prove identity, permission or current database state. Recheck facts that can change or that cross another trust boundary.
   - **Shape, then meaning.** A schema or wire parser proves structure; a domain parser proves business meaning. Keep them as two steps when both exist, and keep wire contracts separate from domain types.
   - **Collect or stop.** Collect errors from independent fields so the caller can fix them all at once. Stop at the first failure when a later step depends on an earlier fact.
   - **Strengthen the argument.** A function that accepts broad input and rejects most of it inside should take the narrower, parsed type instead.
   - **Impossible history fails.** Replaying events or applying a transition from the wrong state returns an explicit invalid-transition outcome; it never silently skips.
6. **Give expected failures a type.** A function that can fail returns the project's `Result` type (or a discriminated outcome) with a named error. Derive messages from the failure; do not decide from message text.
7. **Give coordination one owner.** When an orchestrator sequences several steps, name it after what it coordinates. It owns sequencing, effects (time, randomness, I/O) and failure handling; the pure domain functions it calls own the decisions.

## Example

Before: one shape for every state, and every reader guesses.

```ts
type Problem = {
  state: 'open' | 'proposed' | 'merged';
  pullRequestUrl: string | null;
  mergedAt: string | null;
  mergedBy: string | null;
};
```

After: each state has its own facts, and the transitions are the only way forward.

```ts
type OpenProblem = { state: 'open'; caseId: CaseId; detectedAt: Date };
type ProposedFix = Omit<OpenProblem, 'state'> & { state: 'proposed'; pullRequestUrl: string };
/** `mergedBy` is null when the code host does not name who merged. */
type MergedFix = Omit<ProposedFix, 'state'> & { state: 'merged'; mergedAt: Date; mergedBy: string | null };
type Problem = OpenProblem | ProposedFix | MergedFix;

function proposeFix(problem: OpenProblem, pullRequestUrl: string): ProposedFix {
  return { ...problem, state: 'proposed', pullRequestUrl };
}

function recordMerge(fix: ProposedFix, merge: { at: Date; by: string | null }): MergedFix {
  return { ...fix, state: 'merged', mergedAt: merge.at, mergedBy: merge.by };
}

/** The stored row becomes a variant once, and a failure stops the sequence. */
function merged(row: ProblemRow, merge: Merge): Result<MergedFix, DomainError> {
  const fix = parseProposedFix(row);
  return fix.ok ? ok(recordMerge(fix.value, merge)) : fix;
}

/** Review time needs a merged fix, so it cannot be computed for an open problem. */
function reviewTime(fix: MergedFix, proposedAt: Date): number {
  return fix.mergedAt.getTime() - proposedAt.getTime();
}
```

For a multi-stage pipeline in a language with closed unions, see [the F# example](references/typed-pipeline.md). Translate the approach into the project's language; don't import a monadic framework or add classes just to imitate it.

## Guards and ripple effects

- If the project has a lint rule, contract check or baseline file for lifecycle bags, run it, and remove a site's baseline entry in the same change that fixes it.
- A contract change regenerates its clients (OpenAPI generators, shared packages); change them in the same merge request.
- A stored shape change needs a migration, or a parser that builds the union from the old rows.

## When not to add a type

- Nullable fields that are independent of each other, such as report figures that are null when a period has no data.
- Inputs: a patch, a request body, a query or a filter, where an optional field means "not given".
- One nullable fact that is not a state, such as when a completion was reopened.
- A wrapper or a pattern that adds a name without removing a check from any caller. A function or module is enough when it captures the whole responsibility.
- Branding every primitive. Give a value its own type when mixing it up with another is a real mistake.
- Style churn. Records, unions and pattern matching earn their place when they make intent easier to read and harder to misuse, not because the language has them.

For collaborator interfaces, adapters or brittle interaction tests, use `mock-roles-not-objects` instead: that skill covers relationships between components, this one covers internal facts and transitions.

## What to report

When reviewing or auditing, give for each finding: the responsibility in one sentence; the type and its location with every writer and reader; the states and their fields; the proposed union and transitions; which checks remain necessary (facts that can change); what changes in contracts, generated clients, storage, screens and tests; and the concrete harm, such as a guard that hides an impossible state, a cast, or two readers that disagree. Rank findings by harm and say which can ship as a small change and which need a design decision.

When implementing, change one responsibility per merge request and delete the checks, fallbacks and `?.` that the new type makes unnecessary. Those deletions are the evidence that the type is right.
