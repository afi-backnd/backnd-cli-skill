# Users Domain

Commands: `gamer`, `blocking`, `gm`, `group`

## Non-obvious distinctions

**gamer vs gm**
- `gamer` — player/customer accounts (end-users of the game)
- `gm` — game master / operator accounts (staff who manage the game)
- "관리자 계정 조회" → `gm`, not `gamer`

**blocking**
- Blocks a player from accessing the game entirely
- Separate from chat ban (see `content.md`) — chat ban only restricts chat
- "게임 정지", "이용 제한", "밴" → `blocking`

**group**
- Multi-character or party grouping within the game
- Not related to admin groups or GM groups

## High-risk operations (E3 — always dry-run first)

`gamer delete` and bulk variants permanently delete player accounts. No undo.

```
backnd gamer delete --dry-run --id <gamer-id>     # preview
backnd gamer delete --id <gamer-id>               # execute after confirmation
```

For bulk operations, always extract the target list with `gamer search --json` first,
review the list with the user, then proceed.

## Common patterns

**Find a player by nickname / ID:**
```
backnd gamer search --json --nickname "..."
backnd gamer info --json --id <gamer-id>
```

**Check if a player is currently blocked:**
```
backnd blocking list --json --id <gamer-id>
```
