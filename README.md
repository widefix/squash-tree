# squash-tree

**Status:** RFC · Early Implementation

`squash-tree` is a Git extension that preserves, visualizes, and reverses squash commits by maintaining an explicit *logical squash graph* alongside Git’s native commit DAG.

Git intentionally discards history during squash operations.  
`squash-tree` makes that history **explicit, inspectable, and reversible** — without modifying Git itself.

---

## Install

**macOS / Linux (one command):**

```bash
curl -sSL https://raw.githubusercontent.com/widefix/squash-tree/main/scripts/install.sh | bash
```

Then run `git squash-tree init` (or `git squash-tree init --global`) in a repository.

To pin a version: `curl -sSL ... | bash -s -- v0.1.0`

**Other options:** [Download a pre-built binary](https://github.com/widefix/squash-tree/releases) for your platform, or [build from source](INSTALL.md). Full setup (Windows, Git alias, hooks) is in [INSTALL.md](docs/install.md).

---

## Motivation

Git squash workflows (`merge --squash`, interactive rebase, GitHub squash merges) permanently collapse commit history.

Once a squash is created:

- The original commit structure is lost
- There is no way to inspect how a commit was composed
- Reverting or “unsquashing” is impossible without external context
- Auditability and debuggability suffer

This is not a bug in Git — it is a design choice.

`squash-tree` does not try to change Git’s behavior.  
Instead, it **records squash relationships explicitly** using Git-native mechanisms.

---

## Core Idea

Git maintains a **commit DAG** that represents code history.

`squash-tree` introduces a **parallel logical graph** that represents *composition history*:

- Git DAG → *what code exists*
- Squash Tree → *how that code was composed*

This separation allows squash history to be:
- preserved
- inspected
- traversed
- reversed

without rewriting Git internals.

---

## Design Principles

- **Git remains the source of truth for code**
- **Squash relationships are explicit, never inferred**
- **All metadata is opt-in and Git-native**
- **No heuristics, no magic**
- **Safety over convenience**

---

## High-Level Approach

`squash-tree` uses two existing Git mechanisms:

1. **Git notes** (with a dedicated namespace)  
   Used to attach structured squash metadata to commits.

2. **Hidden Git refs**  
   Used to preserve original commits and prevent garbage collection.

Together, these allow the construction of a **recursive squash tree** where:
- a squash commit is a logical parent
- its original commits are logical children
- children may themselves be squash commits

---

## What This Project Does

- Defines a formal **Squash Tree specification**
- Stores squash relationships explicitly
- Visualizes squash trees
- Enables safe, non-destructive *unsquash* operations
- Works across clones and platforms

---

## What This Project Does NOT Do

- Reconstruct historical squashes without prior metadata
- Modify Git’s internal object model
- Automatically rewrite published history
- Infer squash structure from diffs or reflogs

If metadata was not recorded at squash time, it cannot be recovered later.

---

## Project Status

This repository is in **early RFC / design-first stage**.

Current focus:
- locking the data model
- documenting invariants
- building a minimal, correct foundation

Features will be implemented incrementally once the design is stable.

---

## Repository Structure (initial)

```
cmd/
  squash-tree/        # CLI entrypoint
docs/
  design.md           # Architecture & rationale
  spec.md             # Formal Squash Tree specification
```

---

## Usage (future)

The intended CLI shape (subject to change):

```bash
git squash-tree inspect <commit>
git squash-tree tree <commit>
git squash-tree unsquash <commit>
```

No commands are considered stable yet.

---

## Contributing

This project is **design-driven**.

Before proposing features or code changes:
- read `docs/design.md`
- read `docs/spec.md`

Guiding rule:

> Correctness and explicitness are more important than convenience.

Spec changes require discussion.

---

## Why This Exists

Git chose simplicity and performance over historical preservation.

`squash-tree` exists to restore **visibility and control** — without fighting Git.
