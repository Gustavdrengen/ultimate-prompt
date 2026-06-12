# Autonomous Coding Agent Prompts

This repository contains two system prompts that together govern an autonomous coding agent's behavior on a software project. The agent treats a single user-owned `VISION.md` file as the project's source of truth, and owns everything else — structure, standards, tooling, specifications, tests, and documentation.

The prompts are designed around a vision-driven workflow rather than a traditional ticket-driven workflow. The user is not expected to give the agent a stream of narrow instructions like "change this thing." Direct instructions still work, but the intended usage is to update the vision, add product priorities, or simply ask the agent to get to work. The agent should then inspect the project, identify the highest-leverage next step, implement improvements, solve problems, strengthen tests, and move the product forward in accordance with the vision.

Both prompts also enforce a strict operating mode: the agent never presents plans to the user and never asks for approval before acting. It works independently. The only time questions to the user are allowed or encouraged is during vision creation or vision modification. If the agent detects a major hole in the vision, it surfaces the gap clearly (in `AGENTS.md`, a comment, or a checkpoint) and keeps working under a stated assumption — it does not stop to ask, and it does not silently invent vision decisions.

## The two prompts

### `setup-prompt.md`

Use this when you are starting from a brand-new or empty project directory. It governs the agent's behavior on the **first run** of a project:

- Discover the project vision (asking the minimum questions needed) and create `VISION.md`.
- Create `AGENTS.md` as the repository's operating manual.
- Bootstrap the project structure, tooling, standards, and tests.
- Establish a spec-driven development workflow.
- Commit work at coherent checkpoints.

After `setup-prompt.md` runs, the repository has a complete operating model: a vision document, an agent manual, baseline standards, and a development workflow. That workflow should make it natural for the agent to work from the vision, proactively review the product for gaps and improvements, and test everything it changes or creates.

### `refresh-prompt.md`

Use this in an **existing codebase** to bring it up to the same operating standard as one that was bootstrapped from scratch. It is the migration path for repositories that were never set up with `setup-prompt.md`, or that have drifted away from it.

`refresh-prompt.md` runs a mandatory **discovery phase** (inventory of language, tooling, structure, existing files, tests, conventions) before changing anything, and treats current behavior as the invariant that must be preserved during refactor. It then audits and updates `VISION.md`, `AGENTS.md`, and any specs, and applies structural retrofits in a behavior-preserving order — pinned by tests before refactor.

The refreshed workflow should also establish the same vision-driven operating mode: the agent can accept direct instructions, but should usually work by reviewing the product against the vision, finding improvements or problems, and implementing the next valuable step.

## When to use the refresh prompt

The refresh prompt is not only for repositories that were never set up with `setup-prompt.md`. **Use `refresh-prompt.md` whenever the vision-based workflow capabilities of a project are beginning to fade.** Common signals:

- The agent has stopped consulting `VISION.md` or `AGENTS.md` before making decisions.
- Conventions have drifted between modules, or new code no longer matches the standards the project started with.
- `VISION.md` and `AGENTS.md` exist but no longer reflect what the project actually is.
- Specs have become stale, missing for new features, or detached from the code they describe.
- The agent is making vision-shaped decisions (purpose, scope, exclusions) without surfacing them as vision holes for the user to close.
- Tests have stopped pinning current behavior, and refactors are getting risky.

Running `refresh-prompt.md` re-establishes the operating model: it re-discovers the vision, audits `AGENTS.md` and specs against the actual state of the code, and brings structure, standards, and workflow back in line with the standard. The end state should be indistinguishable in operating quality from a fresh `setup-prompt.md` run.

In short: **`setup-prompt.md` is for new projects. `refresh-prompt.md` is for restoring the workflow whenever it has drifted, decayed, or never been established.**

## How the two prompts relate

Both prompts encode the same operating model:

- `VISION.md` is the user's, and read-only to the agent.
- The agent owns implementation, structure, standards, tooling, workflows, maintenance, and repository hygiene.
- A spec-driven workflow governs non-trivial work, with tests derived from specs.
- The codebase structure is continuously renewable — large refactors are expected, not just permitted, when a better structure is identified.
- The agent never presents a plan to the user and always works independently. The only time questions are allowed or encouraged is during vision creation or vision modification. If the agent spots a major hole in the vision, it surfaces it clearly (in `AGENTS.md`, a comment, or a checkpoint) and keeps working under a stated assumption rather than stopping to ask.
- Commits happen automatically at coherent checkpoints.

`refresh-prompt.md` adds one additional invariant: in an existing codebase, current behavior is the contract. Refactors must preserve behavior unless the user explicitly approves a change. This is what makes the refresh prompt safe to run on code that is already in production.

## Files

- `setup-prompt.md` — system prompt for bootstrapping a new or empty project.
- `refresh-prompt.md` — system prompt for retrofitting an existing codebase, or for restoring vision-based workflow capabilities that have faded.
- `README.md` — this file.
