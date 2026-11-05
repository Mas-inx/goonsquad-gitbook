# Configuration & Integration

## Editable files

The map has no general config.lua. Its manifest explicitly keeps only **client/hide_list.lua** editable under Asset Escrow. This table contains 75 vanilla model/coordinate entries used to clear the site.

```lua
GS_CC_HIDE = {
    { "MODEL_NAME", x, y, z },
}
```

Keep the global table name and load order: hide_list.lua must execute before hide.lua. The hide helper uses a 1.5 m radius for each entry and restores those model hides when the resource stops. Changing the list does not change streamed collision or replacement assets. Back up your edits before an update.

The remaining client scripts support the map's effects, ambient pedestrians and viewport room handling. Core support scripts and streamed assets have no broad escrow exclusions. There is no server credential file, access list or database configuration.

## Streamed files

Keep the complete stream/ tree. The vanilla_clear subfolder contains replacement vanilla ground/collision assets needed for this site. The other vanilla-named YMAPs adjust distant lights. Their names and contents are intentional runtime replacements.

Do not rename internal gs_cc filenames, texture dictionaries or archetypes to match the public resource name. They reference each other and are also referenced by the support scripts. Start only one copy of the resource and review other maps replacing the same location or vanilla filenames.

## Doors and mechanic scripts

Register the doors with your preferred locking resource using the [model/coordinate table](doors.md). Configure job, grade and lock state there. No job, economy or access control is bundled.

Place repair/tuning targets, stashes, employee access and other mechanic interactions in the scripts you use for those systems. The lifts, tools and dyno models are map props; the map does not provide functional repair or dyno APIs. The display forklift is local, locked, frozen and undriveable.

## Runtime behavior

| Helper | Behavior |
|---|---|
| hide.lua | Hides listed vanilla objects on start and restores them on stop |
| fx.lua | Creates local effects within 220 m; removes them beyond 260 m; fans animate per frame within 90 m |
| peds.lua | Blocks ambient scenarios/paths over the lot and removes qualifying ambient peds while the player is near |
| viewport.lua | Keeps the camera/player in the correct interior rooms and releases room overrides on exit/stop |

Pedestrian cleanup preserves players, mission/script peds, peds in vehicles and networked peds this client does not control. Scenario blockers and pedestrian paths are restored on resource stop. It is not a security boundary or a job-access system.

There are no commands, keybindings, server scripts, network events or public exports to configure.
