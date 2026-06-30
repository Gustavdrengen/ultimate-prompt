# Autonomous Coding Agent Prompts

Three system prompts for vision-driven software projects: a vision interview that produces `VISION.md`, a setup prompt that bootstraps a new project from a vision, and a refresh prompt that brings an existing codebase up to the same standard.

## What this is

`VISION.md` is the single user-owned source of truth for a project — its purpose, users, features, constraints, exclusions, and success criteria. The agent owns everything else (structure, standards, tooling, specs, tests, docs) and works from the vision rather than from a stream of narrow tickets.

The three prompts are stages in this workflow:

1. **Interview** — produces `VISION.md`.
2. **Setup** — bootstraps a new project from a vision.
3. **Refresh** — brings an existing codebase up to standard.

All three enforce the same discipline: the agent never presents plans or asks for approval; it works from `VISION.md`, the cross-cutting channels, and the state of the product itself. If it spots a hole in the vision, it surfaces it and keeps working under a stated assumption rather than stopping to ask.

## How to use it

The intended workflow runs the three prompts on the **same project directory**, one after the other.

1. **Vision first.** Open the empty project directory in a coding agent and paste `vision-interview-prompt.md`. The agent will interview you, then write `VISION.md` to the project root using the Write tool. Stop the agent when the file has been written; that is its only output.

2. **Bootstrap (new project).** Still in the same directory, paste `setup-prompt.md` into a coding agent. The agent reads the existing `VISION.md`, creates `AGENTS.md`, scaffolds the project structure, sets up tooling and standards, establishes the cross-cutting channels (`INBOX.md`, `BLOCKED.md`, `STATE_OF_PLAY.md`), and starts working on the product.

3. **Maintain (or refresh).** As the project grows, the same agent keeps working from `VISION.md`, the state-of-play log, and `INBOX.md`/`BLOCKED.md`. If the workflow ever drifts — conventions decay, the agent stops reading `VISION.md`, vision holes accumulate, tests stop pinning behavior — paste `refresh-prompt.md` into the same agent to retrofit the codebase back to standard.

If `VISION.md` is missing when any of the prompts run, the agent writes a `BLOCKED.md` entry telling you to produce it and continues with the parts of work that do not depend on the vision. The next session, after you land `VISION.md`, picks up the rest.

In short: **`vision-interview-prompt.md` produces `VISION.md`. `setup-prompt.md` consumes an existing `VISION.md` to bootstrap a new project. `refresh-prompt.md` consumes an existing `VISION.md` to retrofit discipline back into an existing project.**

## When to use which prompt

| Situation | Paste |
|---|---|
| Brand-new or empty project directory | `vision-interview-prompt.md` first, then `setup-prompt.md` |
| Existing codebase that was never set up with these prompts | `refresh-prompt.md` |
| Existing codebase whose vision-driven workflow has drifted | `refresh-prompt.md` |
| Any time you'd otherwise ask the agent a bunch of questions about what to build | Update `VISION.md`, then let the agent `get to work` |

## Files

- `vision-interview-prompt.md` — for a coding agent in an empty project: interviews the user and writes `VISION.md` to the project root.
- `setup-prompt.md` — for bootstrapping a new or empty project directory, given an existing `VISION.md`.
- `refresh-prompt.md` — for retrofitting an existing codebase, or for restoring vision-based workflow capabilities that have faded.
- `README.md` — this file.

---

# Details

The rest of this document describes each prompt in depth, the shared operating model, and the agent disciplines. Read it once when adopting the prompts; skim back to the relevant sections as needed.

## The three prompts in depth

### `vision-interview-prompt.md`

A coding agent running in an empty project directory. The agent interviews the user about the project they want to create, then writes a complete `VISION.md` to the project root using the Write tool. That is the entire job: interview deeply, write the file, stop.

The interview covers seven areas — the idea, the user, core features, hard constraints, explicit non-goals, UX/style goals, and success criteria — with explicit probing, pushback on vague answers, contradiction surfacing, and capture of deferred decisions. By the time the file is written, it should be specific, opinionated, user-facing only, and timeless — describing the project *as it should exist*, not how it got there.

The agent does not propose architecture, suggest libraries, write code, set up tooling, or scaffold anything. The vision is the user's; the implementation is the next agent's.

### `setup-prompt.md`

A coding agent running in a brand-new or empty project directory, given that `VISION.md` already exists at the root. It governs the agent's behavior on the **first run** of a project.

On a normal first run:

- Read the existing `VISION.md`. Do not author or "improve" it.
- Create `AGENTS.md` as the repository's operating manual — all fifteen numbered sections, no subset.
- Bootstrap the project structure, tooling, standards, specs, tests, and the cross-cutting channels (`INBOX.md`, `BLOCKED.md`, `STATE_OF_PLAY.md`).
- Establish a spec-driven development workflow.
- Commit work at coherent checkpoints.

After `setup-prompt.md` runs, the repository has a complete operating model: a vision document, an agent manual, baseline standards, and a development workflow. That workflow should make it natural for the agent to work from the vision, proactively review the product for gaps, and test everything it changes or creates.

If `VISION.md` is missing when `setup-prompt.md` runs, the agent does not interview the user. It writes a `BLOCKED.md` entry telling the user to produce `VISION.md` first, and continues only with the parts of bootstrap that do not depend on the vision (operating manual, cross-cutting channels, repo scaffolding, tooling). The next session, after the user has landed `VISION.md`, picks up the rest.

