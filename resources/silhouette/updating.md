# Updating to 1.0.1

Keep the resource folder named **gs_nailsalon**. The embedded scenario path is `compcache:/gs_nailsalon/little_seoul`; renaming the folder breaks scenario registration.

## Back up the installation

Stop the resource during a maintenance window and back up its complete folder, `config/config.lua`, any access/target modifications, your external door-lock/job/venue integrations and server.cfg. The resource has no database or saved player/project data to migrate. Server status overrides are temporary and reset on resource restart.

## Replace and merge

1. Keep the existing **game build 3258** requirement for the supplied scenario list. Do not apply this scenario-list override to a different game build without a matching reviewed replacement.
2. Replace the map, `sp_manifest.ymt`, Lua helpers and `web/` together. Keep the supplied `stream/` filenames unchanged.
3. Merge your existing receptionist, access rules, target provider, venues/actions, hours, camera and locale settings into the new config. The release retains the shipped `Config.Access.mode = 'everyone'` and `Config.Debug = false` defaults.
4. Merge access and target adapter changes. These files remain editable, along with the config; the old blanket client/server escrow exceptions have been removed.
5. Start your selected target resource before `gs_nailsalon`, then restart/reconnect and check the client/server consoles.
6. Reapply any server-owned status overrides through your integration after the resource restarts.

## Changes in this delivery

- Version 1.0.1, author Goonsquad Studios, three explicit Lua escrow exceptions.
- Closing while the directory is opening cancels the pending focus/UI operation.
- Camera cleanup also removes handles waiting for the release transition to finish.
- NUI venue actions and selection callbacks check directory state and current access. Integrated server actions still need their own server-side permission/payment checks.
- An empty live venue update clears the previous quote.
- The delivery contains minified runtime JS/CSS and retained font/icon notices. The streamed map, navigation and scenario assets are unchanged.

## In-game verification

Check the map/navmesh/traffic/scenarios on build 3258; open/close the receptionist UI, cancel it during the opening delay, change venues and status overrides, and restart during a camera transition. Test ox_target or qb-target, configured character access, your custom action integrations, opening hours and waypoint arrival. With two players, confirm each gets a local receptionist/UI and shared status changes update both directories.

The directory is an information/integration panel. It does not create mechanic/beauty jobs, charge payments, lock map doors or provide physical building access restrictions.


See [Installation](installation.md), [Configuration](configuration.md) and [Troubleshooting](troubleshooting.md).
