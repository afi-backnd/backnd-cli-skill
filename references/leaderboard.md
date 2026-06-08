# Leaderboard Domain

Commands: `leaderboard`, `guild`

## Non-obvious distinctions

**leaderboard**
- Score-based ranking boards (player scores, positions, resets)
- "랭킹 보드 초기화" → `leaderboard reset`
- "특정 보드 순위 조회" → `leaderboard rank list --uuid <board-uuid>`
- "플레이어 순위 검색" → `leaderboard rank list --uuid <board-uuid> --gamer-id <gamer-id>`

**guild**
- Manages guilds / clans. Guild delete is irreversible — members lose their guild affiliation.

## High-risk operations (E3 — always preview first)

**leaderboard reset — wipes ALL scores for that board**

```
backnd leaderboard describe --json <board-uuid>      # confirm you have the right board
backnd leaderboard reset --uuid <board-uuid>         # execute after user confirms
```

Confirm the board UUID and the user's intent carefully. A reset on the wrong leaderboard
cannot be undone.

**guild delete**

Before deleting a guild, check the current members:
```
backnd guild describe --json --guild-name <guild-name>    # check members
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
backnd leaderboard rank list --json --uuid <board-uuid>
```

**Find a player's current rank:**
```
backnd leaderboard rank list --json --uuid <board-uuid> --gamer-id <gamer-id>
```

**Inspect a specific leaderboard:**
```
backnd leaderboard describe <board-uuid> --json
```
