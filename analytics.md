# Analytics Domain

Commands: `dashboard`, `statistics`, `feature`

## Non-obvious distinctions

**dashboard vs statistics**
- `dashboard` — DAU/MAU and session metrics queried by year/month (fixed calendar unit)
- `statistics` — DAU/MAU for a flexible start-date/end-date range
- "3월 DAU 보여줘" → `dashboard dau --year 2026 --month 3`
- "지난 2주 DAU" → `statistics dau --start-date ... --end-date ...`

**feature**
- Read-only query for feature initialization dates (when key game features were activated)
- Not a toggle — no enable/disable subcommands
- "기능 초기화 날짜 확인" → `feature dates`

## Common patterns

**Get DAU for a specific month:**
```
backnd dashboard dau --json --year 2026 --month 6
```

**Get DAU for a flexible date range:**
```
backnd statistics dau --json --start-date 2026-05-01 --end-date 2026-05-31
```

## Notes

`dashboard` output is a snapshot — it reflects the current moment.
For trend analysis over time, always use `statistics` with an explicit date range.
