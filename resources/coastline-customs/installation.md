# Installation

## Install the resource

1. Extract the package into resources/ as **gs_coastlinecustoms**.
2. Keep fxmanifest.lua, client/ and the entire stream/ directory, including vanilla_clear/.
3. Add this line to server.cfg:

```cfg
ensure gs_coastlinecustoms
```

4. If replacing the development resource, remove the old `ensure gs_mechshop` entry. Do not run both folders, or another copy of these assets, simultaneously.
5. Restart the server and reconnect. Visit **vector3(-1679.28, -769.24, 9.20)** using your existing admin tools.

There is no map-supplied teleport command, UI build, npm installation, database import or framework dependency. The manifest registers stream/gs_cc.ytyp and the resource as a map.

## Check the installation

- Inspect the lot, workshop, lounge, mezzanine, paint booth, dyno area and parts store.
- Walk and drive through the entrances; check floors, stairs, collision and garage doors.
- Check signage by day and at night, rotating ads/fans and the rooftop fire feature.
- Approach from far away, leave the area and return. The local effects spawn within 220 m and clear beyond 260 m.
- Check first-person, third-person and any free-camera/noclip transitions through the interior and back outside.
- Restart the resource with players nearby. Check that effects are not duplicated and the exterior world remains visible.

Door access is optional and configured in your existing locking resource. See [Doors](doors.md). Job scripts and their interactions are separate integrations.

## Existing development installations

Back up your own door-lock entries and any edits to client/hide_list.lua. Replace the complete runtime/map together and merge hide-list changes. The published resource name changes to gs_coastlinecustoms; the internal gs_cc models and archetype names stay unchanged.