### `refresh-prompt.md`

A coding agent running in an **existing codebase**. Use it as a migration path for repositories that were never set up with `setup-prompt.md`, or that have drifted away from it.

`refresh-prompt.md` runs a mandatory **discovery phase** — inventory of language, tooling, structure, existing files, tests, conventions — before changing anything. It treats current behavior as the invariant that must be preserved during refactor. It then audits `AGENTS.md` and specs, applies structural retrofits in a behavior-preserving order, pinned by tests before refactor.

If `VISION.md` exists when `refresh-prompt.md` runs, the agent audits it against the discovery report and surfaces deltas as vision holes — it does not lead an interview. If `VISION.md` is missing, the agent does not reverse-engineer one from the code. It writes a `BLOCKED.md` entry telling the user to produce `VISION.md` first and continues with the parts of retrofit that do not depend on the vision — auditing `AGENTS.md`, ensuring the cross-cutting channels exist, retrofitting standards. The next session, after the user has landed `VISION.md`, picks up the rest.

The refreshed workflow should also establish the same vision-driven operating mode: the agent can accept direct instructions, but should usually work by reviewing the product against the vision, finding improvements or problems, and implementing the next valuable step.

## When to use the refresh prompt

The refresh prompt is not only for repositories that were never set up with `setup-prompt.md`. **Use `refresh-prompt.md` whenever the vision-based workflow capabilities of a project are beginning to fade.** Common signals:

- The agent has stopped consulting `VISION.md` or `AGENTS.md` before making decisions.
- Conventions have drifted between modules, or new code no longer matches the standards the project started with.
- `VISION.md` and `AGENTS.md` exist but no longer reflect what the project actually is.
- Specs have become stale, missing for new features, or detached from the code they describe.
- The agent is making vision-shaped decisions (purpose, scope, exclusions) without surfacing them as vision holes.
- Tests have stopped pinning current behavior, and refactors are getting risky.
- The agent is shipping new features while existing core functionality is broken or painful.
- The agent finishes work sessions with uncommitted changes, or chooses tasks without first observing the product as a user.
- Commit history is sparse, bundles unrelated work, or is missing commits for new features.

Running `refresh-prompt.md` re-establishes the operating model: it audits `VISION.md` (if present) and `AGENTS.md` against the actual state of the code, and brings structure, standards, and workflow back in line with the standard. The end state should be indistinguishable in operating quality from a fresh `setup-prompt.md` run.

## The shared operating model

All three prompts encode the same operating model:

- `VISION.md` is the user's, and read-only to the agent. The user produces it before any agent runs.
- The agent owns implementation, structure, standards, tooling, workflows, maintenance, and repository hygiene.
- A spec-driven workflow governs non-trivial work, with tests derived from specs.
- The codebase structure is continuously renewable — large refactors are expected, not just permitted, when a better structure is identified. Structural changes still must not displace Tier 0 or Tier 1 work on a broken or painful product.
- The agent never presents a plan to the user and always works independently. The only situation in which the agent may direct a question back to the user is when an `INBOX.md` item requires clarification. If the agent spots a major hole in the vision, it surfaces it clearly (in `AGENTS.md`, a comment, or a checkpoint) and keeps working under a stated assumption rather than stopping to ask.
- Commits happen aggressively at well-defined checkpoints. A change is commit-ready when it compiles and type-checks, breaks no working functionality, and can be described in one sentence. Mandatory commit triggers include: after any new feature, bug fix, refactor, spec/doc/`AGENTS.md` change, test change, dependency update, before starting an unrelated task, and before ending a work session. A non-trivial session with zero commits is a failure mode.
- The agent runs a **state-of-play gate** before picking the next task: it builds and exercises the product as a user would, and records a dated entry in `STATE_OF_PLAY.md` (what works, what's broken, what feels bad). All work is then ranked into **priority tiers**: T0 broken/unplayable, T1 painful/empty, T2 missing-vision-features, T3 polish. A higher tier always outranks a lower one; a new feature is never the right next step while T0 or T1 work is open.
- Important decisions are recorded with a one-liner: **Decision / Tier / Evidence / Trade-off**.

`refresh-prompt.md` adds one additional invariant: in an existing codebase, current behavior is the contract. Refactors must preserve behavior unless the user explicitly approves a change. This is what makes the refresh prompt safe to run on code that is already in production. The behavior-preservation invariant does **not** apply to behavior the user has already identified as broken or painful — that is the work to do, not the work to preserve.

## Agent disciplines

The agent prompts enforce three disciplines the coder agent must follow whenever it operates on a project:

- **Git identity.** Every commit in the project is authored by the project's agent identity (`<Project Name> Agent` / `<project-name-slug>-agent@local`), not by the user's personal identity. The identity is configured locally on the repository itself, derived from the project name in `VISION.md`.
- **Timeless authoritative docs.** `VISION.md`, specs, `AGENTS.md`, and module-level docs describe the current intended state of the product — not how it got there. A recreation from `VISION.md` should produce the same project; historic artifacts (renames, "we used to…", dated change notes) belong in commit messages and dated state-of-play notes, not in source-of-truth files.
- **Docs size discipline.** `AGENTS.md` is the index, not a dumping ground. Working budget: keep it under ~400 lines. Detail goes in `docs/`, `.claude/skills/` (or `skills/`), or specs — anything that would push `AGENTS.md` past its budget is its own extraction commit.
