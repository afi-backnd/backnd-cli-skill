# Users Domain

Commands: `gamer`, `block`, `gm`, `group`

## Non-obvious distinctions

**gamer vs gm**
- `gamer` — player/customer accounts (end-users of the game)
- `gm` — game master / operator accounts (staff who manage the game)
- "관리자 계정 조회" → `gm`, not `gamer`

**block**
- Blocks a player from accessing the game entirely
- Has nested subcommands: `block gamer`, `block device`, `block multi`
- Separate from chat ban (see `content.md`) — chat ban only restricts chat
- "게임 정지", "이용 제한", "밴" → `block gamer`

**group**
- Multi-character or party grouping within the game
- Not related to admin groups or GM groups

## High-risk operations (E3 — always preview first)

`gamer delete` and bulk variants permanently delete player accounts. No undo. These commands do NOT support `--dry-run`.

```
backnd gamer list --json --nickname "..."         # find targets first
# show the list to the user and confirm intent, then:
backnd gamer delete <gamer-id>                   # single delete
backnd gamer delete <id1> <id2> <id3>            # bulk delete (multiple positional args)
```

Always extract and review the target list with `gamer list --json` first,
then confirm with the user before deleting.

## Common patterns

**Find a player by nickname / ID:**
```
backnd gamer list --json --nickname "..."
backnd gamer describe --json <gamer-id>
```

**Check if a player is currently blocked:**
```
backnd block gamer list --json
```
