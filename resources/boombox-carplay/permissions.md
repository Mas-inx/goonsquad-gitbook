# Permissions & Administration

Five tiers, lowest to highest: **listen < queue < control < manage < admin**.

| Tier | May |
|---|---|
| listen | hear it |
| queue | add tracks to the queue, remove their own |
| control | play, pause, seek, skip, volume, effect, stations, reorder, clear, load a playlist |
| manage | pick up, put away, access mode, link / unlink, visualiser |
| admin | everything, on every speaker, from anywhere |

**Boombox.** The owner holds *manage*. Everyone else must stand within `interactDistance` and gets
what the owner's access mode gives: *private* → listen, *party* → queue, *public* → control.

**Car.** The seat decides: driver *manage*, front passenger *control*, rear passengers *queue*
(change in `Config.Permissions.car`). Outside the car: listen.

**Admin.** `add_ace group.admin gsbc.admin allow`.

**Restricting who may use it at all.** `Config.Permissions.use = 'ace'` and
`add_ace group.vip gsbc.use allow`, or `use = 'job'` with `jobs = { dj = 0 }`.

Every permission is decided on the server; the interface only hides what would be refused.
A refused request is logged: `[gsbc] WARN  refused "pause" on b3 for #12 Name: needs control, has queue`.

## Administration

`/gsbc admin` opens the server-wide speaker list when `Config.Admin.enabled` is enabled and the player has the configured admin ACE. It can pause, mute or remove speakers, set a route and pause all playback. Removing a consumed-item speaker returns it through the saved refund flow.

`/gsbc carry` is an admin calibration tool for carried models. `/gsbc doctor` is an ordinary diagnostic command. The server-console `gsbc list` lists speakers. The `gsbc_*` and `+gsbc_interact` / `-gsbc_interact` registrations support key bindings.
