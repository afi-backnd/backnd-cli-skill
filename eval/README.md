# Skill Eval — 라우팅 정확도 검증

`cases.json`에 정의된 테스트 케이스로 Claude Code 스킬의 한국어 라우팅 정확도를 검증합니다.

## 케이스 구조

```json
{
  "id": "drop-rate-patch",
  "prompt": "드랍률 패치해줘",
  "expected_domain": "probability",
  "expected_command_hint": "probability",
  "notes": "라우팅 판단 근거 메모"
}
```

## 수동 실행

Claude Code 새 세션에서 빈 컨텍스트로 각 프롬프트를 입력한 뒤, 응답에서 실행하려는 CLI 명령어를 확인합니다.

```
# 검증 기준:
# - expected_command_hint의 명령어를 실행하려고 하면 PASS
# - 다른 명령어로 라우팅되면 FAIL (notes에 충돌 근거 기재)
```

## 충돌 케이스 (collision-*)

다음 케이스는 비슷한 한국어 표현이 다른 도메인으로 라우팅될 수 있는 위험 영역입니다:

| ID | 프롬프트 | 정답 | 혼동 위험 |
|----|----------|------|-----------|
| collision-gamelog-vs-event | 이벤트 로그 조회해줘 | game-log | event (인게임 이벤트 관리) |
| collision-ingame-event-vs-gamelog | 이번 시즌 이벤트 만들어줘 | event | game-log |
| collision-probability-vs-random | 확률 수정해줘 | probability | random (다른 데이터스토어) |
| collision-operation-vs-notice | 인게임 공지 올려줘 | notice | operation (player support용, create 없음) |

## 케이스 추가 기준

- 새 도메인 추가 시: 기본 라우팅 케이스 1건 이상
- 비슷한 한국어 표현이 다른 도메인을 가리킬 때: collision-* 케이스 추가
- `korean-mapping.md` 수정 시: 영향받는 케이스 검증 후 필요하면 신규 추가
