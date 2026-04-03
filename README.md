# buddy

Claude Code buddy companion management plugin.

## Install

```bash
claude --plugin-dir /path/to/buddy
```

## Commands

### `/buddy:kill`

Kill your current buddy and reroll a completely new one with different species, rarity, and stats.

## How it works

Claude Code companions are deterministically generated from `hash(accountUuid + salt)`. This plugin changes `accountUuid` in `~/.claude.json` to produce a different roll, while keeping OAuth authentication intact.

## Restore

If anything goes wrong:

```bash
cp ~/.claude.json.bak ~/.claude.json
```
