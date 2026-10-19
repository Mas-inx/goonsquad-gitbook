# Installation

## Requirements

- A current FiveM GTA V server with OneSync enabled
- Write permission for the resource's `data/scales/` directory
- Optional Qbox, QBCore or ESX, started before this resource
- Optional oxmysql and the included SQL schema when database storage is enabled

## Install

1. Extract the `gs-scalemenu` folder into your server resources. Keep the folder name.
2. Keep `data/scales/` and its placeholder file; saving cannot create a missing directory.
3. Review `config.lua`. The default release allows everyone, costs nothing and has no default key.
4. Add the resource to your server configuration:

```cfg
set onesync on
# Start your framework here if used.
ensure gs-scalemenu
```

Open `/scalemenu` or `/scaleme` in game. The included production UI needs no npm install or build. Online Google Fonts are optional; system fonts provide a fallback.

## Optional Database

Import `sql/gs_scalemenu.sql`, ensure oxmysql before GS ScaleMenu, then set `Config.Persistence.UseDatabase = true`. The SQL table is not created automatically. If you change `Config.Persistence.table`, update the imported schema to match.

Database mode also writes local JSON as a recovery copy. Pending writes remain cached for retry if either backend fails. On load, an equal/newer JSON recovery copy takes precedence over an older SQL row. Monitor storage warnings and correct the underlying problem; unsaved memory cannot survive an abrupt process shutdown.

## Admin Permissions

```cfg
add_ace group.admin gs_scalemenu.admin allow
```

Use your server's existing principals to assign administrators. Console can use `setscale <serverId> <heightCm> [weight]` and `resetscale <serverId>`. In-game use requires the configured admin ACE. Granting `command.setscale` alone does not replace that check.

## First Check

Use two clients. Change height/body shape, verify the other player sees it, reconnect and verify persistence. Test a profile, Reset, denied access, manual apply/discard, vehicle entry/exit, first-person view and resource restart. Check camera/ruler alignment and combat behavior on the actual server.

## Asset Escrow

The manifest has two explicit Lua exceptions: `config.lua` and `shared/framework.lua`. These keep configuration and framework integration editable if the seller processes the free package through Cfx Asset Escrow. The local release is not portal encrypted. Browser assets and locales remain runtime files; the SQL schema is included for optional installation.
