# Configuration

Edit `config.lua` for locations, limits, prices, presentation and language. Restart after changing shared configuration. Keep secrets in `server/credentials.lua`, which is server-only.

## Access and Job

| Setting | Default | Purpose |
|---|---|---|
| `JobName` | `nailsalon` | Staff job identifier |
| `AutoRegisterJob` | `true` | Register configured job at start |
| `JobDefinition` | Four grades | Label, duty policy, wages and boss flags |
| `RequireOnDuty` | `false` | Require active duty for technician access |
| `PayWorker` | `true` | Credit the full accepted service amount to the technician in the customer's payment account |
| `MinCharge / MaxCharge` | `1 / 5000` | Server-validated service charge range |
| `Debug` | `false` | Additional diagnostics; no development commands are included |

This release has no salon society-payment account. With `PayWorker = false`, service charges are removed from customers without a technician payout. Modify the server bridge if your economy requires separate routing.

## Nail Packs and Custom Designs

`Config.Materialize.female` is true and `male` is false. These settings select which prebuilt catalogs are offered; they do not generate models. Both packs are included.

Leave `Config.Nails.componentId = 10`, `mode = 'addon'` and `collection = nil` for the supplied packs. Nails use the character's component-10 decal slot, so other clothing/decal systems using that component need coordination. `palette` defaults to 0.

| `Config.Custom` setting | Default |
|---|---|
| `enabled` | `true` |
| `textureSize` | `512` (valid: 256, 512, 1024) |
| `renderDistance` | `40.0` meters |
| `maxImageBytes` | `1500000` data-URL characters |
| `maxLayerBytes` | `4000000` JSON-text bytes |
| `maxThumbBytes` | `60000` data-URL characters |
| `maxDesignsPerPlayer` | `30` active personal designs |
| `livePreviewIntervalMs` | `100` |
| `liveForwardIntervalMs` | `350` |

Templates are included at high resolution and scaled by the editor. Larger texture sizes and frequent live updates increase client/server work. Personal designs are limited independently of the shared texture-slot pool.

## Chairs, Manicure and Kiosks

`Config.Manicure.stations` provides the five measured client/tech positions, targets and hand-camera points. `Config.SalonChairs` is built from them while manicure is enabled; add other chairs explicitly as needed. Each chair needs a unique string `id`, `coords`, optional `target`, radius and optional `workerChair`. `spawnChair = true` creates a chair prop; false targets existing furniture/positions.

`Config.Manicure` contains animation dictionary/clips, measured entry offsets, camera fields, brush attachment and `techStandsOnMenuClose`. Changing entry offsets alters animation alignment. The brush uses a streamed prop and a configured hand bone/transform.

`Config.Kiosks` defaults to empty. Example:

```lua
Config.Kiosks = {
    { id = 'lobby_1', coords = vector4(-590.0, -1128.0, 22.0, 90.0), radius = 0.55, targetDistance = 2.0 },
}
```

Measure your own coordinates. Reserve the `station:` id prefix for built-in manicure studios. Separate kiosks save/preview designs; customers get them applied during a technician session.

## Self-Design

`Config.SelfDesign` defaults to `apply = true`, `price = 0`, `account = 'cash'` and `presets = false`. In a manicure armchair, customers can save a personal design and apply it permanently for free. Set a nonnegative price and cash/bank account, or disable application to require a technician. Enable `presets` to expose stock sets in that studio too. This does not give separate kiosks a permanent-apply option.

## Timing, Camera and UI

- `InteractDistance = 4.0`: server distance check for chairs/kiosks and active services
- `SessionTimeoutSeconds = 300`: inactive technician session timeout
- `PaymentTimeoutSeconds = 60`: payment response window
- `StandKey = 'X'`, `StandOffset`, `SitScenario`: chair exit/fallback behavior
- `Config.Camera`: hand-bone framing, legacy offset fallback, field of view and editor framing
- `Config.UI.brandName = 'Los Santos'`: salon title
- `Config.Management.bossMenu / duty`: optional target coordinates, radius, distance and enabled flag
- `Config.Locale`: editable Lua notifications, prompts and target labels; browser editor labels are included in English

## Gestures and Appearance

`Config.NailGestures` defaults to enabled, no vehicles, and no requirement to wear salon nails. Inspect uses J / `nails`; show uses O / `shownails`. Keys are rebindable in FiveM settings. Set a gesture's command to an empty string to disable that chat command. Show locks movement and defaults to no automatic stop; `maxSeconds` can set a duration.

`Config.AppearanceEvents` lists skin-reload events that trigger nail reapplication. Add your appearance system's local client event here. Keep `MetadataKey` stable once characters have saved nails.

## Image Storage

`Config.Media.provider = 'database'` requires no external credentials. Optional values are `discord` and `fivemanage`. Credentials, hosting fallback, URL import and limits are explained in [Integrations](integrations.md).
