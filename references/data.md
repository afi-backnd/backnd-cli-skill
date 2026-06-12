# Data Domain

Commands: `game-info`, `database`, `cloud-save`, `db-usage`, `game-log`, `chart`

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

**chart**
- Data table configurations served to the game client (e.g. item tables, config sheets)
- Supports folders, versioned files, and a `file-apply` to make a file live
- Distinct from `probability` (probability is for gacha/drop-rate tables; chart is for general data tables)

## High-risk operations (E3 — always preview first)

**game-info table delete — drops the entire table and all rows**

```
backnd game-info table list --json                            # confirm the right table
# show to user and confirm intent, then:
backnd game-info table delete --table-name <name>
```

**game-info table init — wipes ALL rows (schema preserved, data gone)**

The name sounds safe but this is a full data wipe. There is no undo.

```
backnd game-info row list --json --scope <private|public> --table <name>   # preview row count
# confirm intent, then:
backnd game-info table init --table-name <name>
```

**game-info row delete — permanent row deletion**

```
backnd game-info row describe --json ...                      # confirm the target row
# confirm intent, then:
backnd game-info row delete ...
```

**chart delete — removes a live game-client config table**

Deleting a chart that the game client actively reads will break functionality for all players.

```
backnd chart list --json                                      # confirm the right chart
# confirm intent, then:
backnd chart delete --ids <id>
```

**chart file delete — removes a chart file version**

```
backnd chart file list --json --chart-id <id>                 # confirm the right file
# confirm intent, then:
backnd chart file delete --chart-id <id> --file-id <file-id>
```

**chart row delete — deletes rows from a live chart file**

```
backnd chart file list --json --chart-id <id>                 # find the active file
# show affected rows to user, confirm intent, then:
backnd chart row delete --chart-id <id> --file-id <file-id> --row-ids <ids>
```

## Backup / export pattern

"게임 데이터 백업해줘" → `game-info export start`

```
backnd game-info export status --json    # check if export can be triggered now
backnd game-info export start --json     # trigger the export job
backnd game-info export list --json                         # list completed snapshots
backnd game-info export download --in-date <inDate> --json  # download a specific snapshot (--in-date 필수, export list 출력의 inDate 값)
```

## Disambiguation: "게임 DB"

If the user says "게임 DB 조회":
- Schema / structure question → `database`
- Actual data rows → `game-info`
Ask to clarify if ambiguous.
