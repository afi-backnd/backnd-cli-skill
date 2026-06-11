# Operations Domain

Commands: `operation`, `push`, `notification`, `post`, `notice`, `alarm`, `setting`, `event`

## Non-obvious distinctions

**push vs notification — completely different targets**
- `push` — sends a mobile push notification TO PLAYERS' devices
- `notification` — sends an alert TO THE OPERATOR DASHBOARD (staff see it, not players)
- "유저에게 알림 보내줘" → `push`
- "운영팀에 알림 보내줘" → `notification`
- Getting these backwards causes real operational mistakes

**operation — NOT for in-game announcements**
- `operation` — player support & account management: 1:1 inquiries, account policy, web-links, inquiry forms
- Subcommands: `question`, `account`, `policy`, `web-link`, `inquiry-format`
- "유저 1:1 문의 확인", "계정 정책 변경" → `operation`

**notice — the actual in-game announcement command**
- `notice` — in-game announcements displayed to players inside the game client
- Subcommands: `list`, `describe`, `create`, `update`, `delete`, `emergency`
- "인게임 공지 올려줘", "공지사항 등록" → `notice create`

**post**
- In-game mail/inbox: send items or messages directly to a player's in-game mailbox
- "유저에게 아이템 보내줘", "메일 보내줘" → `post`

**alarm**
- Alert threshold configuration (e.g. "notify when DAU drops below X")
- Not a player-facing feature

## Emergency notice rules

**Three independent subcommands — each does one thing**
- `notice emergency activate` — sets isUse=true, preserves current content
- `notice emergency deactivate` — sets isUse=false, preserves current content
- `notice emergency update --contents "..." --language-contents '{"en":"..."}'` — updates content, preserves isUse

All three do a GET first (read-modify-write), so `--version` is auto-populated from the fetched state.

**`notice emergency update` — `en` key is mandatory in `--language-contents`**
- English (`en`) is the required default language and cannot be omitted
- The front-end prevents deleting the English tab; the CLI enforces the same rule
- Always pass `en` first: `--language-contents '{"en":"Server maintenance.","ko":"점검중."}'`
- If the user asks to register an emergency notice in Korean only, use `en` for the content text as well (or ask the user for an English translation) — never send a payload without `"en"`

## Announcement pattern

"공지사항 올려줘":
```
backnd notice --help           # check current subcommands
backnd notice create --help    # check required fields (title, content, start/end time)
backnd notice create --json ...
```

Always confirm start/end times with the user — a notice with a wrong end time
may stay live indefinitely.
