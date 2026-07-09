# lx_wnk-marketplace

Personal [Claude Code](https://code.claude.com) plugin marketplace by [Alex Wink](https://github.com/lx-wnk).

A marketplace is a catalog (`.claude-plugin/marketplace.json`) that points Claude Code at one or more installable plugins. Adding this marketplace lets you install and update the plugins below from the Claude Code CLI or the Desktop app.

## Plugins

| Plugin | Description | Source |
|--------|-------------|--------|
| **agents** | Specialist sub-agents for AI-assisted development (backend, frontend, security, review, and more). | [lx-wnk/agents](https://github.com/lx-wnk/agents) |
| **skills** | Architecture design/review, component design/review, PR review-and-fix, tech-gazette, obsidian, and agent-context lifecycle skills. | [lx-wnk/skills](https://github.com/lx-wnk/skills) |

## Installation

### 1. Add the marketplace

```bash
/plugin marketplace add lx-wnk/claude-marketplace
```

`lx-wnk/claude-marketplace` is the GitHub `owner/repo`. Claude Code clones it and reads `.claude-plugin/marketplace.json`.

### 2. Install a plugin

```bash
/plugin install agents@lx_wnk-marketplace
/plugin install skills@lx_wnk-marketplace
```

Or browse interactively:

```bash
/plugin
```

→ **Marketplaces** → `lx_wnk-marketplace` → pick a plugin → **Install**.

The same flow works in the **Desktop app**: Settings → Plugins → Marketplaces.

## Updating

Plugins track the **default branch (`main`)** of their source repos — no version pinning.

### One-time: enable auto-update

Third-party marketplaces have auto-update **disabled by default**. Turn it on once:

```
/plugin
```

→ **Marketplaces** → `lx_wnk-marketplace` → **Enable auto-update**. Claude Code then
re-checks and pulls new commits at startup.

### Manual update

```bash
/plugin marketplace update lx_wnk-marketplace   # refresh the catalog first
/plugin update agents
/plugin update skills
```

> **If a plugin stays frozen** (Update greyed out / stuck on an old version), you're
> hitting a known Claude Code cache bug: `/plugin update` does not re-fetch the plugin's
> source clone before comparing versions, so the stale cache is never replaced. Force it
> by clearing this marketplace's cache and reinstalling:
> ```bash
> rm -rf ~/.claude/plugins/cache/lx_wnk-marketplace
> /plugin marketplace update lx_wnk-marketplace
> /plugin install agents@lx_wnk-marketplace
> /plugin install skills@lx_wnk-marketplace
> ```
> Deleting a cache directory is safe — it's a local copy and rebuilds on install. Close
> other Claude Code sessions first (they may hold a lock on the cache). See
> [Versioning & updates](#versioning--updates) for the full picture.

## Uninstalling

```bash
/plugin uninstall agents@lx_wnk-marketplace
/plugin marketplace remove lx_wnk-marketplace   # optional: drop the catalog too
```

## Versioning & updates

This marketplace intentionally carries **no version pins**:

- No top-level `version` in `marketplace.json`.
- No per-plugin `version` in the plugin entries.
- No `version` in the plugins' own `plugin.json`.
- Plugin `source` blocks use `{"source":"github","repo":"…"}` with **no `ref` / `sha`**.

With nothing pinned, Claude Code falls back to the git commit SHA of each source
repo's default branch, so every push is picked up as an update — no manual version
bumps required. This is the officially recommended setup for actively-developed plugins
(*"leave `version` unset so the git commit SHA is used instead"*).

Two caveats to be aware of:

- **Auto-update is off by default** for third-party marketplaces — enable it once (see
  [Updating](#updating)), or the client never re-checks.
- **The manifest repo and the plugin repos are cached separately.** This repo
  (`lx-wnk/claude-marketplace`) holds only the catalog; `agents` and `skills` live in
  their own repos and are cloned into `~/.claude/plugins/cache/lx_wnk-marketplace/<plugin>/<sha>/`.
  A known Claude Code bug means `/plugin update` may not re-fetch those plugin clones, so
  a new push can go undetected until you clear the cache and reinstall
  (see [Updating](#updating)).

The plugin source repos are **public**, so background auto-update works without
credential prompts.

## Repository layout

```
.claude-plugin/
  marketplace.json   # the catalog: name, owner, plugins[]
README.md
```

Plugin code lives in its own repository (linked in the table above), not in this repo.

## License

The marketplace manifest is provided as-is. Each plugin is MIT-licensed in its own repository.
