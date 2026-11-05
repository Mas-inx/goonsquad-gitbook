# Configuration

## Editable integration files

| File | Purpose |
|---|---|
| config/config.lua | Receptionist, access, directory content/actions, hours, UI, camera, guidance and locale |
| client/access.lua | Character-gender and notification adapters |
| client/target.lua | ox_target / qb-target adapters |

These three explicit Lua paths are excluded from escrow. The core helpers and server status implementation have no blanket exclusion. There are no API credentials or database tables in this resource.

## Receptionist and camera

`Config.Receptionist.model` can be one model or an ordered fallback list; the first valid model that loads is used. The default post is vector4(-596.18, -1120.64, 22.26, 357.1). Spawn distance is 40 m, with despawn beyond 50 m while the directory is idle.

Configure the idle preset, outfit, gestures, talk/face/look behavior, target label/icon/distance and height offset. The default idle is `holdArm`. Tablet/clipboard/standing alternatives are defined in `Config.IdlePresets`.

`Config.Interaction` controls the 120 ms opening delay, player turning, local player/HUD hiding, blur and camera. The camera defaults to enabled, 40-degree FOV and a 650 ms transition. Its position is checked against nearby collision before focusing.

## Access and targets

`Config.Access.mode` is `everyone` by default; set `female` to restrict the directory interaction. `check = 'auto'` tries Qbox, QBCore and ESX data before the ped model. Set `check = 'model'` to use only the model. `hideTarget` controls whether denied players see the target option. It does not hide or lock the building.

`Config.Target` accepts `auto`, `ox_target`, `qb-target` or `none`. Start the selected provider before the map. If changing providers, restart the map to re-register its interaction.

## Directory and venue content

`Config.Directory` contains the building name, welcome text, receptionist identity, portrait, greeting and presentation. The current shipped heading is The Beauty House; all text is editable.

`Config.Services` contains five venue entries: onlyshots, clinic, nails, makeup and lounge. Entries support enabled state, title/subtitle/tag, image/icon, accent, features, quote, floor/room/location notes, coordinates, hours, badges and primary/secondary actions. Keep IDs unique and stable because status exports and NUI lookups use them.

The defaults have no venue coordinates or action buttons. Add them for your actual locations/integrations. Floor labels are hidden by `Config.UI.showFloors = false`, although entries have editable presentation levels. Directory entries do not establish functional destinations, lifts or gameplay resources.

| Action type | Required config | Behavior |
|---|---|---|
| waypoint | service.coords | Sets GPS/guidance to the configured position |
| command | command | Executes the configured client command |
| event | event, optional args | Local client event with service ID and args |
| serverEvent | event, optional args | Server event with service ID and args |
| export | resource, fn, optional args | Client export with service ID and args |

Actions can have a label, enabled flag and delay. NUI data chooses only a known service and primary/secondary slot; action implementations and parameters come from Lua config. Any server event you integrate must independently validate the player, permissions, payment and inputs. Client/UI access checks are not server authorization.

## Statuses and hours

`Config.Statuses` defines busy, closed and soon. The server exports select these keys per service and update open directories. Closed/coming-soon flags disable non-waypoint actions; waypoints still work when coordinates are present. Status flags and hours are information/action controls, not door locks.

`Config.Hours.clock` defaults to `server`; `game` uses the game clock. Format defaults to `12h`, with `24h` available; closingSoon is 30 minutes. A service can use `24h`, an open/close pair, or per-day overrides under `days.sun` through `days.sat`. A false day is closed. Overnight periods continue into the next day. Equal open and close times are treated as 24 hours. Hours labels do not automatically disable configured actions.

## UI and guidance

`Config.UI` sets tilt, floor labels, live border, defaultSelection (lounge), sorting, key hints, Backspace close, and waypoint auto-close (1400 ms). `Config.Guidance` controls GPS, sound, arrival cleanup, timeout (180 seconds) and optional marker. The marker is disabled by default. `Config.Locale` contains the interface strings.

Keep `Config.Debug = false` for release. Enable it temporarily for diagnostics only.
