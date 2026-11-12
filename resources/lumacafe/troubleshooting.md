# Troubleshooting

## The resource does not start

Check that OneSync is enabled, the server license key has the asset entitlement and the resource folder is named **gs-lumacafe**. Keep the manifest, map data and lift files together.

## The call panel or cabin screen has no interaction

Check that your chosen target resource started before LUMA. If you use a key prompt, set `Config.Target = 'none'` and stand close to the call button or the cabin screen. Restart `gs-lumacafe` after a provider or config change.

## A destination shows Restricted

Check the `job` rule for that floor and the character's actual job name and grade. The server reads jobs from Qbox, QBCore or ESX. Set `job = nil` to allow everyone to select that floor.

## The lift display is blank

Verify that both HTML files and the local logo are still under `lift/web/` and that `stream/` includes the lift models and textures. Approach within `Config.DuiDistance`. Try restarting `gs-lumacafe` and reconnecting if a display fails after a server update.

## Overlapping maps and water

The files in `stream/site/` replace base-game assets around the venue. If you see overlapping geometry, occlusion problems or incorrect collision, check for another map that streams files for the same area. Disable the conflicting map or reconcile the affected assets before running both.

The rooftop pool loads a water table on each client. A map that also replaces the water table can overwrite it, and stopping LUMA resets the table to the game default. Coordinate the water data if both maps need custom water.

If your own furniture or job props disappear on the lot, set `Config.HidePlacedObjects = false` and restart the resource. Built-in street-clutter cleanup still applies.
