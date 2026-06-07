# Operations Domain

Commands: `operation`, `push`, `notification`, `post`, `notice`, `alarm`, `setting`, `event`

## Non-obvious distinctions

**push vs notification — completely different targets**
- `push` — sends a mobile push notification TO PLAYERS' devices
- `notification` — sends an alert TO THE OPERATOR DASHBOARD (staff see it, not players)
- "유저에게 알림 보내줘" → `push`
- "운영팀에 알림 보내줘" → `notification`
- Getting these backwards causes real operational mistakes

**operation vs notice**
- `operation` — in-game announcement banners shown inside the game client
- `notice` — notice board entries (may be surfaced differently depending on game integration)
- Both are player-facing, but rendered in different UI contexts

**post**
- In-game mail/inbox: send items or messages directly to a player's in-game mailbox
- "유저에게 아이템 보내줘", "메일 보내줘" → `post`

**alarm**
- Alert threshold configuration (e.g. "notify when DAU drops below X")
- Not a player-facing feature

## Announcement pattern

"공지사항 올려줘":
```
backnd operation --help           # check current subcommands
backnd operation create --help    # check required fields (title, content, start/end time)
backnd operation create --json ...
```

Always confirm start/end times with the user — an operation with a wrong end time
may stay live indefinitely.
