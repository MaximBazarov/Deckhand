# Deckhand
A deckhand (noun ) — a member of a ship's crew who performs general manual labor, maintenance, and operational duties on the deck of a vessel, separate from passenger service and engine room operations.

Deckhand is a **local agent-management tool for Firstmate**. Its promise is **atomic task and agent management**: one logical task maps one-to-one to its **current** agent, keyed by task id, while **relaunch generations** replace the process rather than creating a second logical agent.

Deckhand is the authoritative operational source for that task-agent aggregate. Unique task ids, current agent assignments, endpoints, isolated-copy paths, and active generations are enforced together. Lifecycle transitions are explicit, idempotent, and guarded against destructive cleanup, unlanded work, ambiguous ownership, and stale endpoint evidence.

**This repository is in progress.** The current published scope is architecture and decisions only. The CLI, store, and adapters are not a released integration surface yet.

## What Firstmate will use (and only this)

Firstmate’s **only** integration point is the Deckhand CLI contract, permanently, even after future Deckhand internals change.

Firstmate uses Deckhand as a client: it submits task-agent commands and consumes Deckhand’s versioned reports and notifications. Deckhand owns the operational state; future Firstmate updates must not read or write Firstmate task records (`state/<id>.meta` or equivalent), inspect Deckhand’s private store, or maintain a shadow copy as a substitute.

Firstmate may retain captain intent and prioritization, but it learns execution state only from Deckhand’s contract-defined responses and projections.

The executable is meant to be callable from any working directory. `FM_HOME` selects the home-local store.

Planned lifecycle operations (the minimum Firstmate needs):

- create
- start / attach
- inspect current state
- send data-plane instructions
- interrupt / stop
- relaunch as a new generation
- finish with a preserved report
- reconcile reality
- archive only after completion and cleanup safety checks

The contract will be versioned machine-readable JSON, with stable exit codes and structured errors. Idempotency keys and append-only events prevent retries from applying a transition twice.

## Runtime: Pi now, Herdr when needed

Agents are reached through an adapter boundary with capability discovery. Unsupported operations return explicit unsupported results; flags and lifecycle semantics are never guessed.

- **Pi** is the currently supported workflow path (verified executable and argument handling).
- **Herdr** is a seam for when it is needed. Herdr lifecycle commands are not enabled in this unguarded phase. A later enablement requires a verified interface and a herdr-lab dispatch.

## Platform and StateManagement

macOS first, Swift and Swift Package Manager. In-process state changes go through the existing [StateManagement](https://github.com/MaximBazarov/StateManagement) package at its **supported public boundary**. Deckhand will not fork or patch StateManagement.

That dependency is the GitHub package `git@github.com:MaximBazarov/StateManagement.git`, not a private local checkout. See [DECISIONS.md](DECISIONS.md) for the arrangement, local-only product limits, and why implementation is paused while architecture is settled.

## Status

Documentation and decisions are being written down first. Implementation follows once the architecture discussion is closed.
