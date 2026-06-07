---
name: backnd
description: backnd CLI를 사용해 게임 백엔드를 관리하는 스킬. "backnd"를 직접 언급하지 않더라도 Backnd 플랫폼과 관련된 모든 요청에 반드시 이 스킬을 사용하세요. 게이머/유저 관리, DAU/MAU 지표, 쿠폰, 공지사항, 푸시 알림, 결제/영수증, 길드, 리더보드, 채팅, 클라우드 저장, 서버 API 토큰, DB 조회 등 게임 운영 전반을 다룹니다.
---

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

- **Data** — `game-info`, `database`, `cloud-save`, `db-usage`, `game-log` → [`data.md`](data.md)
- **Users** — `gamer`, `blocking`, `gm`, `group` → [`users.md`](users.md)
- **Billing** — `receipt`, `refund`, `cash`, `coupon`, `webshop`, `payment` → [`billing.md`](billing.md)
- **Operations** — `operation`, `push`, `notification`, `post`, `notice`, `alarm`, `setting` → [`operation.md`](operation.md)
- **Leaderboard** — `leaderboard`, `rank`, `guild` → [`leaderboard.md`](leaderboard.md)
- **Analytics** — `dashboard`, `statistics`, `function`, `feature` → [`analytics.md`](analytics.md)
- **Infrastructure** — `server-api`, `realtime`, `world`, `matchmaking`, `serviceplan`, `probability`, `random-pool` → [`infra.md`](infra.md)
- **Content** — `chat`, `popup`, `tutorial`, `console`, `account-setting` → [`content.md`](content.md)

## High-risk commands — always dry-run first

Some commands mutate data at scale and are irreversible. Before running any of these, run with `--dry-run` first and confirm the affected list with the user:

- `gamer delete` / `gamer bulk-delete` — permanent player account deletion
- `leaderboard reset` — wipes all scores for a leaderboard
- `block gamer` (bulk) — blocks multiple players at once
- `coupon revoke` (bulk) — invalidates multiple coupons at once

Pattern:
```
backnd <command> --dry-run ...    # show what would happen
# review output with user, then:
backnd <command> ...              # execute only after user confirms
```

Never execute a mass-mutation command in a single step without a prior dry-run.

## Pagination

Many list commands return a limited set of results. Always check whether more pages exist and handle them appropriately.

### Cursor-based (`--next-page`)

Commands: `gamer search`, `push list`, `guild guilds`, and others.

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
