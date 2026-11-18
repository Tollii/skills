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
| [mock-roles-not-objects](skills/mock-roles-not-objects) | Design app-owned collaborator roles and focused interaction tests at adapters and third-party boundaries. |
| [residuality](skills/residuality) | Residual analysis after Barry O'Reilly's Residuality Theory: stress a naive architecture with random stressors, find attractors, keep what survives. |
| [contagion-analysis](skills/contagion-analysis) | Incidence- and dependency-matrix coupling analysis: where one external fact breaks several parts, and whether a change adds coupling. |
| [statechart](skills/statechart) | Check a TLA+ model of a system that moves between states and draw it as a Mermaid statechart, with the shortest path to each state that breaks a rule. Runs only when asked. |

### Testing

| Skill | What it does |
|---|---|
| [test-audit](skills/test-audit) | Keep tests that prove behavior and cut the rest: an authoring gate for new tests, junk patterns, a retention bar, and an evidence-first workflow for pruning a suite or a whole subsystem. |

### Observability

| Skill | What it does |
|---|---|
| [observability](skills/observability) | Set up or maintain OpenTelemetry tracing plus wide-event logging in a codebase, delivered as two pull requests. Run it again later as a maintenance pass. Runs only when asked. |
| [logging-best-practices](skills/logging-best-practices) | Logging built on wide events (canonical log lines): high-cardinality context, one logger, middleware-based setup. Runs only when asked. |

### UI and motion

| Skill | What it does |
|---|---|
| [make-interfaces-feel-better](skills/make-interfaces-feel-better) | Refine UI consistency, typography, surfaces, icons and feedback within an existing design system. |
| [motion-infografikk-film](skills/motion-infografikk-film) | Turn a script into an explainer or infographic motion film: one interactive HTML page with player, chapters and synced script, exported to MP4 with a sound bed. Two ready styles, Punktet and Tråden. Written in Norwegian. |

### Workflow

| Skill | What it does |
|---|---|
| [show-me](skills/show-me) | Explain the current topic with a diagram, code sketch or small HTML page when a visual helps. |

`shared/design-principles.md` is a general design reference some of these skills draw on.

## Credits and licenses

My own skills are MIT licensed (see [LICENSE](LICENSE)). These come from other people's MIT-licensed
work, most of them rewritten to fit how I use them; each folder keeps its original license:

- **make-interfaces-feel-better**: [jakubkrehel/make-interfaces-feel-better](https://github.com/jakubkrehel/make-interfaces-feel-better), © Jakub Krehel
- **statechart**: [pstack](https://github.com/cursor/plugins/tree/main/pstack) by Lauren Tan ([@poteto](https://x.com/poteto)), unchanged
- **test-audit**: [openclaw/openclaw](https://github.com/openclaw/openclaw/tree/main/.agents/skills/test-audit), © OpenClaw Foundation, rewritten to work in any project
- **logging-best-practices**: [boristane/agent-skills](https://github.com/boristane/agent-skills), © Boris Tane. `observability` embeds an unchanged copy in `vendor/`.
