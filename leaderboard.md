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

Before deleting a guild, check the current member count:
```
backnd guild info --json --id <guild-id>    # check member_count
```
If members exist, ask the user how to handle them (transfer ownership, notify, etc.)
before deleting.

## Common patterns

**List top N players on a board:**
```
backnd leaderboard list --json --id <board-id> --limit 100
```

**Find a player's current rank:**
```
backnd leaderboard info --json --id <board-id> --gamer-id <gamer-id>
```
