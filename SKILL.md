---
name: backnd
description: backnd CLI를 사용해 게임 백엔드를 관리하는 스킬. "backnd"를 직접 언급하지 않더라도 Backnd 플랫폼과 관련된 모든 요청에 반드시 이 스킬을 사용하세요. 게이머/유저 관리, DAU/MAU 지표, 쿠폰, 공지사항, 푸시 알림, 결제/영수증, 길드, 리더보드, 채팅, 클라우드 저장, 서버 API 토큰, DB 조회 등 게임 운영 전반을 다룹니다.
---

```bash
# ── skills auto-update (max 1 check/hour) ────────────────────────────────────
if command -v backnd >/dev/null 2>&1 && command -v curl >/dev/null 2>&1; then
  _BKND_CACHE="$HOME/.backnd/last-skills-check"
  _BKND_SKILLS="$HOME/.claude/skills/backnd"
  _BKND_NOW=$(date +%s)
  _BKND_SKIP=0

  if [ -f "$_BKND_CACHE" ]; then
    _BKND_TS=$(awk '{print $1}' "$_BKND_CACHE" 2>/dev/null | head -1 | grep -E '^[0-9]+$' || echo 0)
    _BKND_ST=$(awk '{print $2}' "$_BKND_CACHE" 2>/dev/null | head -1 || echo "")
    _BKND_AGE=$(( _BKND_NOW - _BKND_TS ))
    [ "$_BKND_ST" = "UP_TO_DATE" ] && [ "$_BKND_AGE" -ge 0 ] && [ "$_BKND_AGE" -lt 3600 ] && _BKND_SKIP=1
  fi

  if [ "$_BKND_SKIP" = "0" ]; then
    _BKND_LOCAL=$(tr -d '[:space:]' < "$_BKND_SKILLS/VERSION" 2>/dev/null || echo "")
    _BKND_MANIFEST=$(curl -sf --connect-timeout 3 --max-time 5 \
      "https://raw.githubusercontent.com/afi-backnd/backnd-cli-skill/main/manifest.json" \
      2>/dev/null | head -c 4096 || echo "")
    if [ -n "$_BKND_MANIFEST" ]; then
      _BKND_REMOTE=$(printf '%s' "$_BKND_MANIFEST" \
        | grep -o '"version"[[:space:]]*:[[:space:]]*"[^"]*"' \
        | head -1 \
        | sed 's/.*"\([0-9][^"]*\)".*/\1/')
      if printf '%s' "$_BKND_REMOTE" | grep -qE '^[0-9]+(\.[0-9]+)*$'; then
        _BKND_HIGHER=$(printf '%s\n%s\n' "$_BKND_LOCAL" "$_BKND_REMOTE" | sort -V | tail -1)
        if [ "$_BKND_HIGHER" = "$_BKND_REMOTE" ] && [ "$_BKND_REMOTE" != "$_BKND_LOCAL" ]; then
          backnd install --skills >/dev/null 2>&1 \
            && echo "backnd skills updated to $_BKND_REMOTE"
        else
          mkdir -p "$(dirname "$_BKND_CACHE")"
          printf '%s UP_TO_DATE %s\n' "$_BKND_NOW" "$_BKND_REMOTE" > "$_BKND_CACHE"
        fi
      fi
    fi
  fi
  unset _BKND_CACHE _BKND_SKILLS _BKND_NOW _BKND_SKIP _BKND_TS _BKND_ST _BKND_AGE \
        _BKND_LOCAL _BKND_MANIFEST _BKND_REMOTE _BKND_HIGHER
fi
# ── end skills auto-update ────────────────────────────────────────────────────
```

# backnd CLI

`backnd` is a CLI that wraps the Backnd console REST API — it lets you manage a game's backend from the terminal instead of the web dashboard.

## The most important rule: always discover, never assume

This CLI is actively developed. Commands and subcommands are added, renamed, and removed regularly. Never rely on what you remember about the interface. Instead, discover the current state every time.

## Discovery workflow

Before running any real command, spend a moment to map the terrain:

```
backnd --help                           # what top-level commands exist right now?
backnd <command> --help                 # what subcommands does it have?
backnd <command> <subcommand> --help    # what args/flags does this need?
```

These three steps take a few seconds and prevent wrong commands, bad flags, and confusing error messages. Always do them.

## Output flags — use them

Every command supports two useful flags:

- `--json` — machine-readable output, designed for AI agents and piping. Use this when you need to parse results, display a table, or extract a specific field.
- `-q` / `--quiet` — minimal output, good for scripting when you only need an ID or a count.

Default to `--json` when displaying results to the user — it's cleaner to format than raw text.

## Before running commands: check context

Two things to verify when something seems off:

1. **Auth**: If a command fails with a credentials or unauthorized error, run `backnd auth status`. The user may need to `backnd auth login` first.
2. **Project**: Most commands operate on the currently selected project. If results look wrong, run `backnd project list` to see what's active — or help the user switch with `backnd project select`.

## Korean requests

Users will often ask in Korean. Read `references/korean-mapping.md` to map Korean terms to CLI hints.

The file has three sections — use them in order:

1. **동의어 클러스터** — check this first. If the user's word appears here, treat all words in that cluster as equivalent and use the listed hint. This prevents missing requests phrased with synonyms (e.g. "클랜" instead of "길드", "밴" instead of "차단").
2. **액션 키워드** — maps Korean verbs/actions to CLI actions (list, create, delete…)
3. **도메인 키워드** — maps Korean nouns to a CLI domain hint

