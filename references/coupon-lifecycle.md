# Hero Recipe: 쿠폰 캠페인 생성 및 관리

사용 시나리오: 이벤트 쿠폰 캠페인을 처음부터 만들고, 발급된 코드를 확인하고, 사용 현황을 추적할 때.

## 1단계 — 캠페인 생성

```
backnd coupon create --help
```

주요 옵션 확인 후 캠페인 생성:

```
backnd coupon create \
  --name "<campaign-name>" \
  --coupon-type serial \
  --count <number> \
  --end-date "YYYY-MM-DD"
```

`serial` 타입은 코드가 각 쿠폰마다 고유하게 생성됨. `single` 타입은 단일 코드를 여러 명이 사용.
코드 생성은 비동기로 처리되므로 즉시 사용 가능하지 않을 수 있다.

## 2단계 — 발급 확인

생성 후 목록에서 캠페인 ID 확인:

```
backnd coupon list --json
```

offset 기반 페이지네이션 — 반환 건수가 `--limit`보다 적을 때까지 `--offset`을 증가시키며 전체 수집.

## 3단계 — 코드 목록 조회

캠페인 ID로 발급된 코드 전체 확인:

```
backnd coupon usage list --json --coupon-id <coupon-id>
```

## 4단계 — 코드 유효성 검증

배포 전 특정 코드가 사용 가능한 상태인지 확인:

```
backnd coupon validate --code <code>
```

## 5단계 — 사용 현황 모니터링

캠페인 진행 중 사용률 추적은 `coupon-stats.md` 참조.

## 주의사항

- 생성 직후 `coupon usage`를 바로 호출하면 코드 목록이 비어 있을 수 있다. 잠시 후 재조회.
- `--count`는 serial 타입에서 발급 코드 수를 의미하며, general 타입에서는 무의미.
- 쿠폰 삭제(`coupon delete`)는 미사용 코드 전체를 무효화한다. 반드시 `coupon list --json`으로 대상을 확인한 뒤 사용자 확인 후 실행할 것 (SKILL.md 고위험 명령 섹션 참조).
