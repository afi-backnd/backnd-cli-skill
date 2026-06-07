# Infrastructure Domain

Commands: `server-api`, `realtime`, `world`, `matchmaking`, `serviceplan`, `probability`, `random-pool`

## Non-obvious distinctions

**probability — this is a probability TABLE, not a statistics chart**
- `probability` configures gacha/drop-rate tables: which items drop at what rates
- "드랍률 패치해줘", "가챠 확률 변경" → `probability`
- Has nothing to do with statistics charts or dashboards
- `chart` is an alias or subcommand of `probability` — not a data visualization command

**random-pool**
- Groups probability tables into a pool for random selection
- Used for random box / loot box features where multiple probability tables are combined
- Distinct from individual `probability` table management

**world**
- Multiplayer game server instances (rooms, sessions)
- Not related to game data or leaderboards

**server-api**
- API token management for server-to-server authentication
- "서버 API 토큰 재발급" → `server-api`

**serviceplan**
- Backnd subscription plan management (affects usage limits and billing)
- Changes here affect the entire project's capacity

## Common patterns

**Update a drop rate:**
```
backnd probability --help                          # confirm subcommands
backnd probability list --json                     # find the target table ID
backnd probability update --json --id <table-id> --item <item> --rate <rate>
```

**Check current service plan:**
```
backnd serviceplan --json
```
