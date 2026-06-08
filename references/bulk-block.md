# Hero Recipe: 대량 유저 차단

사용 시나리오: 어뷰징 탐지, 대회 부정 행위 등으로 다수 유저를 일괄 차단해야 할 때.

## 1단계 — 차단 대상 추출

조건으로 게이머 목록을 추출:
```
backnd gamer list --json --nickname "..."
# 또는 특정 조건이 있는 경우 여러 검색 조합:
backnd gamer list --json --start-in-date 2026-06-01T00:00:00.000Z
```

페이지네이션 신호 확인 후 전체 수집. `--json` 출력에서 `gamer_id` 필드 추출.

## 2단계 — 각 대상의 현재 상태 확인

차단 전에 이미 차단된 유저가 있는지 확인:
```
backnd block gamer list --json
```

## 3단계 — 사전 검토

`block gamer ban`은 `--dry-run`을 지원하지 않는다. 대신 1~2단계 결과를 사용자에게 보여주고 최종 확인을 받는다.

## 4단계 — 실제 차단 실행

사용자 확인 후 실행:
```
backnd block gamer ban --json --gamer-ids <gamer-id> --reason "..."
# 다수 동시 차단:
backnd block gamer ban --json --gamer-ids "id1,id2,id3" --reason "..."
```

## 주의사항

- `block gamer`은 게임 전체 접근 차단. 채팅만 제한하려면 `chat ban`을 사용 (content.md 참조).
- 차단 사유(`--reason`)를 항상 기록해 이후 검토에 활용.
- 실수로 차단한 경우: `backnd block gamer unban --gamer-ids <gamer-id>`로 해제 가능.
