# Leaderboard Domain

Commands: `leaderboard`, `rank`, `guild`

## Non-obvious distinctions

**leaderboard vs rank**
- `leaderboard` — score-based ranking boards (player scores, positions, resets)
- `rank` — player rank/tier system (rank grades, thresholds — separate concept from leaderboard score)
- "랭킹 보드 초기화" → `leaderboard reset`
- "등급 기준 조회" → `rank`

**guild**
- Manages guilds / clans. Guild delete is irreversible — members lose their guild affiliation.

## High-risk operations (E3 — always dry-run first)

**leaderboard reset — wipes ALL scores for that board**

```
backnd leaderboard reset --dry-run --id <board-id>   # preview affected board
backnd leaderboard reset --id <board-id>             # execute after user confirms
```

Confirm the board ID and the user's intent carefully. A reset on the wrong leaderboard
cannot be undone.

**guild delete**

Before deleting a guild, check the current members:
```
backnd guild guild-get --json --guild-name <guild-name>    # check members
```
If members exist, ask the user how to handle them (transfer ownership, notify, etc.)
before deleting.

## Common patterns

**List all leaderboard configurations:**
```
backnd leaderboard list --json
```

**Get top N ranks on a board:**
```
backnd leaderboard ranks --json <board-uuid>
```

**Find a player's current rank:**
```
backnd leaderboard rank-search --json --id <board-uuid> --gamer-id <gamer-id>
```

**Inspect a specific leaderboard:**
```
backnd leaderboard describe --json <board-uuid>
```
