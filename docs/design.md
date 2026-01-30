# Squash Tree — Design Document

## Status
Draft — Design Proposal

## Overview

This document describes the design and implementation approach for **Squash Tree**, a Git extension that preserves, visualizes, and reverses squash commits by maintaining a logical squash relationship graph alongside Git’s native commit DAG.

Git squash operations intentionally discard historical structure. Squash Tree does not attempt to change Git’s behavior. Instead, it records and maintains squash relationships explicitly, enabling tree visualization and safe unsquash operations.

---

## Problem Statement

Git squash workflows permanently collapse commit history. Once a squash is completed, Git provides no reference to the original commits and no way to reconstruct their hierarchy.

This leads to:
- Loss of auditability
- Reduced debugging capability
- Inability to safely revert or inspect changes

---

## Goals

### Primary Goals
- Preserve logical relationships between squash commits and original commits
- Represent squash history as a tree/DAG
- Enable recursive inspection
- Support safe unsquash operations

### Non-Goals
- Reconstruct past squashes without metadata
- Modify Git internals
- Infer structure heuristically

---

## Core Design Principles

1. Git remains the source of truth for code
2. Squash relationships are explicit and separate
3. All metadata is opt-in and Git-native
4. Commits must be preserved to be reversible
5. Safety over convenience

---

## High-Level Architecture

Squash Tree maintains a parallel logical DAG alongside Git’s commit DAG.

- Git DAG → code history
- Squash Tree DAG → composition history

The squash DAG is stored using:
- Git notes (metadata)
- Hidden refs (commit preservation)

---

## Data Storage

### Git Notes

Squash metadata is stored in a dedicated notes namespace:

```
refs/notes/squash-tree
```

Notes contain structured JSON describing squash relationships.

### Commit Preservation

To guarantee reversibility, all child commits are preserved using hidden refs:

```
refs/squash-archive/<squash_commit>/<child_commit>
```

This prevents garbage collection and ensures cross-clone availability.

---

## Operations

### Visualization
Reads squash metadata recursively and renders a tree representation.

### Unsquash (Non-Destructive)
Recreates the original commit structure on a new branch by cherry-picking preserved commits.

### Unsquash (Destructive, Optional)
Rewrites history by removing the squash commit and reapplying children. Disabled by default.

---

## Limitations

- Requires metadata at squash time
- Does not hide internal refs from `git log --all`
- Opt-in only

---

## Conclusion

Squash Tree restores visibility and control over squash operations while respecting Git’s design constraints.
