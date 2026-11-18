# Skills

Agent skills I use with Claude Code, Codex and other coding agents. Each folder in `skills/` is one
skill: a `SKILL.md` plus any references or assets it reads.

Skills work best per project, not loaded globally into every session. Add the ones a project needs.

## Install

Copy a skill into a project:

```sh
git clone https://github.com/Tollii/skills
mkdir -p your-project/.agents/skills your-project/.claude/skills
cp -r skills/skills/type-design your-project/.agents/skills/
ln -s ../../.agents/skills/type-design your-project/.claude/skills/type-design   # Claude Code
```

Or use the [skills CLI](https://github.com/vercel-labs/skills), which lets you pick from the list:

```sh
npx skills add Tollii/skills
```

### As a plugin (Claude Code and Copilot CLI)

The `tollii` plugin installs every skill at once, named `tollii:<skill>`:

```text
/plugin install tollii --marketplace Tollii/skills
```

On older Claude Code versions, and in Copilot CLI, add the marketplace first:

```text
/plugin marketplace add Tollii/skills
/plugin install tollii@tollii
```

The plugin has no version, so `/plugin marketplace update tollii` pulls the latest `main`. Turn it off with
`/plugin disable tollii@tollii`, or for one project by setting `"tollii@tollii": false` under `enabledPlugins` in
its `.claude/settings.json`.

## Skills

### Design and architecture

| Skill | What it does |
|---|---|
| [type-design](skills/type-design) | Give each responsibility a type whose shape makes the wrong state impossible: lifecycles as discriminated unions, parse once at the boundary, typed failures. |
| [collaborator-roles](skills/collaborator-roles) | Design the interfaces a class depends on from what it needs, wrap vendor APIs behind them, and test against them with focused mocks. After *Mock Roles, Not Objects* (Freeman, Pryce, Mackinnon, Walnes 2004). |
| [code-design-review](skills/code-design-review) | Review a diff or module for code smells that carry real risk (double side effects, check-then-act races, changes spread across many files, behavior in the wrong place). Digs deeper with the matching skill and proposes the smallest fix, or a simpler, better-typed design when one exists. |
| [residuality](skills/residuality) | Residuality Theory after Barry O'Reilly: stress a naive architecture with random stressors and keep what survives, or run a quick contagion check (incidence matrix) on a design, codebase or MR to find where one external fact breaks several parts. |
| [statechart](skills/statechart) | Check a TLA+ model of a system that moves between states and draw it as a Mermaid statechart, with the shortest path to each state that breaks a rule. |

### Testing

| Skill | What it does |
|---|---|
| [test-audit](skills/test-audit) | Keep tests that prove behavior and cut the rest: an authoring gate for new tests, junk patterns, a retention bar, and an evidence-first workflow for pruning a suite or a whole subsystem. |

### Observability

| Skill | What it does |
|---|---|
| [observability](skills/observability) | OpenTelemetry tracing plus wide-event logging for servers, browsers, mobile and edge, with TypeScript, C# and Java examples. Sets it up, adopts an existing setup, runs maintenance passes, or helps with a single change. |

### UI and motion

| Skill | What it does |
|---|---|
| [make-interfaces-feel-better](skills/make-interfaces-feel-better) | Refine UI consistency, typography, surfaces, icons and feedback within an existing design system. |
| [motion-infografikk-film](skills/motion-infografikk-film) | Turn a script into an explainer or infographic motion film: one interactive HTML page with player, chapters and synced script, exported to MP4 with a sound bed. Two ready styles, Punktet and Tråden. Written in Norwegian. |

## Credits and licenses

My own skills are MIT licensed (see [LICENSE](LICENSE)). These come from other people's MIT-licensed
work, most of them rewritten to fit how I use them; each folder keeps its original license:

- **make-interfaces-feel-better**: [jakubkrehel/make-interfaces-feel-better](https://github.com/jakubkrehel/make-interfaces-feel-better), © Jakub Krehel
- **statechart**: [pstack](https://github.com/cursor/plugins/tree/main/pstack) by Lauren Tan ([@poteto](https://x.com/poteto)), unchanged
- **test-audit**: [openclaw/openclaw](https://github.com/openclaw/openclaw/tree/main/.agents/skills/test-audit), © OpenClaw Foundation, rewritten to work in any project
- **observability**: its logging rules are Boris Tane's [`logging-best-practices`](https://github.com/boristane/agent-skills/tree/main/skills/logging-best-practices) skill, © Boris Tane, embedded unchanged in `vendor/logging-best-practices`
