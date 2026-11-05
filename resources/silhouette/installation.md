# Installation

## Game build and map compatibility

The supplied `sp_manifest.ymt` targets **game build 3258**. Keep this requirement in server.cfg:

```cfg
sv_enforceGameBuild 3258
```

This is a full scenario-region-list override, not an additive file. Do not run another override of that list at the same time. A different build needs a matching reviewed list; the supplied guide warns that mismatches can crash on load or remove scenario regions.

## Install the resource

1. Extract the release as `resources/gs_nailsalon/`.
2. Keep the entire `stream/`, `sp_manifest.ymt`, `client/`, `server/`, `config/`, `web/` and notices.
3. Start your target resource first if you want the receptionist interaction, then add:

```cfg
ensure gs_nailsalon
```

Do not rename the resource: the scenario list references `compcache:/gs_nailsalon/little_seoul`. There is no npm installation, UI build or database import for the release package.

## Targets and access

`Config.Target = 'auto'` prefers ox_target, then qb-target. Start the chosen target before this resource. Without one, your own client integration can call `exports['gs_nailsalon']:OpenDirectory()`.

The default access mode is `everyone`. Optional `female` mode checks supported character data, then the ped model, or only the model if configured. This controls the directory interaction, not physical entry to the map.

## Verify

Visit the map around -590, -1126, 21.2 and the receptionist at -596.18, -1120.64, 22.26. Test map collision/rooms/doors, pedestrian and vehicle paths, scenarios, mirrors, lighting and billboard animation. Speak to the receptionist, browse all entries, close/reopen, and restart during the camera transition.

For external systems and map conflicts see [Configuration](configuration.md) and [Troubleshooting](troubleshooting.md).
