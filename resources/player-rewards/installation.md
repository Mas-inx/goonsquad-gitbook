# Installation

## Step 1: Add to Server

1. Copy the `gs-playerrewards` folder into your server's `resources` directory.
2. Add to `server.cfg`:
   ```
   ensure oxmysql
   ensure ox_lib
   ensure gs-playerrewards
   ```

## Step 2: Requirements

- **OneSync** must be enabled:
  ```
  set onesync on
  ```
- **oxmysql** — database connector
- **ox_lib** — client-side notifications and URL opening

## Step 3: ACE Permissions

Set up admin access for Overseer and commands:

```
add_ace group.admin playerrewards.admin allow
add_principal identifier.fivem:YOUR_ID group.admin
```

## Step 4: Configuration

Edit `config.lua`:

```lua
Config.Framework = 'auto'  -- or 'esx', 'qb', 'qbx'
Config.AdminIdentifiers = { 'fivem:YOUR_ID' }
```

See [Configuration](configuration.md) for the full reference.

## Step 5: Discord Integration (Optional)

Configure in `config.lua`:

```lua
Config.Discord = {
    BotToken = 'YOUR_BOT_TOKEN',
    GuildId = 'GUILD_ID',
    PrimeRoleId = 'ROLE_ID',
    AutoGrantFromRole = true,
}
```

Or via server convars:

```
setr playerrewards:discord_bot_token "BOT_TOKEN"
setr playerrewards:discord_guild "GUILD_ID"
setr playerrewards:discord_role "ROLE_ID"
```

## Step 6: First Start

On first start, the resource seeds default data from `install/default_season.json` and `install/default_settings.json`. A default season (Season 1, 20 tiers, 40 rewards) is pre-loaded.

To reset to defaults at any time (from server console):
```
playerrewards_resetdefaults
```

---

## Troubleshooting

**Ledger doesn't open (F7):**
- Ensure `ox_lib` is started.
- Check the keybind in `Config.OpenLedgerKeybind`.
- Run `/playerrewards` manually.

**No rewards showing:**
- The season is empty — configure in Overseer and click **Push live**.
- Run `playerrewards_resetdefaults` from console to seed default season.

**Overseer tab missing:**
- You need ACE `playerrewards.admin` or be in `Config.AdminIdentifiers`.
- Run the fix from Step 3 and restart.

**Hours not counting:**
- Verify `Config.Playtime.Enabled = true`.
- Sessions under `MinimumSessionSeconds` (default 60s) are ignored.
- Check oxmysql connection.

**Prime purchase not working (Tebex mode):**
- Set the Tebex URL in Overseer under **Season + Relay**.
- Discord must be configured.
- Player must have Discord linked and hold the configured role.

**Prime purchase not working (in-game mode):**
- Ensure Tebex is disabled in Overseer.
- Set the in-game price in Overseer under **Season + Relay**.
- Players need enough cash or bank balance.
