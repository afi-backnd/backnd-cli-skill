# Infrastructure Domain

Commands: `serverapi`, `realtime-noti`, `world`, `matchmake`, `serviceplan`, `probability`, `random-pool`, `random`, `function`

## Non-obvious distinctions

**probability — this is a probability TABLE, not a statistics chart**
- `probability` configures gacha/drop-rate tables: which items drop at what rates
- "드랍률 패치해줘", "가챠 확률 변경" → `probability`
- Has nothing to do with statistics charts or dashboards

**random-pool**
- Groups probability tables into a pool for random selection
- Used for random box / loot box features where multiple probability tables are combined
- Distinct from individual `probability` table management

**world**
- Multiplayer game server instances (rooms, sessions)
- Not related to game data or leaderboards

**serverapi**
- API token management for server-to-server authentication
- "서버 API 토큰 재발급" → `serverapi`

**random**
- Read-only list of random card configurations (gacha/loot box probability tables)
- Distinct from `probability`: `random` lists card sets; `probability` is where you manage the actual probability tables and files
- "랜덤 카드 목록" → `random`

**realtime-noti**
- Checks and toggles whether realtime push notifications are enabled for the project
- "실시간 푸시 활성화 여부" → `realtime-noti get`

**matchmake**
- Matchmaking pool configuration management
- "매칭 풀 조회", "매칭 설정 변경" → `matchmake`

**serviceplan**
- Backnd subscription plan management (affects usage limits and billing)
- Changes here affect the entire project's capacity

**function**
- Lambda / serverless function management deployed to Backnd
- Despite the name, this is infrastructure, not analytics
- "람다 함수 목록 보여줘" → `function list`

## Common patterns

**Update a drop rate:**
```
backnd probability --help                          # confirm subcommands
backnd probability list --json                     # find the target table ID
backnd probability update --json --id <table-id> --item <item> --rate <rate>
```

**Check current service plan:**
```
backnd serviceplan status refund-management
```
