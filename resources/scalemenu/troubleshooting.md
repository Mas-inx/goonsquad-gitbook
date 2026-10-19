# Troubleshooting

## Menu Does Not Open

Keep the folder named `gs-scalemenu`, ensure it starts and enable OneSync. Use `/scalemenu` or `/scaleme`. No default key is assigned. Stand alive outside vehicles; inspect `Config.Enabled` and the translated access-denial reason.

## Restricted Access or Missing Roles

Enabled identifier, job, Discord and gender checks all apply. Start your framework first. A nonempty role list fails closed without a working provider. Configure gender model tables or framework data; the optional client hint is not authoritative. Admin commands require `gs_scalemenu.admin`, not only `command.setscale`.

## Size or Profiles Do Not Persist

Keep `data/scales/` present and writable. Check console storage warnings. Import the schema and start oxmysql before enabling database mode. Failed writes remain pending for retry, so repair storage rather than repeatedly restarting. Back up both JSON and SQL. Changing framework or character identifier can point to a different record.

## Height Looks Different from the Number

Height is derived from configured base height and a clamped visual factor. Calibrate `Height.modelBaseCm` for addon peds. Body shape scales the whole rig, including the head. Reset restores model base height, which can differ from the Normal preset.

## Normal Size in a Vehicle or First Person

This is the default behavior. Vehicle seat/ragdoll physics do not scale with the matrix. First-person `unscaled` preserves the normal local camera/weapon view while other players keep seeing the configured scale. Experimental modes require in-game testing.

## Combat Alignment or Damage Conflicts

Capsule height is unchanged. Test player instant-hit weapons with your damage/death/armor resources. Temporarily configure `debugDraw = true` to compare silhouettes, then disable it. Confirm `boneSpace` aligns with your build. There is no public hitbox-debug command. Disable bullet correction when another combat resource needs exclusive damage control. See [Integrations](integrations.md) for report validation and limits.

## Camera, Ghost or Focus Remains

Close using Escape/Done or the client `CloseMenu` export. Check for another resource controlling cameras/focus. Test resource stop/restart and character logout. The resource cleans up its owned ghost, camera and temporary proofs.

## UI or Language Problems

Keep the HTML, CSS, production JavaScript bundle and all six locale JSON files together. Clear the relevant client cache after replacing assets. Fonts fall back to system fonts if Google Fonts is unavailable. Preserve locale key names and placeholders such as `{height}`. Restart after configuration changes.
