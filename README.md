# Continuous Coordination Skills

Skills for working with [Continuous Coordination](https://continuouscoordination.org) from AI-powered tools.

Continuous Coordination is a lightweight methodology for keeping teams, people, and AI agents in sync through two structured loops — team check-ins and recurring goal story updates. These skills help an assistant write code against a CoCo server and compose the updates that flow through those loops.

Each skill is a self-contained folder with a `SKILL.md` written to the [Agent Skills](https://agentskills.io) open standard — the same format Claude Code, the Claude apps, and OpenAI Codex all read. Drop a skill into your assistant's skills directory and it loads the right knowledge automatically when a task calls for it (or on demand when you invoke it by name).

## Available skills

| Skill | What it does | Use it when |
|-------|--------------|-------------|
| [`coco-api`](skills/coco-api/SKILL.md) | Write correct code against a [CoCo reference server](https://github.com/continuouscoordination/coco-server) — a PostgREST API over the six schema entities (actors, teams, team check-ins, goal stories, goal story updates, coordination events). Covers running the stack, the `localhost:2626` base URL, single-tenant/no-auth, PostgREST query conventions, the live OpenAPI spec, and create flows. | You're writing scripts, clients, or integrations that hit a self-hosted CoCo server. |
| [`coco-updates`](skills/coco-updates/SKILL.md) | Write clear, useful team check-ins and goal story updates — adding context beyond the bare activity, right length, unambiguous people references, scannable bullets — and submit them to **either a CoCo server or Steady** (with the field mapping between the two). This is the *what to write* skill; `coco-api` covers *how to submit* to a server. | You're drafting or polishing a check-in or goal story update (as a person, or as an agent reporting progress). |

> **Targeting [Steady](https://runsteady.com)?** Steady is the commercial Continuous Coordination platform — same methodology, a different authenticated API. `coco-updates` already covers writing content for Steady (and maps the fields). For *code* against Steady's API or CLI, use the separate [`steady-skills`](https://github.com/steadyspacecorp/steady-skills) (`steady-api`, `steady-cli`) instead of `coco-api`.

## Install

The same skill folders (under [`skills/`](skills/)) work across every tool below. The fastest paths are one command each; manual copy/upload still works where there's no CLI.

### Claude Code

Add this repo as a plugin marketplace, then install the bundled `coco-skills` plugin (both skills):

```
/plugin marketplace add continuouscoordination/coco-skills
/plugin install coco-skills
```

Claude loads each skill when relevant, or invoke one directly with `/coco-api`, `/coco-updates`. Update later with `/plugin update coco-skills`.

→ Full reference: [Extend Claude with skills](https://code.claude.com/docs/en/skills)

### Agent Skills hosts (Codex, Cursor, Copilot, Gemini CLI, Zed, …)

Any host that reads the open [Agent Skills](https://agentskills.io) format can install with the [`skills`](https://github.com/vercel-labs/skills) CLI — it discovers both skills under `skills/` and writes them to the right place for your agent:

```sh
# Personal (all repos) — drop -g for project-local (./<agent>/skills/)
npx skills add continuouscoordination/coco-skills -g

# — or — install a single skill by name
npx skills add continuouscoordination/coco-skills/coco-api -g
```

→ Full reference: [Agent Skills — Codex](https://developers.openai.com/codex/skills)

### Claude apps (web & desktop)

The apps install skills by **upload**, not the filesystem. First enable **Code Execution** and **File Creation** under Settings → Capabilities (skills require them; available on Pro, Max, Team, and Enterprise plans). Then upload each skill's `SKILL.md` under Settings → Features → Skills.

→ Step-by-step: [Use skills in Claude](https://support.claude.com/en/articles/12512180-use-skills-in-claude)

### Manual copy

Prefer the filesystem? Clone and copy the folders into any agent's skills directory:

```sh
git clone https://github.com/continuouscoordination/coco-skills.git

# Claude Code — personal or project
cp -R coco-skills/skills/coco-* ~/.claude/skills/

# Codex and other .agents hosts
cp -R coco-skills/skills/coco-* ~/.agents/skills/
```

## Ecosystem

- [Schema](https://github.com/continuouscoordination/coco-schema) — JSON Schema definitions for the six entities. Source of truth for shapes.
- [Server](https://github.com/continuouscoordination/coco-server) — runnable PostgREST reference implementation of the schema.
- [continuouscoordination.org](https://continuouscoordination.org) — the methodology.
- [Steady](https://runsteady.com) — commercial Continuous Coordination platform.

## License

MIT.
