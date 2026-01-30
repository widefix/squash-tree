# Squash Tree Specification — v1

## 1. Purpose

This document defines the formal specification for representing squash relationships in Git using native mechanisms.

---

## 2. Namespaces

### Notes Namespace

```
refs/notes/squash-tree
```

Used to store squash metadata.

### Preservation Refs

```
refs/squash-archive/<root>/<child>
```

Used to keep original commits reachable.

---

## 3. Metadata Schema

All metadata must be valid JSON.

### Required Fields

```json
{
  "spec": "squash-tree/v1",
  "type": "squash",
  "root": "<commit>",
  "base": "<commit>",
  "children": [
    { "hash": "<commit>", "order": 1 }
  ],
  "created_at": "<ISO8601>"
}
```

### Optional Fields

```json
{
  "strategy": "rebase|merge|github|manual",
  "author": "<string>",
  "message": "<string>"
}
```

---

## 4. Rules

- One squash note per commit per namespace
- Order must be explicit
- Children commits must exist and be preserved
- Invalid metadata must fail safely

---

## 5. Operations

### Inspect
Reads and validates squash metadata.

### Tree
Recursively resolves squash relationships.

### Unsquash
Reapplies children commits in order.

---

## 6. Versioning

The `spec` field is mandatory. Incompatible versions must be rejected.

---

## 7. Compatibility

- GitHub/GitLab do not preserve metadata
- Squash Tree operates client-side only

---

## 8. Security

The spec does not permit automatic history rewriting without explicit user intent.