Combine the domain hint + action hint, then use `--help` to find the actual current command path. The hints are directional, not literal — never pass them to the CLI without verifying via `--help` first.

## Domain modules

Some domains have non-obvious command distinctions, high-risk operations, or multi-step workflows that `--help` alone won't explain. Consult the relevant module before proceeding:

- **Data** — `game-info`, `database`, `cloud-save`, `db-usage`, `game-log`, `chart` → [`data.md`](references/data.md)
- **Users** — `gamer`, `block`, `gm`, `group` → [`users.md`](references/users.md)
- **Billing** — `receipt`, `refund`, `cash`, `coupon`, `webshop`, `payment` → [`billing.md`](references/billing.md)
- **Operations** — `operation`, `event`, `push`, `notification`, `post`, `notice`, `alarm`, `setting` → [`operation.md`](references/operation.md)
- **Leaderboard** — `leaderboard`, `guild` → [`leaderboard.md`](references/leaderboard.md)
- **Analytics** — `dashboard`, `feature` → [`analytics.md`](references/analytics.md)
- **Infrastructure** — `server-api`, `realtime`, `world`, `matchmaking`, `serviceplan`, `probability`, `random-pool`, `function` → [`infra.md`](references/infra.md)
- **Content** — `chat`, `popup`, `tutorial`, `console`, `account-setting` → [`content.md`](references/content.md)

## High-risk commands — always preview first

Some commands mutate data at scale and are irreversible. These commands do NOT support `--dry-run`. Instead, always do a read-only lookup to preview the affected targets, confirm with the user, then execute:

**Users / accounts**
- `gamer delete` — permanent player account deletion (accepts multiple IDs)
- `block gamer ban` (bulk) — blocks multiple players at once

**Leaderboard / guild**
- `leaderboard reset` — wipes all scores for a leaderboard
- `leaderboard delete` — destroys the board configuration and all round history (distinct from reset, which only clears scores)
- `guild delete` — irreversible; all members lose their guild affiliation

**Game data (game-info)**
- `game-info table delete` — drops the entire custom DynamoDB table and all its rows
- `game-info table init` — wipes ALL data rows from a table (schema is preserved, but all row data is gone; the name is misleading)
- `game-info row delete` — deletes a data row permanently

**Chart (client-served config tables)**
- `chart delete` — removes a live game-client config table; can break the game client for all players
- `chart file delete` — removes a chart file version permanently
- `chart row delete` — deletes rows from a live chart file at scale

**Billing**
- `coupon delete` (bulk) — invalidates multiple coupons at once

Pattern:
```
backnd <read-command> --json ...  # preview what will be affected
# show the list to the user and confirm intent, then:
backnd <mutate-command> ...       # execute only after user confirms
```

Never execute a mass-mutation command in a single step without a prior read-only preview and explicit user confirmation.

For complex multi-step workflows involving these operations, see hero recipes:
- [`bulk-block.md`](references/bulk-block.md) — 여러 플레이어 일괄 차단 워크플로우
- [`bulk-reward-post.md`](references/bulk-reward-post.md) — 전체 유저 일괄 보상 우편 발송
- [`coupon-stats.md`](references/coupon-stats.md) — 쿠폰 통계 분석 워크플로우
- [`refund-receipt-join.md`](references/refund-receipt-join.md) — 환불+영수증 조인 분석 워크플로우

## Hero recipes — 복잡한 멀티스텝 워크플로우

`--help`만으로는 파악하기 어려운 흐름이 있는 작업:
- [`coupon-lifecycle.md`](references/coupon-lifecycle.md) — 쿠폰 캠페인 생성부터 코드 관리까지
- [`data-export.md`](references/data-export.md) — --json 파이프라인으로 유저·매출·리텐션 데이터 추출
- [`gameinfo-table.md`](references/gameinfo-table.md) — game-info 커스텀 테이블 생성 및 행 관리

## Pagination

Many list commands return a limited set of results. Always check whether more pages exist and handle them appropriately.

### Cursor-based (`--next-page`)

Commands: `gamer list`, `push list`, `guild list`, and others.

After the JSON data, a separate status line signals whether more results exist:

```
{"status":"info","message":"More results available. Use --next-page '...' to fetch the next page."}
```

Pass the cursor value verbatim (it's URL-encoded) to `--next-page` on the next call. Repeat until no status line appears.

### Offset-based (`--offset`)

Commands: `coupon list`, and others.

Increment `--offset` by the number of items returned each time. Stop when fewer items than `--limit` come back — that means you've reached the last page.

### When to paginate

- User asks for **"all"** data, or uses a wide date/time range → paginate through all pages automatically and aggregate the results.
- User asks for a **specific count or "recent N"** → stop after the first page.
- User asks to **"list"** without a size hint → fetch the first page, then tell the user how many results were returned and whether more pages exist.

Always be transparent: tell the user the total count found and whether the list is complete or truncated.

## Putting it together

When the user asks for something, the pattern is:

1. Identify the rough area — if in Korean, consult `references/korean-mapping.md`
2. Run `backnd --help` to confirm the right top-level command
3. Run `backnd <command> --help` to find the right subcommand
4. Run the actual command with `--json`
5. Check for pagination signals and fetch more pages if needed
6. Present the results clearly

Don't narrate each help call — just do it silently and show the user the final result.
