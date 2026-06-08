# Hero Recipe: --json 파이프라인으로 데이터 추출

사용 시나리오: 콘솔 웹 CSV 다운로드 대신 CLI `--json` 출력을 파이프라인으로 연결해 유저·매출·리텐션 데이터를 추출하고 분석할 때.

## 기본 패턴

모든 `--json` 출력은 `jq`로 바로 파이프할 수 있다:

```
backnd <command> --json | jq '<filter>'
```

`jq`가 없는 환경에서는 `--json` 결과를 그대로 받아 Claude가 파싱·집계.

## 유저 데이터 추출

전체 유저 목록 (페이지네이션 자동 처리):

```
backnd gamer list --json
```

특정 필드만 추출:

```
backnd gamer list --json | jq '[.data[] | {gamer_id, nickname, in_date, last_login, is_blocked}]'
```

차단 유저만 필터:

```
backnd gamer list --json | jq '[.data[] | select(.is_blocked == "y")]'
```

## 매출 데이터 추출

월별 결제 사용량 요약 (`--period` 필수):

```
backnd payment usage list --period total --json
# 월별 세부:
backnd payment usage list --period monthly --year 2026 --json
```

영수증 목록으로 건별 거래 내역:

```
backnd receipt list --json
```

## 리텐션·DAU 추출

DAU 시계열:

```
backnd dashboard dau --year <YYYY> --month <M> --json
```

코호트별 리텐션 (D1~D7):

```
backnd dashboard retention --date <YYYY-MM-DD> --json
```

`--date`는 코호트 시작일. 여러 날짜를 순차 조회해 이어 붙이면 장기 리텐션 분석 가능.

## 여러 커맨드 결과 합산

두 기간의 DAU를 합쳐 비교:

```
backnd dashboard dau --year 2026 --month 2 --json | jq '.data' > /tmp/feb.json
backnd dashboard dau --year 2026 --month 3 --json | jq '.data' > /tmp/mar.json
jq -s '[.[0], .[1]] | flatten' /tmp/feb.json /tmp/mar.json
```

## 주의사항

- 대량 데이터 조회 시 페이지네이션 신호를 반드시 확인하고 전체 수집 (SKILL.md의 Pagination 섹션 참조).
- `--json` 출력 구조는 CLI 버전에 따라 바뀔 수 있다. 필드명은 항상 실제 출력 결과로 검증.
- 민감한 유저 데이터(`gamer_id`, 결제 정보)를 외부 서비스로 파이프하지 않도록 주의.
