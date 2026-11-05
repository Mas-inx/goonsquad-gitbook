# Troubleshooting

## Crash or missing scenarios on load

Confirm game build **3258**, the complete supplied sp_manifest.ymt and the unchanged **gs_nailsalon** folder name. Its scenario list targets that build and references compcache:/gs_nailsalon/little_seoul. Check for another scenario-region-list override. A renamed folder or unmatched list/build needs correction before further testing.

## Map, collision, lighting or navigation conflicts

Keep the complete stream folder. This delivery intentionally overrides Korean Plaza map blocks, collision/ground/distant models, occlusion and distant-light files, navmesh[105][96], navmesh[108][96], nodes430, little_seoul.ymt and sp_manifest.ymt. Disable or properly merge other resources replacing those same assets; do not run duplicate copies of this map.

## Receptionist or interaction missing

Visit the configured post and check client errors, the ordered model fallbacks, spawn distance and height offset. The NPC is local per client. Start ox_target or qb-target before the map, verify Config.Target, and restart the map after changing providers. Without a target resource, open through the documented client export.

## Directory denied or missing buttons

Default access is everyone. In female mode, verify character gender data or the model check and target visibility settings. These rules apply to the directory, not building entry.

No buttons or coordinates are configured on default entries. Add primary/secondary action definitions and coordinates as needed. Closed/coming-soon states disable non-waypoint actions; waypoint actions require coordinates. Validate server handlers in the integration itself.

## Wrong directory branding, venue names or hours

Edit Config.Directory, Config.Services and Config.Locale. Default entries are editable server examples and do not install the listed gameplay packages. Floor labels are intentionally hidden. Check Config.Hours.clock, the server timezone/game clock, weekday overrides and overnight periods. Hours labels alone do not close doors or disable actions.

## Camera, player/HUD visibility or focus remains after closing

Install the complete 1.0.1 Lua/UI update together. This version cancels pending opens and tracks cameras waiting to finish their release transition so stop cleanup can remove them. Test rapid open/close, reopen, and resource restart during transitions. Check other scripts that own NUI focus or scripted cameras.

## Status override lost after restart

Overrides are runtime state, not persistent storage. Reapply them from a trusted server integration after the map starts. Check service IDs/status keys and the export's boolean result.

For support include version 1.0.1, server artifact/game build, map conflicts, target/framework provider, config changes, console errors and reproduction steps. See [Support & Discord](../../support.md).
