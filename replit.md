# Discord Bot

A production-ready Discord bot built with Discord.js v14, featuring modular architecture, slash commands, auto-moderation, SQLite database, and a full utility suite.

## Run & Operate

- **Bot** runs automatically via the "Discord Bot" workflow
- `cd artifacts/discord-bot && node src/deploy-commands.js` — register slash commands with Discord
- `cd artifacts/discord-bot && node src/deploy-commands.js` (with `DISCORD_GUILD_ID` set) — instant guild-scoped deploy for dev

## Stack

- Node.js 24, ESM (`"type": "module"`)
- Discord.js v14
- sql.js (pure-JS WebAssembly SQLite — no native compilation needed)
- Winston + winston-daily-rotate-file for logging
- dotenv for environment variables

## Where things live

```
artifacts/discord-bot/
├── src/
│   ├── commands/
│   │   ├── general/    # ping, uptime, userinfo, serverinfo, avatar
│   │   ├── moderation/ # kick, ban, unban, mute, unmute, warn, warnings, clear
│   │   └── utility/    # poll, ticket, suggest, welcome, autorole, automod
│   ├── events/         # ready, interactionCreate, messageCreate, guildMemberAdd/Remove, etc.
│   ├── handlers/       # commandHandler, eventHandler, interactionHandler
│   ├── database/       # database.js — sql.js init, migrations, helper fns
│   ├── models/         # GuildSettings, Warning, ModLog, UserData, Poll, Ticket, Suggestion
│   ├── services/       # moderationService, automodService, pollService, ticketService, etc.
│   ├── utils/          # logger, embed, permissions, formatters
│   ├── config/         # config.js — all env-based config in one place
│   ├── index.js        # Entry point
│   └── deploy-commands.js  # Slash command registration
└── data/               # bot.db (created at runtime)
```

## Architecture decisions

- **sql.js** (pure JS WebAssembly SQLite) used instead of `better-sqlite3` because Replit's environment lacks the native build tools (Python, make, gcc) required to compile native modules.
- **Privileged intents** (GuildMembers, Presences, MessageContent) are toggled via env vars so the bot can run without them until the user enables them in the Discord Dev Portal.
- **Auto-save** on sql.js DB every 30 seconds + after every write — since sql.js is in-memory first.
- **Command loader** auto-discovers all `*.js` files in `commands/*/` directories — adding a new command just means dropping a file in the right folder.
- **Services layer** contains all business logic, making it ready to be imported by a future web dashboard API.

## Product

- **53 slash commands** across 9 categories: general, moderation, leveling, economy, music, giveaway, reaction roles, fun, utility, and admin
- Auto-moderation: anti-spam, anti-link, bad word filter, auto-mute on warning threshold
- Ticket system with private channels and modal UI
- Poll system with live vote counts via button interactions
- Suggestion system with approve/deny workflow
- Welcome/goodbye messages with custom templates
- Auto-role assignment for new members
- Per-guild configurable settings stored in SQLite

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- **Privileged intents**: Must be enabled in Discord Developer Portal → Bot → Privileged Gateway Intents (Server Members Intent, Presence Intent, Message Content Intent). Then set `INTENT_GUILD_MEMBERS=true`, `INTENT_PRESENCES=true`, `INTENT_MESSAGE_CONTENT=true` in Replit Secrets and restart the bot.
- **Slash commands**: Must be deployed once with `node src/deploy-commands.js`. Global commands take up to 1 hour. Use `DISCORD_GUILD_ID` for instant guild-scoped deploy during development.
- **sql.js DB path**: Stored at `artifacts/discord-bot/data/bot.db`. DB is auto-saved every 30s and after every write.

## Pointers

- See `artifacts/discord-bot/README.md` for full command reference and setup guide
