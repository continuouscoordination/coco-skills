---
name: coco-updates
description: Use when writing, drafting, improving, or reviewing a Continuous Coordination team check-in or goal story update — whether as a writing aid for a person or when an agent composes one autonomously. Covers what makes an update clear and useful — adding context beyond the bare activity, right length, unambiguous people references, scannable bullets — and how to submit it to either a CoCo server or Steady. Trigger when the user asks to write/draft/polish a check-in, a "previously"/"next"/"blockers" update, a goal story update, a progress story, or a standup-style update — or when an agent needs to report progress into a CoCo server or Steady.
---

# Writing great Continuous Coordination updates

A good update gives teammates **signal**, not a transcript. The whole test is clarity: would a teammate skimming this understand what happened, what's next, and what's at risk — without asking a follow-up question? Add words only when they add clarity; never pad to hit a length, never truncate to fit one. And be straight: name real blockers and risks plainly, and write only what you actually know — don't manufacture a rationale or a status you don't have.

This skill is about *what to write*. The **Submitting** section at the end covers *how* — to a CoCo server (via the `coco-api` skill) or to Steady (via `steady-api` / `steady-cli` / the Steady MCP server).

## Two formats

Continuous Coordination runs on two structured loops:

- **Team check-in** — periodic, tight. Three fields, each a list of bullets:
  - `previously` — what you did since last time
  - `next` — what you're doing next
  - `blockers` — what's in your way
- **Goal story update** — a story about a goal's progress. Fields:
  - `title` — the milestone in a phrase
  - `content` — the narrative
  - `status` — `on_track` / `at_risk` / `off_track` / `completed` / `paused`
  - `progress_percent` — 0–100

A check-in is scannable bullets. A goal story update is expected to run longer, and its structure matters as much as its length (see "Long-form updates"). Longer is fine *when it's all signal*.

## Anti-patterns

### 1. Restating activity without adding context
Putting activity into an update is fine — often better than leaving it out. A captured event (a merged PR, a closed ticket) is **pull**: a teammate has to go find it. Putting it in your update is **push**: you proactively make people aware. So pushing the important stuff is worth doing.

The anti-pattern is pushing it **bare** — a bare reference makes the reader drill down for the point. Always carry the context with it, so the answer is right there.

> ✗ "Merged PR #2345." · "Worked on issue #ABC-123." · "Had a meeting with Sarah."
> ✓ "Merged PR #2345, which overhauls form validation across the key app views."
> ✓ "Spiked deferring the support widget until first interaction — a smaller lift than expected since we already had custom triggers."
> ✓ "Closed #ABC-123. Root cause was a stale cache, so I added a guard to invalidate it on save."

### 2. Length that isn't scannable
Each check-in bullet should be **1–3 sentences** carrying one idea. Lead with the outcome, not the play-by-play. (Goal story updates may be longer — but the same rule applies per bullet, and every sentence still has to earn its place.)

### 3. Ambiguous people references
A first name with no handle and no surname is a dead end for the reader. Use a **full name + context**, or an **@mention** if the target platform links them.

> ✗ "Got feedback from the designer." · "Waiting on approval from John."
> ✓ "Got feedback from Maria Chen (design)." · "Waiting on approval from John Okafor (finance)."

### 4. Prose where bullets belong
Any enumerable set — multiple tasks, next actions, blockers, dependencies — reads better as bullets than a run-on paragraph. On a CoCo server the check-in fields *are* arrays (one element per bullet), so this is structural, not just stylistic. Reserve prose for a single throughline (often the opening frame of a goal story update).

### 5. Bare links instead of contextual ones
Anchor the link on the words that describe it, so the meaningful phrase is what's clickable — don't write it out in plain text and tack a separate link on the end.

> ✗ "Worked on a PR to add action items to the API. [link]"
> ✓ "Worked on a PR to [add action items to the API](https://github.com/...)."

## A good team check-in, end to end

