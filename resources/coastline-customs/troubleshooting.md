# Troubleshooting

## Map missing or duplicate scenery

Confirm the installed folder and server.cfg use **gs_coastlinecustoms**. Keep the full stream/ directory and its vanilla_clear/ subfolder. Stop old gs_mechshop copies and inspect other maps replacing the same lot or vanilla assets. Restart the server after replacing streamed files and reconnect.

## Fences, debris or incorrect ground/collision

Confirm client/hide_list.lua loads before client/hide.lua and the scripts start without errors. The resource hides 75 listed vanilla objects. The streamed vanilla ground/collision replacements are also required; a hide-list edit alone cannot repair conflicting streamed collision.

## Doors do not lock or garage doors do not behave as expected

The map supplies door models and physics, not access rules. Use the [Doors](doors.md) table, configure garage versus swing behavior in your door-lock resource, and check for duplicate/conflicting door registrations. Test your configured job permissions separately.

## Interior missing, grey exterior or camera stuck inside

Keep client/viewport.lua enabled. It handles room transitions for ordinary cameras, clipping and free-camera/noclip movement. Check ordinary doorway entry/exit, roof/wall crossings and returning outside. Check for other resources forcing interior rooms or replacing this MLO. Include the camera tool and exact location in a support report.

## Signage, fans, fire or forklift missing

The effects are local to each client and appear near the lot. All gs_cc_fx assets and client/fx.lua must be present. Leave beyond 260 m and return, then check the client console. Neon flicker occurs at night; by day it is steady. The forklift waits for streamed ground collision before grounding and is intentionally undriveable.

## Ambient pedestrians or missing job NPCs

Keep client/peds.lua enabled for ambient cleanup. Scripted job NPCs should be mission entities; configure the script that spawns them accordingly. Players, mission peds, peds in vehicles and networked peds without client control are preserved. The helper restores its scenario blockers and ped paths on resource stop.

## Updating

Back up hide-list changes and external door-lock/job configuration. Replace the map and support scripts together. Keep only the new ensure entry; internal gs_cc model names remain the same.

For support, provide version **1.0.0**, the server artifact/game build, exact coordinates, client errors, conflicting maps and a reproduction. See [Support & Discord](../../support.md).
