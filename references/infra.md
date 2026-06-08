# Infrastructure Domain

Commands: `server-api`, `realtime`, `world`, `matchmaking`, `serviceplan`, `probability`, `random-pool`, `function`

## Non-obvious distinctions

**probability — this is a probability TABLE, not a statistics chart**
- `probability` configures gacha/drop-rate tables: which items drop at what rates
- "드랍률 패치해줘", "가챠 확률 변경" → `probability`
- Has nothing to do with statistics charts or dashboards

**random-pool**
- Groups probability tables into a pool for random selection
- Used for random box / loot box features where multiple probability tables are combined
- Distinct from individual `probability` table management
- "랜덤 카드 목록", "랜덤 풀 조회" → `random-pool list`

**world**
- Multiplayer game server instances (rooms, sessions)
- Not related to game data or leaderboards

**server-api**
- API token management for server-to-server authentication
- "서버 API 토큰 재발급" → `server-api`

**realtime**
- Checks and toggles whether realtime push notifications are enabled for the project
- "실시간 푸시 활성화 여부" → `realtime get`

**matchmaking**
- Matchmaking pool configuration management
- "매칭 풀 조회", "매칭 설정 변경" → `matchmaking`

**serviceplan**
- Backnd subscription plan management (affects usage limits and billing)
- Changes here affect the entire project's capacity

**function**
- Lambda / serverless function management deployed to Backnd
- Despite the name, this is infrastructure, not analytics
- "람다 함수 목록 보여줘" → `function list`

## Common patterns

**Update a drop rate (file-based workflow):**

Drop rates live in data files, not in the chart metadata. `probability update` only edits name/description.

```
backnd probability list --json                                    # find chart ID
backnd probability file list --id <chart-id> --json              # see current files
backnd probability file download --id <chart-id> --file-id <file-id>  # get current rates
# edit the downloaded JSON with the new rates, then re-upload:
backnd probability file upload --id <chart-id> --file-name <name> --data-json '[...]'
backnd probability file apply --id <chart-id> --file-id <new-file-id>  # activate new file
```

**List random pool categories:**
```
backnd random-pool list --json
```

**Check current service plan:**
```
backnd serviceplan status refund-management
```
