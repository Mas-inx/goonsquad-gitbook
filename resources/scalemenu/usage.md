# Usage

## Open the Menu

Use `/scalemenu` or `/scaleme` while alive, standing and outside a vehicle. Administrators may configure a key; the default is unbound. Restricted players see the translated reason. Escape closes that screen.

Drag the clear stage area to orbit the camera and use the mouse wheel to zoom. Reset Camera restores the configured view.

## Height and Shape

Adjust sliders, numeric height fields or increment buttons. Switch between centimetres and feet/inches. Use Base restores the model's base height without forcing the body-shape index. Linked mode makes height changes adjust body shape too.

The translucent ghost shows your last confirmed size, while the live readout and ruler show the current rendered size. Actual centimetres depend on the configured model base and factor clamps.

With Live Apply enabled, releasing a control sends the change to the server. With it disabled, changes stay a local preview until Apply. Closing a dirty manual preview offers Apply or Discard. A rejected change restores the last confirmed record.

## Presets and Profiles

Presets apply a configured height/body-shape combination. Random picks a combination within configured slider bounds. Reset restores the model's unscaled height and default body shape; the Normal preset is a separate configured **180 cm / 50** combination.

Save a named profile from the current values, load it later, or delete it. Reusing a name without regard to capitalization updates that profile. New profiles stop at the configured limit. Applying a preset/profile still requires server access and any enabled in-game charge.

## Settings and Persistence

Settings include language, units, linking, ghost, ruler and smoothing. UI preferences are stored in client resource KVP; confirmed size and profiles use the server's character record. Framework character switches load separate records. Standalone follows the selected stable player identifier.

## Administrator Commands

| Command | Purpose |
|---|---|
| `/setscale <serverId> <heightCm> [weight]` | Set an online player's values, within hard limits |
| `/resetscale <serverId>` | Restore an online player's normal size |

These require the configured admin ACE or server-console access. No test/debug command is shipped. Values set by trusted admin/server integrations bypass self-service restrictions and optional charges.
