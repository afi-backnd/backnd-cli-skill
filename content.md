# Content Domain

Commands: `chat`, `popup`, `tutorial`, `console`, `account-setting`, `account-campaign`, `account-enqueter`

## Non-obvious distinctions

**chat ban vs game ban**
- `chat` ban → restricts a player from posting in chat channels only
- `blocking` → bans a player from the game entirely (see `users.md`)
- "채팅 금지" → `chat ban`
- "게임 정지", "이용 제한" → `blocking` in users domain

**popup — this is a CONSOLE dashboard popup, not an in-game notice**
- `popup` creates a popup in the Backnd operator console (staff-facing)
- For in-game player-facing announcements, use `operation` or `notice` (see `operation.md`)
- Getting this wrong means announcements go to staff, not players

**account-setting**
- Operator/admin account settings (the Backnd console account, not player accounts)
- For player account management, use `gamer` (see `users.md`)

**console**
- Image/asset upload for the Backnd console UI
- Not related to game console (PlayStation, Xbox)

**account-campaign and account-enqueter**
- `account-campaign` — marketing campaign configuration
- `account-enqueter` — survey/questionnaire management

## Common patterns

**Mute a player in chat:**
```
backnd chat --help                            # confirm ban subcommand
backnd chat ban --json --id <gamer-id> --channel-id <channel>
```

**List active chat channels:**
```
backnd chat list --json
```
