# Troubleshooting

Start with **`/gsbc doctor`** (in game) and **`gsbc doctor`** (server console). Each line that is
not PASS has a `fix:` line under it.

| Symptom | Likely cause |
|---|---|
| `/boombox` does nothing, F8: `the interface page has not loaded` | `web/dist` is missing (installed the source instead of the release zip) or another resource broke NUI; doctor line "Interface page (NUI)" |
| Panel opens, no sound | muted (`/gsbc mute`), "My volume" at 0, FiveM muted in Windows, or doctor "Audio engine" not `running` |
| Built-in tracks play, links do not | the link is not a direct audio file, is not https, or uses AAC; the red message names the browser's reason |
| A link plays but has no direction | its host sends no CORS headers ([Audio Sources & Limits](audio.md)) |
| Others hear a different part of the song | compare both players' doctor lines "Server sync" and "Speakers in range"; a file host without range requests ([Audio Sources & Limits](audio.md)) |
| No dashboard screen in the car | `/gsbc doctor`: check the screen heartbeat, installed screen item and the model's position/rotation in `config/props.lua` |
| Arrow keys also open the phone while driving | another resource uses the same keys; rebind either one |
| "You need control access for that" | [Permissions](permissions.md) |
| Playlists gone after restart | doctor "Queue persisted"; the `data/` folder must exist and be writable |
| The phone has no *Boombox* app | doctor "Phone app (NPWD)": `npwd_gs_boombox` must be started before `npwd` and listed in `npwd/config.json`. After `ensure npwd_gs_boombox`, run `ensure npwd` and reconnect - NPWD loads its apps only when it starts.  |
| Settings > *Phone remote* says the server has no address | the server has no public web address and its computer no network address; set `Config.Remote.url` to an address the phone can reach |
| The remote opens only on the home Wi-Fi | Set `Config.Remote.url` to the public address or reverse proxy serving this resource’s HTTP handler; check TCP reachability and proxy routing. |
| The phone cannot open the remote on the same Wi-Fi | Windows Firewall blocks FXServer for private networks, or the phone is on a guest network that cannot see other devices |
| No lyrics for a song | LRCLIB has none for that title / artist / length; YouTube titles are cleaned before the search, but some still do not match |

Every line this resource prints starts with `[gsbc]`.

## Release Verification

The release includes a verification report beside the runtime ZIP. Automated checks run the real Lua in a simulator and production pages in a browser; they do not reproduce the GTA renderer, real inventory/phone deployments or audible in-game output.

Before publishing, follow [Installation](installation.md) on a real FiveM server. Check two real clients, actual audio hosts, wall/cabin muffling, screen orientation, carry animation, inventory capacity/refunds, vehicle keys, routing buckets, framework character changes and restart recovery. The publisher’s full checklist is kept beside the ZIP in `verification/IN-GAME-CHECKLIST.md`; test documentation is excluded from the installable resource. Cfx portal encryption and an entitlement test happen after local packaging.
