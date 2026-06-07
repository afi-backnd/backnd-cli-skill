# Data Domain

Commands: `gameinfo`, `database`, `cloud-save`, `dbusage`, `gamelog`, `chart`

## Non-obvious distinctions

**gameinfo vs database**
- `gameinfo` — custom game data rows your team defined (DynamoDB-backed, game-specific tables with actual row data)
- `database` — DB schema inspection (table structure, not row data)
- "게임 데이터 조회/백업" almost always means `gameinfo`, not `database`

**cloud-save**
- Per-user S3 file storage — one blob per player, not a table
- Use to read or overwrite a specific player's save data
- Not related to `database` or `gameinfo`

**gamelog**
- Append-only event/action log. Read-only — no create/update/delete subcommands

**dbusage**
- Read-only capacity and storage metrics. No mutation commands.

**chart**
- Data table configurations served to the game client (e.g. item tables, config sheets)
- Supports folders, versioned files, and a `file-apply` to make a file live
- Distinct from `probability` (probability is for gacha/drop-rate tables; chart is for general data tables)

## Backup / export pattern

"게임 데이터 백업해줘" → `gameinfo export-start`

```
backnd gameinfo export-status --json    # check if export can be triggered now
backnd gameinfo export-start --json     # trigger the export job
backnd gameinfo export-list --json      # list completed snapshots
backnd gameinfo export-download --json  # download a specific snapshot
```

## Disambiguation: "게임 DB"

If the user says "게임 DB 조회":
- Schema / structure question → `database`
- Actual data rows → `gameinfo`
Ask to clarify if ambiguous.
