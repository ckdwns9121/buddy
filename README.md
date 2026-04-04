# buddy-kill

Claude Code buddy companion management plugin.

## Install

```
/plugin marketplace add ckdwns9121/buddy-kill
```

## Uninstall

```
/plugin uninstall buddy@buddy-kill
```

## Commands

### `/buddy:kill`

Kill your current buddy and reroll a completely new one with different species, rarity, and stats.

## Why account UUID rotation

If only `companionGeneration` changes, the next roll can still collide and feel the same.
This plugin rotates `oauthAccount.accountUuid` to a fresh random UUID to force a new seed path.

## How it works

Claude Code companions are seeded from `companionUserId + salt`.
By changing `oauthAccount.accountUuid`, the seed changes for the next `/buddy` hatch.

## Restore

If anything goes wrong:

```bash
cp ~/.claude.json.bak ~/.claude.json
```
