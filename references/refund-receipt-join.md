# Hero Recipe: 환불-영수증 조인 분석

사용 시나리오: 환불 요청 건과 원본 영수증을 대조해 처리 상태를 확인하거나 일괄 업데이트해야 할 때.

## 1단계 — 영수증 목록 추출

```
backnd receipt list --json --gamer-id <gamer-id>
# 또는 날짜 범위로:
backnd receipt list --json --start-date 2026-05-01 --end-date 2026-05-31
```

페이지네이션 신호를 확인하고 필요 시 `--next-page`로 전체 수집.

## 2단계 — 환불 목록 추출

```
backnd refund list --json --user-uuid <gamer-id>
# 또는:
backnd refund list --json --status refunded
```

## 3단계 — receipt_id 기준으로 JOIN

두 JSON 결과를 `receipt_id` (또는 `order_id`) 필드로 매칭:
- 영수증 있음 + 환불 없음 → 정상 완료 건
- 환불 있음 + 영수증 없음 → 데이터 불일치, 수동 확인 필요
- 양쪽 모두 있음 → 환불 처리 대상

## 4단계 — 환불 상태 업데이트

처리할 대상을 사용자에게 목록으로 보여주고 확인 받은 후 진행:

```
backnd refund status --json --order-ids <order-id> --status refunded
# 유효 status: refunded, notification_sent, recovered, recovery_excluded, blocked_excluded, other
# 다수 처리: --order-ids "id1,id2,id3"
```

한 건씩 처리하거나, 다수를 처리할 경우 대상 목록을 미리 보여주고 confirm 후 루프 실행.

## 주의사항

- 환불 상태 변경은 취소가 어렵다. 반드시 receipt-id를 재확인 후 실행.
- `refund` 명령어는 실제 결제 환불을 트리거하지 않는다. 스토어(Google Play, App Store) 환불은 별도 처리 필요.
