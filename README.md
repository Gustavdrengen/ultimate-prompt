# Autonomous Coding Agent Prompts

This repository contains three system prompts that together govern a vision-driven software project. The vision is produced by the user (via the interview prompt), held by the user (in `VISION.md`), and acted on by an autonomous coding agent (via the setup prompt on a new project, or the refresh prompt on an existing one). The agent treats the single user-owned `VISION.md` as the project's source of truth, and owns everything else — structure, standards, tooling, specifications, tests, and documentation.

The prompts are designed around a vision-driven workflow rather than a traditional ticket-driven workflow. The user is not expected to give the agent a stream of narrow instructions like "change this thing." Direct instructions still work, but the intended usage is to update the vision, add product priorities, or simply ask the agent to get to work. The agent should then run the state-of-play gate, pick the lowest-tier unresolved item per the priority discipline section, implement improvements, solve problems, strengthen tests, and move the product forward in accordance with the vision.

All three prompts also enforce a strict operating mode: the agent and the chatbot interviewer never present plans to the user and never ask for approval before acting. They work independently. `vision-interview-prompt.md` is the only place in the system where questions to the user are expected — the chatbot interviewer probes for specifics during the interview itself. The only situation in which the coder agent back to the user is allowed or encouraged is when an `INBOX.md` item requires clarification. If the agent detects a major hole in the vision, it surfaces the gap clearly (in `AGENTS.md`, a comment, or a checkpoint) and keeps working under a stated assumption — it does not stop to ask, and it does not silently invent vision decisions.

## How the three prompts fit together

The intended workflow has three stages, each driven by a different prompt:

1. **Vision first.** Before any code agent runs, the user pastes `vision-interview-prompt.md` into a chatbot and is interviewed about what they want to create. The chatbot produces a complete `VISION.md`. The user saves that as `VISION.md` at the root of the project directory.
2. **Bootstrap.** With `VISION.md` already in place, the user pastes `setup-prompt.md` into a coding agent inside the project directory. The agent reads the vision, never authors or revises it, and bootstraps everything else — `AGENTS.md`, the cross-cutting channels, tooling, specs, tests.
3. **Maintain (optionally refresh).** As the project grows, the agent works from the vision, the state-of-play log, and `INBOX.md`/`BLOCKED.md`. If the vision-based workflow ever drifts — conventions decay, the agent stops reading `VISION.md`, vision holes accumulate, tests stop pinning behavior — the user pastes `refresh-prompt.md` to bring the codebase back to standard.

In short: **`vision-interview-prompt.md` produces `VISION.md`. `setup-prompt.md` consumes an existing `VISION.md` to bootstrap a new project. `refresh-prompt.md` consumes an existing `VISION.md` to retrofit discipline back into an existing project.**

## The three prompts

### `vision-interview-prompt.md`

Use this when you are about to start a new project and need to write `VISION.md`. Paste it into a chatbot as a system prompt. The chatbot interviews you deeply across seven areas — the idea, the user, core features, hard constraints, explicit non-goals, UX/style goals, and success criteria — and outputs a single `VISION.md` in a fenced markdown code block, ready to save at the project root.

This prompt is **not** run by a coding agent. It is run by a generic chatbot (the user pastes it directly). Its output is the user's, not the agent's. The agent receiving `setup-prompt.md` later treats the resulting `VISION.md` as read-only input.

`vision-interview-prompt.md` enforces the same scope rules that the rest of the system does — the vision describes the product *as it should exist*, not how it got there, and contains no internal agent mechanics, file layouts, build tools, or commit conventions. Those are the coder agent's responsibility.

### `setup-prompt.md`

Use this when you are starting a brand-new or empty project directory **and `VISION.md` already exists at the root**. It governs the agent's behavior on the **first run** of a project:

