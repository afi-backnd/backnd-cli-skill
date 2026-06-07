# Analytics Domain

Commands: `dashboard`, `statistics`, `function`, `feature`

## Non-obvious distinctions

**dashboard vs statistics**
- `dashboard` — real-time DAU/MAU and live session metrics (today's numbers)
- `statistics` — historical time-series data over a date range
- "오늘 접속자 수" → `dashboard`
- "지난달 통계" → `statistics`

**function — not analytics**
- `function` manages Lambda / serverless functions deployed to Backnd
- Despite the name, this is infrastructure management, not an analytics function
- "람다 함수 목록 보여줘" → `function`

**feature**
- Feature flag management — enable/disable game features by toggle
- "피처 플래그 켜줘" → `feature`

## Common patterns

**Get today's DAU:**
```
backnd dashboard --json
```

**Get monthly statistics for a date range:**
```
backnd statistics --json --from 2026-05-01 --to 2026-05-31
```

**List deployed Lambda functions:**
```
backnd function list --json
```

## Notes

`dashboard` output is a snapshot — it reflects the current moment.
For trend analysis over time, always use `statistics` with an explicit date range.
