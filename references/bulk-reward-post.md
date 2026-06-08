# Hero Recipe: 전체 유저 일괄 보상 우편 발송

사용 시나리오: 긴급 버그 수정, 점검 보상, 이벤트 등으로 전체 또는 특정 유저에게 인게임 우편을 일괄 발송할 때.

## 1단계 — 발송 범위 결정

`--receiver` 옵션이 발송 대상을 결정한다. 선택지를 먼저 확인:

```
backnd post send --help
```

| `--receiver` 값 | 대상 |
|----------------|------|
| `all`          | 전체 유저 (되돌릴 수 없음) |
| `<gamer-id>`   | 특정 유저 한 명 |

전체 발송(`all`)은 수백만 유저에게 동시에 발송된다. 실수로 보낸 우편은 회수할 수 없다.

## 2단계 — 소규모 검증 (전체 발송 전 권장)

먼저 테스트 계정 한 명에게 발송해 내용과 형식을 검증:

`--receiver`는 gamer UUID가 아닌 **gamer inDate** 값을 받는다 (`backnd gamer list --json`으로 확인):

```
backnd post send \
  --title "<title>" \
  --content "<content>" \
  --expiration-date "YYYY-MM-DDTHH:MM:SS.000Z" \
  --receiver "2024-01-01T00:00:00.000Z"
```

발송 후 해당 계정의 우편함에서 수신 확인:

```
backnd post list --json
```

## 3단계 — 전체 발송

내용 확인 후 사용자에게 발송 대상과 우편 내용을 다시 한 번 보여주고 최종 확인을 받는다:

```
backnd post send \
  --title "<title>" \
  --content "<content>" \
  --expiration-date "YYYY-MM-DDTHH:MM:SS.000Z" \
  --receiver all
```

## 4단계 — 발송 확인

```
backnd post list --json
```

최상단에 방금 발송한 우편이 있는지 확인.

## 주의사항

- `--receiver all`은 되돌릴 수 없다. 발송 전 반드시 사용자에게 내용을 보여주고 확인.
- `--expiration-date`를 생략하면 CLI 기본값이 적용될 수 있다. `--help`로 확인.
- 인게임 재화·아이템을 첨부하는 옵션이 있다면 `backnd post send --help`에서 별도 플래그 확인.