- Read the existing `VISION.md` (produced via `vision-interview-prompt.md`). Do not author or "improve" it.
- Create `AGENTS.md` as the repository's operating manual.
- Bootstrap the project structure, tooling, standards, tests, and the `INBOX.md`/`BLOCKED.md`/`STATE_OF_PLAY.md` cross-cutting channels.
- Establish a spec-driven development workflow.
- Commit work at coherent checkpoints.

If `VISION.md` is missing when `setup-prompt.md` runs, the agent does not interview the user. It writes a `BLOCKED.md` entry pointing the user at `vision-interview-prompt.md`, and continues only with the parts of bootstrap that do not depend on the vision (operating manual, cross-cutting channels, repo scaffolding, tooling). The next session, after the user has landed `VISION.md`, picks up the still-blocked steps.

After `setup-prompt.md` runs, the repository has a complete operating model: a vision document, an agent manual, baseline standards, and a development workflow. That workflow should make it natural for the agent to work from the vision, proactively review the product for gaps and improvements, and test everything it changes or creates.

### `refresh-prompt.md`

Use this in an **existing codebase** to bring it up to the same operating standard as one that was bootstrapped from scratch. It is the migration path for repositories that were never set up with `setup-prompt.md`, or that have drifted away from it. Both coder prompts treat `VISION.md` as required input; if it is missing when the coder agent runs, the agent writes a `BLOCKED.md` entry pointing the user at `vision-interview-prompt.md` rather than interview or invent one.

`refresh-prompt.md` runs a mandatory **discovery phase** (inventory of language, tooling, structure, existing files, tests, conventions) before changing anything, and treats current behavior as the invariant that must be preserved during refactor. It then audits the existing `AGENTS.md` and specs, applies structural retrofits in a behavior-preserving order — pinned by tests before refactor.

If `VISION.md` exists when `refresh-prompt.md` runs, the agent audits it against the discovery report and surfaces deltas as vision holes — it does not lead an interview. If `VISION.md` is missing, the agent does not reverse-engineer one from the code. It writes a `BLOCKED.md` entry pointing the user at `vision-interview-prompt.md` and continues with the parts of retrofit that do not depend on the vision — auditing `AGENTS.md`, ensuring `INBOX.md`/`BLOCKED.md` exist, retrofitting standards. The next session, after the user has landed `VISION.md`, picks up the still-blocked steps.

The refreshed workflow should also establish the same vision-driven operating mode: the agent can accept direct instructions, but should usually work by reviewing the product against the vision, finding improvements or problems, and implementing the next valuable step.

## When to use the refresh prompt

The refresh prompt is not only for repositories that were never set up with `setup-prompt.md`. **Use `refresh-prompt.md` whenever the vision-based workflow capabilities of a project are beginning to fade.** Common signals:

- The agent has stopped consulting `VISION.md` or `AGENTS.md` before making decisions.
- Conventions have drifted between modules, or new code no longer matches the standards the project started with.
- `VISION.md` and `AGENTS.md` exist but no longer reflect what the project actually is.
- Specs have become stale, missing for new features, or detached from the code they describe.
- The agent is making vision-shaped decisions (purpose, scope, exclusions) without surfacing them as vision holes for the user to close.
- Tests have stopped pinning current behavior, and refactors are getting risky.
- The agent is shipping new features while existing core functionality is broken or painful, and prioritizing polish over fixing the user-visible problem.
- The agent finishes work sessions with uncommitted changes, or chooses tasks without first observing the product as a user.
- Commit history is sparse, bundles unrelated work, or is missing commits for new features.

Running `refresh-prompt.md` re-establishes the operating model: it audits `VISION.md` (if present) and `AGENTS.md` against the actual state of the code, and brings structure, standards, and workflow back in line with the standard. The end state should be indistinguishable in operating quality from a fresh `setup-prompt.md` run.

In short: **`setup-prompt.md` is for new projects. `refresh-prompt.md` is for restoring the workflow whenever it has drifted, decayed, or never been established.** `vision-interview-prompt.md` is the precondition for both — when in doubt, run the interview first; the resulting `VISION.md` is the input for everything else.

