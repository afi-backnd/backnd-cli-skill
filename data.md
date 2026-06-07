# Data Domain

Commands: `game-info`, `database`, `cloud-save`, `db-usage`, `game-log`

## Non-obvious distinctions

**game-info vs database**
- `game-info` — custom game data rows your team defined (DynamoDB-backed, game-specific tables with actual row data)
- `database` — DB schema inspection (table structure, not row data)
- "게임 데이터 조회/백업" almost always means `game-info`, not `database`

**cloud-save**
- Per-user S3 file storage — one blob per player, not a table
- Use to read or overwrite a specific player's save data
- Not related to `database` or `game-info`

**game-log**
- Append-only event/action log. Read-only — no create/update/delete subcommands

**db-usage**
- Read-only capacity and storage metrics. No mutation commands.

## Backup / export pattern

"게임 데이터 백업해줘" → `game-info export`

```
backnd game-info --help             # confirm export subcommand exists
backnd game-info export --help      # check required flags (table name, format)
backnd game-info export --json ...
```

## Disambiguation: "게임 DB"

If the user says "게임 DB 조회":
- Schema / structure question → `database`
- Actual data rows → `game-info`
Ask to clarify if ambiguous.
