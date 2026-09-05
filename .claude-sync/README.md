# Why this fork exists

[cursor/plugins](https://github.com/cursor/plugins) ships Cursor manifests only
(`.cursor-plugin/plugin.json`) and has no `.claude-plugin/marketplace.json`, so Claude Code
cannot install from it. This fork adds the Claude Code manifests and nothing else — plugin
content is byte-for-byte upstream.

## What this fork owns

| Path | Purpose |
| --- | --- |
| `.claude-sync/gen-manifests.sh` | Derives the Claude manifests from the Cursor ones |
| `.claude-plugin/marketplace.json` | Generated. Marketplace Claude Code installs from |
| `pstack/.claude-plugin/plugin.json` | Generated. Per-plugin manifest |
| `.github/workflows/sync-upstream.yml` | Weekly upstream merge + regenerate |

Everything else is upstream and must stay untouched, so merges never conflict.

## Consuming it

From [PierrickGT/dotfiles](https://github.com/PierrickGT/dotfiles), `claude/plugins/manifest.json`:

```json
{ "repo": "PierrickGT/plugins", "name": "pstack" }
```

Then `just update-claude-plugins`.

## Publishing another plugin from this repo

Add its directory name to `PLUGINS` in `.claude-sync/gen-manifests.sh`, run the script, commit.

## Commit signing

Every commit on `main` is signed. Human commits use the maintainer's GPG key over SSH; the
weekly sync creates its commits through the GitHub API — the Merges API for the upstream merge,
the Contents API for regenerated manifests — so GitHub signs them with its own key. A runner has
no GPG key, so a plain `git push` from CI would leave unsigned commits in a signed history.

The Git Data API is not used anywhere here: commits created that way are unsigned.

## Upstream drift

The manifests are generated rather than hand-written so upstream version bumps and description
changes propagate on their own. A failed sync run means upstream added a file at one of the
paths above and the conflict needs a human.
