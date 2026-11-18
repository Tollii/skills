# Distilled principles from *Mock Roles, not Objects*

Use this file when you need the ideas of [*Mock Roles, Not Objects*](https://www.jmock.org/oopsla2004.pdf) (Freeman, Pryce, Mackinnon and Walnes, 2004) in portable form rather than jMock-specific syntax.

## What to carry forward from the paper

1. **Interaction tests are a design tool.** The paper treats test-first work as design-by-discovery. Mock-based tests are valuable because they reveal the outgoing interfaces an object needs from its environment.
2. **Need-driven development beats interface-first speculation.** Let the current object pull narrow roles into existence instead of designing wide provider interfaces up front.
3. **Collaborators are roles.** The point is not to imitate concrete classes. The point is to discover the role a neighbour plays in this scenario.
4. **Tell, Don't Ask supports better seams.** Prefer messages to immediate neighbours over getter chains and structural navigation.
5. **Composition follows naturally.** Once collaborators are passed in or abstracted behind roles, designs tend toward smaller focused objects and flatter structures.

## Heuristics to apply

### Mock only types you own

- Do not build tests around direct mocks of framework, runtime, or vendor types.
- First introduce a thin application-owned seam, then mock that seam.
- Thin wrappers are part of the design, not just test scaffolding.

### Only mock immediate neighbours

- If a test needs a whole graph of mocks, the object under test is probably reaching too far.
- Add an intermediate role, or move behaviour so the object only talks to its nearest collaborators.

### Specify as little as possible

- Verify order only when order is itself part of the requirement.
- Verify exact counts only when the count matters.
- If a test breaks every time you refactor internals, it is specifying implementation chatter, not behaviour.

### Be explicit about what must not happen

- Omitted expectations are ambiguous.
- If a collaborator must not be called, say so explicitly in the test or review notes.

### Do not add real behaviour to mocks

- If a mock has to parse inputs, branch on business rules, or manufacture complex behaviour, you have probably hidden a missing object inside the test.
- Move that behaviour into a real collaborator or a new role.

### Use interaction tests selectively

- If an object has no important relationships to neighbours, prefer state or value assertions.
- Value objects, parsers, mappers, and independent calculations usually do not need interaction tests.

### Hidden construction is a design smell

- If the object under test creates dependencies internally, interaction testing becomes awkward for a reason.
- Inject the instance, inject a factory, or add a small application-owned creation seam.

## Practical test-writing sequence

The paper's practical rhythm is:

1. Identify the object under test and call its public method.
2. Describe the services it needs from the rest of the system.
3. Represent those services as mocks or fakes for the test.
4. Build the rest of the test context.
5. Add postconditions only if the scenario needs them.
6. Verify the expected interactions.

In practice, that means:

- **Stub queries** that return information the object needs.
- **Expect commands** that represent meaningful effects on neighbours.
- Keep postconditions small when the interesting behaviour is the interaction itself.

## Review questions

Ask these when designing, reviewing, or debugging:

1. What role does this collaborator play for the object under test?
2. Is this interface narrow because of a real need, or wide because it mirrors a concrete dependency?
3. Are we mocking something we do not own?
4. Are we verifying a real requirement, or incidental implementation chatter?
5. If this test needs many mocks, what missing role or misallocated responsibility is it pointing at?
6. Should this be an interaction test at all, or would a state/value assertion be clearer?

## Smells and likely fixes

| Smell | Likely issue | Likely fix |
|---|---|---|
| Mocking `HttpClient`, SDK clients, database contexts, or framework objects directly | The seam belongs to somebody else | Introduce an application-owned wrapper or gateway |
| A test needs five or more collaborators | The object under test is too large or missing an intermediate role | Split responsibilities or add a bridging role |
| Mock expectations mirror every internal call | Test is overspecified and brittle | Keep only behaviourally meaningful expectations |
| Mock has real branching or parsing logic | Missing production object hidden inside test | Extract a real collaborator |
| Object creates dependencies with `new` or static calls | Interactions cannot be observed or controlled cleanly | Inject dependency or inject a factory |

## Skill boundary

- Use this skill for collaborator roles, outgoing interfaces, seams, wrappers, and interaction tests.
- Use `type-design` for value types, state transitions, invariants, and domain parsing.
- Use both when collaborator discovery reveals a domain concept that deserves its own type.
