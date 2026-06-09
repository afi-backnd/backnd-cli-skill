# Leaderboard Domain

Commands: `leaderboard`, `guild`

## 핵심 개념

**그룹(group) — leaderboard 파티션**
`backnd group`(멀티 캐릭터)과 무관. 리더보드를 여러 독립 구간으로 나누는 파티션 키.
기본값 `default`. 서버별·지역별 별도 순위가 필요할 때 사용.
`rank list --group-name <name>`, `rank download --group-name <name>`으로 지정.
현재 그룹 설정은 `leaderboard describe --json <uuid>`의 group division 필드에서 확인.

**rank-type: user vs guild**
`rank list`에서 --gamer-id/--nickname/--guild-name으로 검색 시 필수.
- `user`: 개인 게이머 순위
- `guild`: 길드 단위 순위 (union rank 전용)

**union rank leaderboard**
길드 점수를 지원하는 특수 타입.
`leaderboard score guild`, `leaderboard guild-column`, `leaderboard reward`가 이 타입에만 해당.

**leaderboard score guild vs backnd guild**
- `backnd guild`: 길드 존재 자체 관리 (생성/삭제/멤버/타입)
- `backnd leaderboard score guild`: union rank 리더보드에서 길드 점수 조작

## Non-obvious distinctions

**leaderboard**
- Score-based ranking boards (player scores, positions, resets)
- "랭킹 보드 초기화" → `leaderboard reset`
- "특정 보드 순위 조회" → `leaderboard rank list --uuid <board-uuid>`
- "플레이어 순위 검색" → `leaderboard rank list --uuid <board-uuid> --gamer-id <gamer-id>`

**guild**
- Manages guilds / clans. Guild delete is irreversible — members lose their guild affiliation.

## High-risk operations (E3 — always preview first)

**leaderboard reset — wipes ALL scores for that board**

```
backnd leaderboard describe --json <board-uuid>      # confirm you have the right board
backnd leaderboard reset --uuid <board-uuid>         # execute after user confirms
```

Confirm the board UUID and the user's intent carefully. A reset on the wrong leaderboard
cannot be undone.

**guild delete**

Before deleting a guild, check the current members:
```
backnd guild describe --json --guild-name <guild-name>    # check members
```
If members exist, ask the user how to handle them (transfer ownership, notify, etc.)
before deleting.

## Common patterns

**List all leaderboard configurations:**
```
backnd leaderboard list --json
```

**Get top N ranks on a board:**
```
backnd leaderboard rank list --json --uuid <board-uuid>
```

**Find a player's current rank:**
```
backnd leaderboard rank list --json --uuid <board-uuid> --gamer-id <gamer-id>
```

**Inspect a specific leaderboard:**
```
backnd leaderboard describe <board-uuid> --json
```
