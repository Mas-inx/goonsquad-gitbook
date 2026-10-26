# Commands & Usage

| Command | What it does |
|---|---|
| `/boombox` | Places a boombox (with `Config.Boombox.item.required`, the player needs its item; admins never do). Next to one: opens it. Carrying one: puts it down. |
| `/boombox open` | Opens the panel of the boombox you carry (or stand at) without putting it down. |
| `/boombox recall` | Brings your boombox back from wherever it stands (into your storage, queue kept). |
| `/gsbc carry` | Admins, carrying a speaker: fine-tune how that model sits in the hand, saved for everyone ([Radio & Custom Models](models.md)). |
| `/boombox <model>` | Puts down that model from `config/props.lua` instead of the default: `gs_rgb`, `gs_retro`, `gs_hifi`, `gs_party`, `gs_cylinder` (the GOONSQUAD STUDIOS speakers, streamed from `stream/`), or `boombox`, `ghetto`, `hifi`. |
| `/carplay` | Opens the car screen (sit in a vehicle). |
| `/carplay-install` | Fits the car system in the vehicle you sit in or stand in front of (`Config.Car.availability = 'installed'`). Takes the item and `Config.Car.install.seconds`; the bonnet opens while you work. Same as using the `carplay` item. |
| `/carplay-remove` | Takes the system out again. Only the player who fitted it, a mechanic (when `install.jobs` is set) or an admin can. The item comes back if one was used, and only into an inventory with room for it. |
| `/findcar` | Sets the route to where you last got out of a car you were driving (`Config.Parking.command`). |
| `/gsbc admin` | Admins: every speaker on the server, with pause, mute, remove and a route to it. |
| `/gsbc doctor` | Checks the whole system, prints PASS / WARN / FAIL to F8 and the verdict to chat. |
| `/gsbc mute` | Mutes / unmutes all boombox and car audio for you only. |
| `/gsbc` | Lists these commands. |
| `gsbc doctor` (server console) | The server half of the doctor. |
| `gsbc list` (server console) | Every speaker, what it plays, how many listeners. |

Keys (players change them in *Settings > Key Bindings > FiveM*; defaults are in `Config.Keys`):

| Key | Default | What it does |
|---|---|---|
| Use boombox | `E` | **Hold** (about half a second) to put down the boombox you carry - a tap at a door or shop does nothing to it. A tap opens the boombox you stand next to (with ox_target / qb-target running, opening is done with the eye target instead) |
| Open the boombox you carry | `U` | Opens the panel of the boombox in your hands (`Config.Keys.carriedBoombox`) - it keeps playing wherever you go. Pick one up with *Pick up and carry* on the eye target, or in the panel; hold `E` to put it down, or *Put down* in the panel. |
| Car screen | `F7` | Opens and closes the car screen with the mouse - parked or driving: while the car moves you keep driving with your usual keys, the mouse works the screen, and the screen moves to a corner of its own. (With `Config.Car.panelWhileDriving = false`: moving, it switches keyboard control of the dashboard screen on / off instead.) |
| Car screen navigation | arrows, `Enter`, `Backspace`, mouse wheel | Only while keyboard control of the dashboard screen is on |
| Play / pause, next, volume | not bound | Act on the car you sit in, else the boombox you carry or stand at |

### In the panel and on the phone

The boombox panel is laid out like a music-streaming desktop app, finished as smoked glass lit by the song that plays (its colour glows in the corners, on the open place, on what plays and on the song's progress): *Home* - "Welcome back" over a city at night tinted by the song, with what plays, the radio as picture posters and the built-in tracks - and *Search* top left, *Your Library* under them (favourites, recently played, radio, your files, the built-in tracks and each saved playlist), the page in the middle with a header in the colour of the song that plays, *Now playing* on the right (the cover, the lyrics card, **This speaker** with the 3D boombox and its *Carry* / *Put down* button, what comes next), and the player bar along the bottom (shuffle, previous, play, next, repeat, the progress bar; lyrics, queue, *Sound*, the speaker's settings and the volume on the right). Green marks only what is on.