```
previously:
- Added CSV export, rolling it out behind a flag. It streams instead of buffering, which keeps memory flat on large accounts.
- Tracked down the flaky checkout test — a timezone assumption in the test itself, not a problem with the payment code.
- Synced with Maria Chen (design) on billing edge cases. We're deferring proration to next cycle so it doesn't block launch.

next:
- Roll the export flag out to 10% and watch error rates.
- Start on webhook retries. Writing a short spec before any code.

blockers:
- Waiting on Sarah Lin (PM) to sign off on the proration deferral before I can close the ticket.
```

## Long-form updates: inverted pyramid

A goal story update should follow the **inverted pyramid**: lead with the bottom line, then the supporting detail, then the background last. State the outcome up front — don't build up to it. A reader who stops after the first line should still walk away with the headline; a reader who wants the full story keeps going.

```
title: Self-serve onboarding is live
status: on_track
progress_percent: 70

content:
New accounts now finish setup without a call, and day-one activation is up from
48% to 61% in the first week.

[…the rest develops the story: what changed and why, the decisions behind it,
what's still open, and what's next…]
```

Make `status` and `progress_percent` match reality — a confident `content` next to `at_risk` reads as either careless or evasive.

## Quick check before sending

- Does every line carry its own context — the point answered without drilling down?
- Could a teammate skim it in 10 seconds and know what's next + what's blocked?
- Is every person a full name + context (or an @mention where the platform links it)?
- Are enumerable items split into separate bullets / array elements?
- (Goal story update) Does it lead with the bottom line, and do `status` + `progress_percent` match reality?

## Submitting

The same written content can go to a self-hosted **CoCo server** or to **Steady** — the two implement the same methodology but name the fields differently. Write once; map at submit time.

### Field mapping

| Concept | CoCo server | Steady |
|---|---|---|
| Check-in: what I did | `previously` (array) | `previous` (Markdown) |
| Check-in: what's next | `next` (array) | `intentions` (Markdown) |
| Check-in: blockers | `blockers` (array) | `blockers` (Markdown) |
| Check-in: mood | *(none)* | `mood` (enum) |
| Goal update: headline | `title` | `title` |
| Goal update: narrative | `content` | `body` (Markdown) |
| Goal update: health | `status` (`on_track`/`at_risk`/`off_track`/`completed`/`paused`) | `confidence` (90 / 60 / 30 = on track / at risk / off track) |
| Goal update: progress | `progress_percent` (0–100) | `progress` (0–100) |

Two things to watch when crossing platforms:

- **Arrays vs Markdown.** CoCo check-in fields are arrays — one bullet per element (`["…", "…"]`). Steady's are single Markdown strings — join bullets with newlines (`- …\n- …`). The *content* is the same; the container differs.
- **Health: enum vs number.** CoCo's `status` is a named enum; Steady's `confidence` snaps to 30/60/90. `on_track`→90, `at_risk`→60, `off_track`→30. Steady has no native equivalent for `completed`/`paused` — convey those in the body and set `progress`/`confidence` accordingly (e.g. completed → `progress: 100`).
- **People references.** Steady links `@username` mentions and notifies them; a self-hosted CoCo server generally won't, so prefer full name + context there. When in doubt, full name + context is safe on both.

### Submit to a CoCo server

`POST /team_checkins` or `POST /goal_story_updates` on the server. See the **`coco-api`** skill for the base URL, PostgREST conventions, and exact request shapes. The server auto-creates the matching coordination event — you don't post that.

### Submit to Steady

Use the **`steady-api`** or **`steady-cli`** skill, or the Steady MCP server. Note Steady's check-ins are PATCH-only (pre-generated, addressed by date), and its goal updates default `progress`/`confidence` to the prior update's values — see `steady-api` for those quirks.

Those skills live in a separate repo — [`steadyspacecorp/steady-skills`](https://github.com/steadyspacecorp/steady-skills). If they aren't installed:

```
# Claude Code
/plugin marketplace add steadyspacecorp/steady-skills
/plugin install steady-skills

# Agent Skills hosts (Codex, Cursor, …)
npx skills add steadyspacecorp/steady-skills -g
```

The [Steady MCP server](https://runsteady.com/mcp) is an alternative when you'd rather Claude write to Steady through tool calls than generate API/CLI code.
