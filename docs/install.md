# squash-tree — Installation and Setup

This guide covers how to install and configure `git squash-tree`.

---

## Prerequisites

- **Git** — For the `git squash-tree` integration  
- **Go 1.21+** — Only needed when building from source  

---

## One-line install (macOS / Linux)

```bash
curl -sSL https://raw.githubusercontent.com/widefix/squash-tree/refs/heads/main/scripts/install.sh | bash
```

This downloads the latest release, installs the binary, and configures the Git alias. Then run `git squash-tree init` (or `init --global`) in a repository.

To pin a version: `curl -sSL ... | bash -s -- v0.1.0`

---

## Download Pre-built Binary

If release builds are available (e.g. on [GitHub Releases](https://github.com/widefix/squash-tree/releases)), you can skip building.

### 1. Download

Choose the archive for your platform:

| Platform | File |
|----------|------|
| macOS (Intel) | `git-squash-tree_Darwin_x86_64.tar.gz` |
| macOS (Apple Silicon) | `git-squash-tree_Darwin_arm64.tar.gz` |
| Linux (amd64) | `git-squash-tree_Linux_x86_64.tar.gz` |
| Linux (arm64) | `git-squash-tree_Linux_arm64.tar.gz` |
| Windows (amd64) | `git-squash-tree_Windows_x86_64.zip` |

### 2. Extract and install

**macOS / Linux:**

```bash
curl -sL https://github.com/widefix/squash-tree/releases/download/v0.1.0/git-squash-tree_Darwin_arm64.tar.gz | tar xz
chmod +x git-squash-tree
sudo mv git-squash-tree /usr/local/bin/
```

Or to a user directory (no sudo):

```bash
mkdir -p ~/bin
mv git-squash-tree ~/bin/
export PATH="$HOME/bin:$PATH"  # Add to .bashrc or .zshrc
```

**Windows:** Extract the `.zip` and add the directory containing `git-squash-tree.exe` to your `PATH`.

**macOS Gatekeeper:** If you see *"Apple could not verify git-squash-tree is free of malware"*, remove the quarantine attribute:

```bash
xattr -d com.apple.quarantine $(which git-squash-tree)
```

Or with the full path, e.g. `xattr -d com.apple.quarantine /usr/local/bin/git-squash-tree`.

### 3. Configure Git

```bash
git config --global alias.squash-tree '! git-squash-tree'
```

Then run `git squash-tree init` in your repo or `git squash-tree init --global` (see [Setup: Initialize Hooks](#setup-initialize-hooks)).

---

## Building from Source

### 1. Clone or obtain the repository

```bash
git clone <repository-url> squash-tree
cd squash-tree
```

### 2. Build the binary

```bash
go build -o git-squash-tree ./cmd/git-squash-tree
```

This produces a `git-squash-tree` executable in the current directory.

---

## Installation Options

### Option A: Add to PATH

Place the binary somewhere in your `PATH` (e.g. `~/bin`, `/usr/local/bin`):

```bash
# Example: copy to a user bin directory
mkdir -p ~/bin
cp git-squash-tree ~/bin/

# Ensure ~/bin is in PATH (add to .bashrc, .zshrc, etc.)
export PATH="$HOME/bin:$PATH"
```

Then register it as a Git alias:

```bash
git config --global alias.squash-tree '! git-squash-tree'
```

### Option B: Git alias with absolute path

If you keep the binary in a fixed location:

```bash
git config --global alias.squash-tree '! /path/to/squash-tree/git-squash-tree'
```

### Option C: Git alias with project-relative path

If you run `git squash-tree` from within the squash-tree project directory:

```bash
git config --global alias.squash-tree '! $(pwd)/git-squash-tree'
```

Note: This only works when your current directory is the squash-tree repo.

### Option D: Install as a Git subcommand

Create a wrapper script named `git-squash-tree` (no extension) in a directory that is in your `PATH`:

```bash
#!/bin/sh
exec /path/to/squash-tree/git-squash-tree "$@"
```

Make it executable:

```bash
chmod +x /path/to/git-squash-tree
```

With this approach, `git squash-tree` works without an alias.

---

## Verify Installation

Run:

```bash
git squash-tree help
```

You should see:

```
Usage: git squash-tree <commit>       Show squash tree for a commit
       git squash-tree init [--global] Install hooks in repo (or globally)
       git squash-tree add-metadata --root=<ref> --base=<ref> --children=<refs>
...
```

---

## Setup: Initialize Hooks

Hooks record squash metadata automatically when you perform squash operations (interactive rebase, `merge --squash`). Choose one:

### Local (current repository only)

```bash
cd /path/to/your-repo
git squash-tree init
```

Hooks are installed in `.git/hooks/`. Affects only this repository.

### Global (all repositories)

```bash
git squash-tree init --global
```

Hooks are installed in `~/.config/git/squash-tree-hooks/`, and Git’s global `core.hooksPath` is set so all repositories use these hooks.

> **Note:** Global hooks apply to every Git repo on your machine. Use local init if you want squash-tree only in specific projects.

---

## Post-Installation

- For design and specification, see [docs/design.md](docs/design.md) and [docs/spec.md](docs/spec.md).
