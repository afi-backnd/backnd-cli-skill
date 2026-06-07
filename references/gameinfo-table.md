# Hero Recipe: gameinfo 커스텀 테이블 생성 및 행 관리

사용 시나리오: Unity/Unreal SDK 없이 REST API나 CLI만으로 게임 커스텀 데이터 테이블을 만들고 행을 읽고 쓸 때.

## 1단계 — 테이블 생성

```
backnd gameinfo create-table --name "<table-name>" --table-type <private|public>
```

| `--table-type` | 접근 범위 |
|---------------|-----------|
| `private`     | 특정 게이머의 데이터만 (유저별 저장소) |
| `public`      | 모든 게이머가 공유하는 데이터 (전역 설정, 아이템 테이블 등) |

## 2단계 — 테이블 목록 확인

```
backnd gameinfo list --json
```

## 3단계 — 행 생성

`--data` 파라미터에 타입 명시 JSON 형식이 필요하다. 각 필드를 `{"dataType": "<type>", "dataValue": <value>}` 구조로 작성:

```
backnd gameinfo create-row \
  --gamer-id "<gamer-id>" \
  --partition "<table-name>" \
  --data '{"score":{"dataType":"number","dataValue":100},"name":{"dataType":"string","dataValue":"player1"}}'
```

지원 타입: `string`, `number`, `boolean`

private 테이블은 `--gamer-id`가 필수. public 테이블에서는 생략하거나 다른 플래그로 대체될 수 있다 — `--help`로 확인.

## 4단계 — 행 조회

```
backnd gameinfo list-rows --scope <private|public> --table "<table-name>" --json
```

특정 게이머의 행만 조회:

```
backnd gameinfo list-rows --scope private --table "<table-name>" --gamer-id "<gamer-id>" --json
```

## 5단계 — 행 수정 및 삭제

```
backnd gameinfo update-row --help   # 플래그 확인
backnd gameinfo delete-row --help
```

삭제는 되돌릴 수 없다. `--dry-run` 옵션이 있으면 반드시 먼저 실행.

## 주의사항

- `--data` JSON 형식이 틀리면 CLI가 파싱 오류를 낸다. 따옴표 이스케이프에 주의.
- private 테이블의 `create-row`는 해당 `gamer-id`가 존재해야 한다. 없는 계정에 쓰면 실패.
- `gameinfo`(커스텀 게임 데이터)와 `database`(DB 스키마 조회)는 완전히 다른 명령. data.md 참조.
