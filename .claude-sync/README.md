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
| `.github/workflows/sync-upstream.yml` | Weekly `git merge upstream/main` + regenerate |

Everything else is upstream and must stay untouched, so merges never conflict.

## Consuming it

From [PierrickGT/dotfiles](https://github.com/PierrickGT/dotfiles), `claude/plugins/manifest.json`:

```json
{ "repo": "PierrickGT/plugins", "name": "pstack" }
```

Then `just update-claude-plugins`.

## Publishing another plugin from this repo

Add its directory name to `PLUGINS` in `.claude-sync/gen-manifests.sh`, run the script, commit.

## Upstream drift

The manifests are generated rather than hand-written so upstream version bumps and description
changes propagate on their own. A failed sync run means upstream added a file at one of the
paths above and the conflict needs a human.
