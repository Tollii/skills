---
name: collaborator-roles
description: "Design the interfaces a class depends on from what it needs, and mock those roles, not concrete objects. Use when wrapping a vendor SDK, framework or runtime API behind an interface you own; when mocks are brittle, deeply nested or pin call order; or when a class creates its own dependencies and is hard to test. Not for pure functions or value objects, for whether a test is worth keeping (test-audit), or for state inside one type (type-design)."
---

# Collaborator roles

Based on [*Mock Roles, Not Objects*](https://www.jmock.org/oopsla2004.pdf) by Steve Freeman, Nat Pryce, Tim Mackinnon and Joe Walnes (OOPSLA 2004).

Use this skill to treat interaction testing as a design tool. Start from the object under test, identify the services it needs from immediate neighbours, and let those needs define small app-owned role interfaces. The goal is not to maximize mocks; it is to discover better seams, clearer collaborations, and tests that fail for the right reasons.

## Use this skill when

- Designing a seam, adapter, wrapper, or gateway around a vendor SDK, runtime API, or framework type
- Writing or reviewing tests for services, handlers, orchestrators, controllers, or other collaboration-heavy code
- Interaction tests are brittle because they over-specify order, call counts, or internal chatter
- A unit needs many collaborators and you need to discover narrower roles from its actual needs
- Code creates dependencies internally with `new`, static calls, or direct framework access and is hard to test
- The user explicitly asks about mock objects as a design aid, interface discovery, or mock-heavy .NET or TypeScript code

## Do not use this skill when

- The object is a value object, parser, mapper, pure function, or boundary object with no interesting collaborators; prefer state or value assertions
- The main problem is internal invariants, flags, nulls, or state modeling; use `type-design`
- You are mocking third-party types only to avoid integration cost; first design an app-owned seam you can actually change

## Core ideas

1. **Mock roles, not concrete objects.** Name collaborators by the role the object under test needs, not by a concrete class or library.
2. **Use tests for interface discovery.** Let the needs of the current object pull narrow outgoing interfaces into existence.
3. **Prefer Tell, Don't Ask.** Test meaningful messages to immediate neighbours instead of getter chains and train-wreck navigation.
4. **Stub queries, expect commands.** Give the object the answers it needs, and verify the meaningful outgoing effects it should trigger.
5. **Only mock types you own.** Wrap vendor, runtime, and framework APIs behind thin application-owned interfaces before mocking.
6. **Specify as little as possible.** Exact order and exact call counts matter only when the requirement depends on them.
7. **Too many mocks is design feedback.** Complex setup, mock networks, or smart mocks usually mean a missing role or a too-large object.
8. **Composition beats hidden construction.** Pass collaborators in, or introduce a factory when creation itself matters.

## Workflow

1. **Name the target role.** State the object under test and the service it provides.
2. **List required neighbours.** Identify the immediate collaborators it needs, phrased as role names.
3. **Draft the narrow interfaces.** Define only the commands and queries required by this scenario.
4. **Write the interaction test.**
   - Call the object under test through its public API.
   - Stub collaborator queries the object depends on.
   - Expect collaborator commands that represent success.
   - Assert ordering only if the behaviour truly requires it.
5. **Implement by composition.** Inject collaborators or introduce owned seams and factories instead of reaching directly into infrastructure.
6. **Refactor from the test feedback.**
   - Too many mocks -> split responsibilities or add an intermediate role.
   - Mocking foreign APIs -> add a wrapper you own.
   - Smart mock behaviour -> move logic into a real collaborator or the object under test.
7. **Choose the right assertion style.** For collaboration-heavy objects, verify interactions. For value and boundary objects, prefer state and value assertions.

## Anti-patterns to flag

- Directly mocking framework or vendor types in domain tests
- Deep mock graphs that mirror object structure instead of role boundaries
- Tests that assert every incidental call instead of only the required interaction
- Getter-driven code that reaches through collaborators to manipulate internals elsewhere
- Mocks with real parsing, routing, or business logic hidden inside them
- Objects that instantiate their own dependencies so interactions cannot be observed cleanly

## Output expectations

When applying this skill:

- Identify the object under test and the collaborator roles it needs
- Propose the smallest application-owned interfaces that satisfy the scenario
- Distinguish stubbed queries from expected commands
- Call out any wrapper or factory needed to stop mocking foreign or internally constructed dependencies
- Say whether the test should be interaction-based, state-based, or mixed
- Weaken overspecified expectations unless the requirement truly depends on them

## Reference map

Read only what you need:

- `references/principles.md` -> distilled paper ideas, review questions, and smell-to-fix guidance
- `references/dotnet.md` -> .NET examples for seam discovery and interaction tests
- `references/typescript.md` -> TypeScript examples for app-owned ports and collaborator-first tests

## Pairing with other skills

- Use `type-design` for internal invariants, state transitions, and semantic value types
- Use this skill for collaborator protocols, seams, wrappers, and interaction-test design
- Use both when interface discovery exposes missing domain types or when brittle tests are really a symptom of weak domain boundaries
