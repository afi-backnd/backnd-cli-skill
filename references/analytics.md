# Analytics Domain

Commands: `dashboard`, `feature`

## Non-obvious distinctions

**dashboard — fixed vs flexible date queries**
- `dashboard dau/mau` — year/month 단위 고정 쿼리
- `dashboard stats dau/mau` — start-date/end-date 자유 범위 쿼리 (`stats`는 grouping 명령 — 반드시 `dau` 또는 `mau` 하위 명령 필요)
- "3월 DAU 보여줘" → `dashboard dau --year 2026 --month 3`
- "지난 2주 DAU" → `dashboard stats dau --start-date ... --end-date ...`

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
backnd dashboard stats dau --json --start-date 2026-05-01 --end-date 2026-05-31
```

## Notes

`dashboard dau/mau` outputs a fixed calendar unit snapshot.
For trend analysis over time, use `dashboard stats dau` or `dashboard stats mau` with an explicit date range.