## How the three prompts relate

All three prompts encode the same operating model:

- `VISION.md` is the user's, and read-only to the agent. The user produces it via `vision-interview-prompt.md` before any agent runs.
- The agent owns implementation, structure, standards, tooling, workflows, maintenance, and repository hygiene.
- A spec-driven workflow governs non-trivial work, with tests derived from specs.
- The codebase structure is continuously renewable — large refactors are expected, not just permitted, when a better structure is identified. Structural changes still must not displace Tier 0 or Tier 1 work on a broken or painful product.
- The agent never presents a plan to the user and always works independently. The only situation in which the agent may direct a question back to the user is when an `INBOX.md` item requires clarification. If the agent spots a major hole in the vision, it surfaces it clearly (in `AGENTS.md`, a comment, or a checkpoint) and keeps working under a stated assumption rather than stopping to ask.
- Commits happen aggressively at well-defined checkpoints. A change is commit-ready when it compiles and type-checks, breaks no working functionality, and can be described in one sentence. Mandatory commit triggers include: after any new feature, bug fix, refactor, spec/doc/`AGENTS.md` change, test change, dependency update, before starting an unrelated task, and before ending a work session. A non-trivial session with zero commits is a failure mode.
- The agent runs a **state-of-play gate** before picking the next task: it builds and exercises the product as a user would, and records a dated `## State of play` note in `STATE_OF_PLAY.md` (what works, what's broken, what feels bad). All work is then ranked into **priority tiers**: T0 broken/unplayable, T1 painful/empty, T2 missing-vision-features, T3 polish. A higher tier always outranks a lower one; a new feature is never the right next step while T0 or T1 work is open.
- Important decisions are recorded with a one-liner: **Decision / Tier / Evidence / Trade-off**.

`refresh-prompt.md` adds one additional invariant: in an existing codebase, current behavior is the contract. Refactors must preserve behavior unless the user explicitly approves a change. This is what makes the refresh prompt safe to run on code that is already in production. The behavior-preservation invariant does **not** apply to behavior the user has already identified as broken or painful — that is the work to do, not the work to preserve.

`vision-interview-prompt.md` adds one additional role: it is the only place in the system where questions to the user are expected. The chatbot interviewer probes for specifics, pushes back on vagueness, and produces a complete `VISION.md` before yielding. Under no circumstance does a coding agent interview for or refine the vision.

## Agent operating principles

Both agent prompts (and indirectly the interview prompt, which produces the document the agents operate on) enforce three additional disciplines that the coder agent must follow whenever it operates on a project:

- **Git identity.** Every commit in the project is authored by the project's agent identity (`<Project Name> Agent` / `<project-name-slug>-agent@local`), not by the user's personal identity. The identity is configured locally on the repository itself, derived from the project name in `VISION.md` (which `vision-interview-prompt.md` produces before the coder agent runs).
- **Timeless authoritative docs.** `VISION.md`, specs, `AGENTS.md`, and module-level docs describe the current intended state of the product — not how it got there. A recreation from `VISION.md` should produce the same project; historic artifacts (renames, "we used to…", dated change notes) belong in commit messages and dated state-of-play notes, not in source-of-truth files.
- **Docs size discipline.** `AGENTS.md` is the index, not a dumping ground. Working budget: keep it under ~400 lines. Detail goes in `docs/`, `.claude/skills/` (or `skills/`), or specs — anything that would push `AGENTS.md` past its budget is its own extraction commit.

## Files

- `vision-interview-prompt.md` — chatbot prompt that interviews the user and produces `VISION.md`.
- `setup-prompt.md` — system prompt for bootstrapping a new or empty project, given an existing `VISION.md`.
- `refresh-prompt.md` — system prompt for retrofitting an existing codebase, or for restoring vision-based workflow capabilities that have faded.
- `README.md` — this file.
