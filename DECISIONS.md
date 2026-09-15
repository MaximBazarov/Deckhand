# Deckhand decisions

Living record of choices that bind the first implementation. This file is the narrowed **documentation-only** scope of the current branch: architecture discussion, not code.

## Product

- Deckhand is a **separate** local agent-management tool for Firstmate, not a folder inside the Firstmate/FLEET tree.
- Promise: **atomic task and agent management**. One logical task and its current agent assignment are one aggregate keyed by `task_id`. Relaunch allocates a new generation (`{task-id}:gN`) instead of a duplicate logical agent.
- Deckhand is the authoritative operational source for task-agent lifecycle state.
- Firstmate’s **exclusive and permanent** integration point is the Deckhand CLI contract (JSON, stable exit codes, structured errors, `FM_HOME` for the home-local store, invokable from any cwd). Firstmate submits commands and consumes Deckhand’s reports, notifications, and projections. Future Firstmate updates must not bypass it by reading or writing Firstmate task records directly, inspecting Deckhand’s private store, or maintaining a shadow copy of operational state.
- Compatibility posture: **Pi now**, **Herdr when needed**, behind an adapter interface with capability discovery and explicit unsupported results.
- Herdr lifecycle (start/stop/delete/restart and similar) is **not enabled** until a verified interface is used under a herdr-lab dispatch. Help text may inform the capability matrix; unguarded work must not drive Herdr sessions.

## Platform and stack

- **macOS first** (local-only limitation for this phase). Linux/Windows are out of scope until a later decision.
- Implementation language: **Swift**, distributed with **Swift Package Manager**.
- In-process mutation: the published [StateManagement](https://github.com/MaximBazarov/StateManagement) library, used only through its supported boundary (containers, operations, environment). Deckhand does not modify that repository.
- StateManagement dependency: Git remote **`git@github.com:MaximBazarov/StateManagement.git`**, not a path dependency on a sibling checkout. Pin a version or revision when implementation starts.
- Durability vs Environment: StateManagement owns in-process state and the operation boundary. A Deckhand-owned snapshot (home-local under `FM_HOME`) is the durable operational copy across CLI invocations. Firstmate does not own or recreate that snapshot; uniqueness and transitions still go through Deckhand operations, then persist.

## Lifecycle (planned, not implemented in this commit)

Minimum verbs Firstmate needs: create, start or attach, inspect, send, interrupt or stop, relaunch, finish (preserve report), reconcile, archive (only after finished + cleanup safety).

Guards: no destructive cleanup with unlanded work; no relaunch into a live or ambiguous endpoint; idempotent retries via idempotency keys; append-only event history.

## Current branch scope (narrowed)

This push contains **only** `README.md` and `DECISIONS.md`.

Implementation files that may exist in the worktree are **preserved and uncommitted** on purpose. Do not delete them; do not ship them until the architecture discussion says implementation may resume.

No PR. No merge to `main` from this documentation push. Origin remote is `git@github.com:MaximBazarov/Deckhand.git`.