| Feature | Where | What it does |
|---|---|---|
| **YouTube search** | *Search* (top left of the boombox panel) > the search / link box | Type words and press *Search YouTube*; pick a result to play or queue it. A pasted YouTube link (any shape) works too. The search runs on the server and needs no API key. Videos whose owner blocked embedding are refused with the reason - pick another result. |
| **Favourites** | the heart next to a track | Keeps the track under *Favourites* in *Your Library* (100 per player). Radio stations have their own star. |
| **Recently played** | Home, and *Recently played* in *Your Library* | The last 30 tracks you started, newest first. |
| **Now playing on radio** | the title of a live station | Live streams that carry titles (Icecast / Shoutcast) show the song that is on air; it is read by the server every 15 s, only for stations somebody is listening to. |
| **Crossfade** | *Sound* (the sliders button in the boombox's player bar), Settings (car) | Off (a short gap), or 2 / 4 / 6 / 8 s: the next track starts that long before the current one ends and the two fade into each other. Saved with the speaker. |
| **Sleep timer** | *Sound* (boombox player bar), Settings (car), phone | Pauses the speaker after 15 / 30 / 60 / 90 minutes. |
| **Phone app (NPWD)** | the *Boombox* app on the phone | A remote control for the car you sit in, the boombox you carry, or your own boombox wherever it stands, laid out like a music-streaming app (Home, Search, Your Library, a mini player and a full Now Playing): play / pause / skip, shuffle / repeat, volume, queue, YouTube search and playlists, radio, favourites, saved playlists, lyrics, sleep timer. |
| **Phone app (lb-phone, qs-smartphone-pro, YSeries, GKS Phone, okokPhone)** | the *Boombox* app on the phone | The same app as the NPWD one, added to whichever of these phones the server runs - by itself, nothing to set up (`Config.PhoneApp`). Each phone shows it the way it shows its own apps; it talks to this resource directly. The old qb-phone has no way to add apps: its players use the phone remote below. |
| **Phone remote (your real phone)** | Settings > *Phone remote* | Scan the QR code with your own phone's camera: a remote opens in its browser (nothing to install; *Add to Home Screen* makes it an app). It controls the same speaker as the NPWD app, with exactly your in-game permissions, only while you are on the server. Up to three phones per player; remove one in Settings. The page is served by this resource itself: from anywhere when the server has a public address, otherwise on the same Wi-Fi as the server's computer (see `Config.Remote`). |
| **Lyrics** | the *Lyrics* button (Now Playing, and Music in the car) | The words of the song in time with it, the line being sung bright; tap a line to go to it. From LRCLIB (free, no key), looked up by the server. Plain text where there is no timing; none for live radio. |
| **Weather** | everywhere, *Settings > Appearance* | The accent colour follows the game's weather where you are (the music, or the server's colours, if you prefer), and a light touch of it shows on the glass: raindrops, snow, mist, the sun, a flash of lightning. Switch the effects off in *Appearance*; *Less motion* makes them still. |
| **Find your car** | Settings > *Your car*, the phones, `/findcar` | Where you last got out of a car you drove, and a route back to it. |
| **Party code** | *Settings > Party code* (the owner) | A QR code on the boombox's floating screen and in Settings: guests scan it with their own phones, see what plays and add songs (a few each, nothing else). |
| **YouTube playlists** | the search box | Paste a playlist link: its songs are listed, to add them all to the queue or keep them as your own playlist. |
| **Admin view** | `/gsbc admin`, or *Settings > Admin* (admins) | Every boombox and car speaker on the server: what it plays, who owns it, how many listen; pause, mute for everyone, remove (a boombox goes back to its owner's storage, item refunded), set a route to it, pause everything. |

**The phone app** is its own small resource, `npwd_gs_boombox`, built by `npm run build:phone` into
`resources/[npwd-apps]/`. It must start before `npwd` (it does when `[npwd-apps]` is ensured before
`npwd`) and its name must be in the `"apps"` list of `npwd/config.json`. Servers without NPWD
simply do not install it; nothing else depends on it. NPWD loads its apps when it starts: after
starting the app by hand, run `ensure npwd` too and reconnect. `/gsbc doctor` checks all of this.

### In the car

| App | What it does |
|---|---|
| **Home** | What plays (cover, controls), a live map of where you are with the next turn, speed and fuel, the weather now and what it is turning into. |
| **Maps** | GTA V's own map (dark map or satellite), with you as an arrow that follows the car. Drag, zoom with the wheel or the buttons; click anywhere for the street there and *Drive here* (a passenger's *Send to driver* sets the driver's GPS). *Places* lists the quick destinations and your saved places; *Report* marks an accident, police, shots fired, a hazard or traffic where you are. Reports show on everyone's map (and as blips on the game's map) until they expire; click one for *Still there* / *Gone*. |
| **Turn-by-turn** | While a waypoint is set, a banner on screen (also with no panel open) shows the next turn the way Apple Maps does: its arrow, how far it is, the street it leads onto, a second turn close behind ("Then turn left"), and the time and distance still to go. The turns are read off the very route the game's GPS draws on the map, so the banner and the line always agree; a bend that keeps to the same street is not announced. Players hide the banner or move it to any of eight places in *Settings > Driving*. |
| **Messages** | Your phone's contacts (favourites first, search, or type a number) and your conversations; texts go through the phone, so the other person gets them on their phone. While driving (dashboard screen) the quick replies are the only way to answer. |
| **Vehicle** | With the mouse while parked, the game's camera steps out and shows your own car - its real model, paint and parts - through the screen: drag to turn around it, scroll to zoom, and the door, bonnet and boot buttons open them on the car you see. On the dashboard and while driving, a drawing of the car from above in its paint shows the open doors, bonnet and boot, burst tyres and the lights. *Status*: speed, gear, fuel, engine and body, and the everyday switches - engine, doors (locks) and headlights - as tiles that are lit while on. *Health*: engine temperature, oil, fuel tank, body, tyres, dirt, and a log of what happened to the car. *Controls*: what the server allows (see `Config.Car.controls`), the windows and the xenon colour. |
| **Car playlists** | *Music > This car*: playlists saved in a car stay with that car whoever drives it; *From your other cars* imports one. A playlist opens to show its tracks, each with a button to take it out (your own playlists too). |
| **Screen window** | With the mouse, the car screen can be dragged by its bezel and resized from its corner; a double click on the bezel puts it back. It has two places - parked (large, in the middle) and driving (smaller, in a corner, nothing dimmed) - and glides to the second when the car starts to move. Both remembered on each player's PC. |
| **Fitting** | With `availability = 'installed'`, a car without the system offers music and radio only; a mechanic (or anyone, see `Config.Car.install.jobs`) fits it with the `carplay` item or `/carplay-install`, standing at the bonnet or sitting in the car. |
| **Nearby places** | *Maps > Places*: fuel stations, garages, mechanics, hospitals, police and car washes, nearest first; one tap sets the route. Fuel stations and garages are this server's own (read from ox_fuel and qbx_garages). Your parked car is there too. |
| **Convoy** | *Music > This car*: the driver starts a convoy; drivers of cars nearby join it from the same place, and every car in it plays the same song at the same second. A car that falls far behind leaves by itself. |
| **Calls** | *Messages*: the phone button calls a contact through the selected supported phone (NPWD, lb-phone, YSeries or GKS Phone). |
| **Beat lights** | *Vehicle > Controls*: the car's underglow pulses with the bass of what plays (and the headlights, while it stands still). Everyone near the car sees it. Needs underglow fitted. |
| **Lyrics** | *Music*: the lyrics button shows the words in time with the song. |

## Fitted Screen Commands

| Command | Action |
|---|---|
| `/carscreen` | Reposition the screen fitted in the current car |
| `/carscreen remove` | Remove it and return its item, subject to permissions and inventory capacity |

Use a screen item to begin fitting. Arrows move it; W/S or Page Up/Page Down raise/lower it; Q/E rotate; the wheel tilts; Shift makes adjustments finer; C changes the camera; R resets; Enter confirms and Backspace cancels.

## Default Workflow

Buy/use a speaker item, aim its placement and confirm it. Open it using the target or proximity key, then choose a track, radio station or permitted URL. Nearby guests can queue tracks in party mode; the owner controls playback. Carry it from the panel, open it with U while carrying, and hold E to put it down. Put it away to return its consumed item and retain its queue.

For cars, F7 opens the interface even without a physical screen. A mounted screen adds the world display. With `availability = 'installed'`, a kit or a screen with `fitsSystem = true` enables the full system; other vehicles receive only the configured restricted apps.

Administrative actions remain protected by `gsbc.admin`. There is no public screen-test command in this release.
