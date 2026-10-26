# Configuration

Edit the four files in `config/`; keep their Lua table structure valid and restart the resource after changing them. Preserve custom settings when replacing release files.

The release defaults are one active boombox per player, two nearby visualizers, interaction hints on, passenger mini player on and debug off. Saved screen calibration for supported vehicle models is retained.

Everything is in `config/config.lua` (sent to clients - **never put a password or key in it**),
`config/stations.lua`, `config/props.lua` and `config/server.lua` (server only).
After a change: `restart gs-boombox-carplay`, then `/gsbc doctor` re-checks the files.

### General

| Option | Default | Meaning |
|---|---|---|
| `Config.Debug` | `false` | Extra console lines for every request and sync decision. |
| `Config.Commands` | `boombox`, `carplay`, `main = 'gsbc'` | Command names. |
| `Config.Keys` | see file | Default key per action (`interact`, `carScreen`, `navUp`, `navDown`, `navLeft`, `navRight`, `navSelect`, `navBack`, `playPause`, `nextTrack`, `volumeUp`, `volumeDown`). `''` = no default. |

### `Config.Permissions` (see [Permissions](permissions.md))

| Option | Default | Meaning |
|---|---|---|
| `adminAce` | `gsbc.admin` | ACE that makes a player admin of every speaker. |
| `use` | `'everyone'` | Who may use the system at all: `'everyone'`, `'ace'` (needs `gsbc.use`), `'job'`. |
| `jobs` | `{}` | With `use = 'job'`: job name → lowest grade, e.g. `{ dj = 0, police = 2 }`. |
| `boombox.defaultAccess` | `'party'` | What strangers standing at a new boombox may do: `private`, `party` (queue), `public` (control). |
| `boombox.allowedAccess` | all three | The modes an owner may pick. |
| `boombox.interactDistance` | `3.5` | Metres within which a player counts as "at" the boombox. |
| `car` | driver `manage`, front passenger `control`, rear `queue` | Tier per seat (`driver`, `frontPassenger`, `rearPassenger`). |

### `Config.Limits`

| Option | Default | Meaning |
|---|---|---|
| `maxBoomboxesPerPlayer` | `1` | Boomboxes one player may have out. |
| `maxBoomboxesTotal` | `40` | On the whole server. |
| `maxQueue` | `50` | Tracks waiting in one queue. |
| `maxQueuePerGuest` | `8` | Of those, how many one non-owner may have waiting. |
| `maxTrackSeconds` | `10800` | Longest track accepted. |
| `maxUrlLength`, `maxTitleLength` | `600`, `80` | Length limits of pasted links and titles. |
| `maxPlaylists`, `maxPlaylistTracks` | `12`, `100` | Saved playlists per player and tracks per playlist. |
| `maxDestinations` | `12` | Saved GPS places per player. |
| `requestsPer10s` | `60` | Requests one player may send in 10 s before being refused. |

### `Config.Sources` - what may be played

| Option | Default | Meaning |
|---|---|---|
| `directUrls` | `true` | Players may paste links. `false`: built-in tracks, stations and playlists only. |
| `guestLinks` | `true` | `false`: only the people who run a speaker (its owner, the driver, the front passenger, anyone at a `public` boombox) may add links; guests in a shared queue add the built-in tracks. See "Who fetches the stream" in [Audio Sources & Limits](audio.md). |
| `allowHttp` | `false` | Plain `http://` links. Leave off: the game's browser page is https and blocks them. |
| `hostMode` | `'any'` | `'any'`: every host not in `deny`. `'allow'`: only hosts in `allow`. |
| `allow`, `deny` | `{}` | Host lists; `*.example.com` matches the domain and its subdomains. |
| `blockPrivateHosts` | `true` | Refuses localhost and LAN addresses in links. |
| `files` | `true` | The server's own music: audio files (.mp3 .ogg .opus .wav .flac) in this resource's `music/` folder are listed under **My files** in the library, with a search box. `Artist - Title.mp3` is shown with artist and title. After adding files: `refresh`, then `restart gs-boombox-carplay`. Every player downloads the folder on join. |
| `blockedWords` | `{}` | Words (not case-sensitive) that block a track when they are in its title, artist or link - e.g. `{ 'earrape' }`. Refused everywhere: links, search results, playlists, favourites, the phone. |
| `blockedVideos` | `{}` | YouTube video ids nobody may play; they are also left out of search results. |

`Config.DemoTracks` lists the built-in test tracks (`id`, `title`, `artist`, `duration` in seconds).
They are generated in the game's browser and need no internet. The three ids that have their own
tune are `neon-drive`, `low-tide` and `night-shift`.

### `Config.Sync` - keeping everyone at the same second

| Option | Default | Meaning |
|---|---|---|
| `startLeadMs` | `350` | A new track starts this far in the future so all listeners begin together. |
| `gapMs` | `400` | Silence between two queue tracks (everyone starts the next at 0:00). |
| `checkIntervalMs` | `1000` | How often a listener compares its player with the server clock. |
| `deadbandMs` | `45` | Drift below this is left alone. |
| `softMaxMs` | `1500` | Drift up to this is closed by a tiny speed change, never a jump. |
| `maxRateDelta` | `0.04` | That speed change is at most 4 % (pitch is preserved). |
| `correctOverMs` | `4000` | Target time to close soft drift. |
| `seekCooldownMs` | `5000` | Larger drift causes a jump, at most once per this long. |
| `clockSamples`, `clockResyncMs` | `5`, `45000` | Clock measurements at start, and how often it is re-measured. |
| `endToleranceMs` | `4000` | A listener's "the track ended" report is believed this close to the end. |

### `Config.Audio`

| Option | Default | Meaning |
|---|---|---|
| `updateIntervalMs` | `100` | Position / direction updates while something is audible. |
| `panningModel` | `'HRTF'` | `'HRTF'` (best on headphones) or `'equalpower'` (cheaper). |
| `listenerPosition` | `'ped'` | Ears at your character's head (`'ped'`) or at the camera. |
| `crossfadeMs` | `1200` | Fade between tracks on a skip or a station change. `0` = cut. |
| `defaultVolume`, `maxVolume` | `0.6`, `1.0` | Volume of a new speaker and the upper limit. |
| `effects` | `flat, bass, hall, lofi` | Sound effects offered in the interface. |
| `surfaces` | see below | Distance behaviour **per surface type**. |
| `occlusion` | on, `intervalMs 500`, `gain 0.6`, `lowpassHz 1800` | A wall between you and the speaker. |
| `interiorMismatch` | on, `gain 0.55`, `lowpassHz 1400` | Speaker in a building and you outside (or the reverse). |

**Surface types.** Each has `refDistance` (full volume inside it), `maxDistance` (silence, and the
point where the stream is stopped), `rolloff` (higher = falls off faster), `gain` (scales the whole
surface) and `lowpassHz` (muffling; `0` = none):

| Surface | When | Defaults (ref / max / rolloff / gain / low-pass) |
|---|---|---|
| `boombox` | a boombox on the ground | 3 m / 45 m / 1.1 / 1.0 / none |
| `boombox_carried` | a boombox in someone's hands | 2.5 m / 35 m / 1.2 / 0.9 / none |
| `vehicle_cabin` | you sit in that car (`spatial = false`: no direction) | 4 m / 8 m / 1.0 / 1.0 / none |
| `vehicle_closed` | a car heard from outside, doors and windows shut | 2 m / 18 m / 1.6 / 0.5 / 650 Hz |
| `vehicle_open` | door or window open, roof down, bikes, boats | 3 m / 40 m / 1.2 / 0.95 / none |

### `Config.Performance` (see [Performance](performance.md))

| Option | Default | Meaning |
|---|---|---|
| `scanIntervalMs` | `1000` | How often a client looks for speakers in range. |
| `maxVoices` | `3` | Streams one player may run at once; the loudest win. |
| `hysteresis` | `3.0` | Metres past `maxDistance` before a stream is dropped (no flapping at the edge). |
| `idleAction` | `'pause'` | When nobody was in range for `idleSeconds`: `'pause'`, `'stop'`, or `'keep'` playing. |
| `idleSeconds` | `180` | That time. |
| `propDistance` | `80.0` | Boombox props are created locally within this distance. |
| `maxVisualizers` | `2` | Floating visualisers drawn at once (nearest first). |

### `Config.Boombox`

| Option | Default | Meaning |
|---|---|---|
| `enabled` | `true` | Boomboxes on / off. |
| `item` | `name = 'boombox'`, `required = true`, `consume = true` | See [Inventory](inventory.md). Admins never need the item. |
| `select` | `'items'` | How a player chooses the model. `'items'`: each model is an item of its own (`item` in `config/props.lua`), bought at the speaker shop (`Config.Shop`), that puts that model down. `'picker'`: the boombox item / `/boombox` opens the model picker - every model turning in 3D, its name, what it is, how it is carried; locked ones shown locked. `'both'`: model items, and the picker for the boombox item / command. |
| `placement` | `'aim'` | `'aim'`: putting a speaker down shows where it will go (below). `'front'`: it goes straight onto the ground in front of the player (`placeDistance`), no copy. |
| `placeRange` | `4.0` | Putting a speaker down (new, from the hands, or Move) shows a see-through copy where you look - on the ground, a table, a counter - with a ring: green where it can go, red where not (too far, too steep, something in the way). The wheel turns it (Shift: finely), click / Enter / the interact key places it, right click / Backspace leaves. Looking at the edge of a table puts it on the table. **By hand**, for an exact spot: the arrow keys slide it over what it stands on (staying on a table top; off its edge it drops to what is below), Page Up / Page Down raise and lower it (up to 1.5 m above the surface, shown in cm), Shift makes both finer, R gives it back to the look. This is how far from you it can go; the server checks it too. |
| `placeDistance` | `0.9` | Where it goes when no copy can be shown (or the player went down): metres in front of the player. |
| `carry` | `true` | Pick up and walk around with it playing. |
| `removeWhenOwnerLeaves` | `true` | Put a boombox away when its owner disconnects ... |
| `ownerLeaveGraceSeconds` | `120` | ... after this long. |
| `persistPlaced` | `false` | `true`: boomboxes standing in the world survive a server restart (not ones in an instance / routing bucket: those go back to their owner). |
| `restartGraceSeconds` | `900` | With `persistPlaced`: how long a boombox that came back after a restart waits for its owner before it is put away for them. |
| `interactHint` | `true` | The "[E] Boombox" chip. |
| `target` | `'auto'` | Adds an option to ox_target / qb-target when one is running. `'off'` disables. |

### `Config.PhoneApp` - the app on the in-game phone

The Boombox app is added by itself to the phone script the server runs - **lb-phone**, **qs-smartphone-pro**, **YSeries**, **GKS Phone** or **okokPhone** - through that phone's own custom-app call, whenever this resource or the phone starts (`shared/phoneapps.lua`). NPWD has its own app resource (`npwd_gs_boombox`, section 3). Start the phone before this resource or after it: either way works. The console names a phone that refused the app.

| Option | Default | Meaning |
|---|---|---|
| `enabled` | `true` | Add the app to the phone. |
| `name` | `'Boombox'` | Its name on the phone. |
| `description` | see file | Its description in the phone's app store. |
| `installed` | `true` | On the home screen at once; `false`: players install it from the phone's app store (lb-phone, YSeries). |

### `Config.Shop` - the speaker shop

A vendor who sells the speakers as items (ox_inventory: its own shop window, its money). They
stand at `coords` with two of the speakers on show beside them and a blip on the map; the eye
target on them opens the shop. The items must be in `ox_inventory/data/items.lua` ([Inventory](inventory.md)):
one that is not is left out of the shop, with a warning in the server console.

| Option | Default | Meaning |
|---|---|---|
| `enabled` | `true` | The shop on / off. |
| `name` | `'GOONSQUAD Audio'` | Its name: the shop window's title, the blip, the eye target. |
| `coords` | Legion Square | Where the vendor stands (`vector3`; the vendor is put on the ground there). |
| `heading` | `145.0` | The way the vendor faces. |
| `ped` | `'a_m_y_hipster_01'` | The vendor's model. |
| `scenario` | `'WORLD_HUMAN_STAND_IMPATIENT'` | What the vendor does while waiting (`false`: stands still). |
| `blip` | `sprite = 136, colour = 27, scale = 0.8` | The map blip; `false` for none. |
| `display` | the party speaker and the RGB boombox | Speakers on show: `prop` (an id from `config/props.lua`), `offset` (`vector2`: metres to the vendor's right, forward) and `heading` (degrees, turned from the vendor's). |
| `items` | the five GOONSQUAD speakers | What is for sale: `{ item = '...', price = ... }`. |

### `Config.Car`

| Option | Default | Meaning |
|---|---|---|
| `enabled` | `true` | The car system on / off. |
| `brand` | `'GS Drive'` | Name shown on the screen. |
| `availability` | `'all'` | `'all'` vehicles, or `'installed'`: only vehicles where the system was fitted (stored by plate) - see `install` and `restricted`. |
| `item` | `name = 'carplay'`, `consume = true` | The item used to fit the system. |
| `install.jobs` | `{}` | Jobs that may fit and remove the system, e.g. `{ 'mechanic' }`. Empty: anyone with the item. Admins need neither job nor item. |
| `install.seconds` | `10` | How long fitting takes. Walking off, X or Backspace cancels; the item is only taken at the end. |
| `install.command`, `install.removeCommand` | `'carplay-install'`, `'carplay-remove'` | The commands for fitting and removing. |
| `restricted.enabled`, `restricted.apps` | `true`, `{ 'music', 'radio' }` | A vehicle without the system fitted (or one an admin spawned) gets only these apps instead of no screen at all. The server refuses the other apps there too. |
| `healthLog` | `30` | Entries kept in *Vehicle > Health > Log* (damage, burst tyres, overheating, low fuel and oil, repairs). |
| `blockedClasses` | `13, 21` | Vehicle classes without the system (cycles, trains). |
| `blockedModels` | `{}` | Model names without the system. |
| `openClasses` | `8, 13, 14` | Classes always "open" to the outside (motorcycles, cycles, boats). |
| `parkedSpeed` | `1.0` | m/s below which the car counts as parked. |
| `panelWhileDriving` | `true` | The car screen with the mouse stays open while driving: the game keeps the keyboard (drive as usual), the mouse works the screen, and the screen glides to a corner of its own (each player can drag and size it; remembered). Looking round, shooting, the weapon wheel and pausing are held off while it is open, and while the player types in one of its text boxes the game gets no keys at all. `false`: it closes when the car moves, and the car screen key gives keyboard control of the dashboard screen instead. |
| `passengerMouseWhileMoving` | `true` | Passengers may use the mouse screen at any speed (with `panelWhileDriving` off). |
| `focusTimeoutSeconds` | `12` | Keyboard control switches itself off after this long idle. |
| `display.world` | `true` | Draw the screen at the dashboard. |
| `display.width`, `display.height` | `1920`, `1080` | Browser size of the screen. |
| `display.offset` | `forward 0.62, up 0.36, right 0` | Where it sits, from the point between the front seats. |
| `display.size`, `display.tilt`, `display.yaw` | `0.27`, `18`, `8` | Width in metres, lean back and turn towards the driver in degrees. |
| `display.models` | calibrated `schlagen`, `cinquemila`, `rebla` entries | Per-model override, e.g. `{ adder = { forward = 0.7, up = 0.3, size = 0.25 } }`. |
| `display.flipU`, `display.flipV` | `false` | If the picture is mirrored / upside down in game. |
| `display.dock` | `x 0.5, y 0.72, width 0.42` | The HUD copy shown during keyboard control. |
| `display.style` | `'glass'` | What is drawn round the picture: `'glass'` (a floating pane of smoked glass with a lit edge and a soft glow), `'device'` (a dark housing on a mount) or `'none'`. Drawn with the picture so it always lines up. |
| `miniPlayer` | `true` | Now-playing card for everyone in the car. |
| `muteGameRadio` | `true` | Switch GTA's own car radio off when this system starts playing. |
| `units` | `'kmh'` | `'kmh'` or `'mph'`. |
| `controls` | `engine`, `lights`, `doorLock`, `doors`, `windows` on; the rest off | Vehicle controls: `engine`, `lights` (headlights), `doorLock`, `interiorLight`, `hazards`, `autopark`, `doors` (each door, bonnet and boot - also by clicking them on the drawing of the car), `windows` (front windows), `headlightColor` (xenon colour). See below. |

**The everyday switches** - `engine`, `doorLock` and `lights` - are the tiles on *Vehicle > Status*, where their state
is shown (and in *Controls*). They get along with **qbx_vehiclekeys**: the locks go through it (it keeps the lock state on
the vehicle and would undo a change made past it), and an engine it keeps off because the player has no keys is reported
as "did not start". Headlights switched from the screen stay as set until the driver uses the game's own headlight key,
which hands them back to the car.

**The other controls are off by default** because most servers already have scripts for them, and two scripts driving the
same thing fight. `headlightColor` only changes the colour of xenons a car already has - fitting xenons stays a mechanic's
job. For each control you can `enabled = true` it (the built-in action is used), or also give it an `event`:
`engine = { enabled = true, event = 'myscript:toggleEngine' }` - then the button only triggers
that client event (with the vehicle as argument) and this resource touches nothing itself.
`autopark` brakes to a stop, sets the handbrake and switches the engine off.

### `Config.Car.screens` - screens players mount themselves

Four GOONSQUAD screens (streamed from `stream/`, `gs_carplay.ytyp`): **7" portable** on a suction
mount, **10" wide** on a dash plate, **10" double-DIN** and **12" vertical** on a stand. Each is an
inventory item ([Inventory](inventory.md), sold at the speaker shop). Using it while sitting in a car shows a
see-through copy on the dashboard, seen from behind the front seats: the arrow keys move it,
W / S (or Page Up / Down) raise and lower it, Q / E turn it, the mouse wheel tilts it, Shift makes every step finer, C
switches the camera, R starts over, Enter fits it and Backspace leaves. It stays in that car (by
plate) for every player and comes back when the car spawns again.

A car with a screen in it shows its interface **on that screen** (a car without one shows no screen;
with `required = false` it shows the floating `display` above instead): the
system starts with the engine (a short GOONSQUAD start-up screen), goes dark when the engine stops,
and dims a little at night. A tall screen gets the dock along the bottom. The app opened with F7
shows on the screen too. **Other players see it live**: the nearest few screens in view run the same
interface as in that car - its music, its speed, its route, the app open on it (Messages and
Settings are shown as Home: they are the owner's). `/carscreen` (in the car) moves the screen,
`/carscreen remove` or the eye target on the car (from outside) takes it out; the item comes back.
Needs OneSync.

| Option | Default | Meaning |
|---|---|---|
| `enabled` | `true` | Screens as items on / off. |
| `fitsSystem` | `true` | With `availability = 'installed'`: a mounted screen fits the system too (taking it out takes the system out again). |
| `required` | `true` | A car shows a screen only when one is mounted in it; taken out, it is gone (F7 still opens the interface in any car). `false`: a car without one shows the floating `display` instead. |
| `others.max`, `others.distance` | `2`, `8.0` | How many other cars' screens run live at once (the nearest in view) and how near. Each is a browser page: `0` shows them dark. |
| `nightBrightness` | `0.78` | How bright the screen is between 21:00 and 06:00. |
| `bootSeconds` | `2.2` | The start-up screen when the engine starts. |
| `driverMayChange` | `true` | Besides the one who mounted it and admins, whoever drives the car may move or take out its screen. |
| `command` | `'carscreen'` | The command that moves (`/carscreen`) or takes out (`/carscreen remove`) the screen. |
| `models` | the four | `{ id, item, model, label }` each. `id` is what is saved; only models `shared/screens.lua` knows the glass of can be listed. |

`Config.Destinations` are the GPS quick destinations: `{ label, icon, x, y, z }`. Icons: `pin`,
`cross`, `shield`, `wrench`, `wave`, `plane`, `sun`, `tree`.

### The car's apps: map, road reports, navigation, messages

| Option | Default | Meaning |
|---|---|---|
| `Config.Map.enabled` | `true` | The map in the car (Maps and the map on Home). Off: Maps is a list of places. |
| `Config.Map.tiles` | Rockstar's map server | Where the map pictures come from (`{style}` `{z}` `{x}` `{y}`). The default is the GTA V map of the Social Club website, loaded by each player's game - nothing is shipped or hosted by you. |
| `Config.Map.style` | `'game'` | `'game'` (the dark map of the pause menu) or `'render'` (satellite). Players switch between them on the map. |
| `Config.Reports.enabled` | `true` | Road reports: drivers mark what is on the road where they are; everyone sees it on the map. |
| `Config.Reports.minutes` | `20` | How long a report stays. Each "still there" from another driver starts it again. |
| `Config.Reports.cooldownSeconds` | `45` | Between two reports from one player. |
| `Config.Reports.maxActive` | `60` | Reports on the whole server; the oldest goes first. |
| `Config.Reports.goneVotes` | `2` | "Gone" from this many players removes a report (the one who made it removes it alone). |
| `Config.Reports.blips` | `true` | Also show reports as blips on the game's own map and minimap. |
| `Config.Reports.kinds` | accident, police, shots, hazard, traffic | Each `{ id, label, blip, color }`: `blip` and `color` are the game's blip sprite and colour ids. Nobody is told who made a report. |
| `Config.Navigator.enabled` | `true` | Navigation (routes, turn-by-turn). |
| `Config.Navigator.anySeat` | `true` | Passengers may set the route too: it goes to the driver's GPS, with who set it. |
| `Config.Navigator.overlay` | `true` | The next-turn card on screen while driving to a waypoint. |
| `Config.Navigator.position` | `'top-left'` | Where the card sits (`top-left` `top-center` `top-right` `middle-left` `middle-right` `bottom-left` `bottom-center` `bottom-right`); players move it in the car's Settings. |
| `Config.Phone.enabled` | `true` | Messages in the car. |
| `Config.Phone.provider` | `'auto'` | The phone script Messages (in the car and on the phone apps) sends and reads texts through - `'auto'` takes the first that runs of `'npwd'`, `'lb-phone'`, `'yseries'`, `'gksphone'`; `'none'`: no Messages. Texts go through that phone, so they arrive on the other player's phone like any text, and calls are made by it. **NPWD** and **lb-phone**: contacts and every conversation, also the ones written in the phone itself (read from their tables through oxmysql). **YSeries**: no contacts (type a number); the conversations since the server started - texts sent from here and every text YSeries reports. **GKS Phone**: no contacts; the conversations sent from here since the server started. **qs-smartphone** and **okokPhone** document no way to send a text as a player: Messages says so (calls and the phone app still work). |
| `Config.Phone.maxLength` | `240` | Longest text. |
| `Config.Phone.quickReplies` | five short replies | One-tap answers - the only way to text while driving (the dashboard screen has no keyboard). |
| `Config.Phone.calls` | `true` | A call button on contacts and conversations; the call is made by the phone (NPWD, lb-phone, YSeries, GKS Phone). |
| `Config.Places.enabled` | `true` | Nearby places in Maps. |
| `Config.Places.auto` | `{ fuel = true, garages = true }` | Read fuel stations from ox_fuel and public garages from qbx_garages when they run, so they match this server. |
| `Config.Places.list` | hospitals, police, mechanics, car washes | Your own places: `{ kind, label, x, y }`, kind `fuel` `garage` `mechanic` `hospital` `police` `carwash` `shop`. |
| `Config.Parking.enabled` | `true` | Keep where each player last got out of a car they drove (with their saved data). |
| `Config.Parking.command` | `'findcar'` | Sets the route back to it. |
| `Config.Convoy.enabled` | `true` | Convoy (needs `Config.Group.enabled`). |
| `Config.Convoy.joinDistance` | `60` | How close (metres) a car must be to join a convoy. |
| `Config.Convoy.breakDistance` | `600` | A car further than this from the lead car leaves the convoy by itself. |
| `Config.Convoy.openSeconds` | `120` | How long a started convoy can be joined. |
| `Config.Convoy.maxCars` | `6` | Cars in one convoy, the lead car included. |
| `Config.BeatLights.enabled` | `true` | Beat lights (underglow with the bass). |
| `Config.BeatLights.headlights` | `true` | Also pulse the headlights, only while the car stands still. |
| `Config.BeatLights.sensitivity` | `1.0` | `0.5` calm .. `2.0` strong. |

### Stand-out features

| Option | Default | Meaning |
|---|---|---|
| `Config.Visualizer.enabled` | `true` | Floating visualiser above a playing boombox. |
| `Config.Visualizer.default`, `modes` | `'card'`; `card, off` | `card`: the floating now-playing display - a slim, wide panel whose glass is the song's own cover blurred, with the cover, the title and artist, a line of sound that moves with the music and the time (a party code, when shown, sits at its right end). `off`: nothing floats. The owner switches it per boombox (Speaker > Floating display). |
| `Config.Visualizer.height`, `size`, `drawDistance`, `resolution` | `0.95`, `1.15`, `22`, `512` | Metres from the boombox's base to the display's middle; metres wide (it is 0.42 times as tall); range; texture size (twice as wide as `resolution`). |
| `Config.Visualizer.covers` | `false` | `false`: the display never shows a song's real cover - a YouTube video's thumbnail is a still of the video, which players who stream can be flagged for - it draws an abstract cover of its own in the song's colours instead. `true`: real covers. |
| `Config.Visualizer.spectrumHz` | `12` | How often the real spectrum is sent to it. |
| `Config.Visualizer.flipU`, `flipV` | `false` | If it is mirrored / upside down in game. |
| `Config.Group.enabled` | `true` | Linking speakers (group listen, boombox to car). |
| `Config.Group.linkDistance`, `breakDistance`, `maxFollowers` | `30`, `80`, `6` | How close to link, when a link breaks, speakers per group. |
| `Config.Ident.enabled` | `true` | Spoken station ident (a GTA voice line at the speaker). |
| `Config.Ident.everyNthTrack`, `minGapSeconds`, `default` | `1`, `20`, see file | How often, and the default voice (`speech`, `voice`, `params`). |
| `Config.Sounds` | GTA front-end sounds | Interface feedback: `nav`, `select`, `back`, `error`, `open`, `close` as `{ name, soundSet }`; `enabled`. |
| `Config.Theme` | cyan / blue | `accent`, `accent2`, `dynamic` (the accent follows the cover or station that is playing, and the boombox panel is lit by the cover itself), `reducedMotion`, `stage3d` (floating 3D objects in the panels). |
| `Config.Lyrics.enabled` | `true` | Lyrics (boombox Now Playing, Music in the car, the phone remote). |
| `Config.Lyrics.provider` | `'https://lrclib.net'` | An LRCLIB-compatible service, asked by the server (free, no key). |
| `Config.Lyrics.cacheMinutes` | `360` | How long the server keeps lyrics it found. |
| `Config.Weather.enabled` | `true` | The interface follows the game's weather. |
| `Config.Weather.effects` | `true` | Players' default for the weather on the glass (raindrops, snow, mist, sun, lightning); each player can switch it in *Settings > Appearance*. |
| `Config.Weather.accent` | `'weather'` | Players' default for what the accent colour follows: `'weather'`, `'music'` (the cover / station, see `Config.Theme.dynamic`) or `'fixed'` (`Config.Theme`'s colours). |
| `Config.Remote.enabled` | `true` | The phone remote for players' real phones. |
| `Config.Remote.url` | `''` | The address phones reach the server at. Empty: the server's public web address (the `web_baseUrl` convar) - for phones anywhere, over HTTPS; the Qbox Dashboard's free *Nucleus* proxy gives one (paste its `set web_baseUrl "name.server.qbox.re"` line into `server.cfg`). The old cfx.re addresses (`*.users.cfx.re`) stopped working in June 2026 and are ignored. Without a public address: this computer's own network address, for phones on the same Wi-Fi as the server. |
| `Config.Remote.port` | `30120` | The server's port (as in `endpoint_add_tcp`), for that same-Wi-Fi address. |
| `Config.Remote.pairMinutes` | `5` | How long a QR code can be used (once). |
| `Config.Remote.maxDevices` | `3` | Phones one player can keep connected. |
| `Config.Remote.requestsPerSecond` | `8` | Per phone. |
| `Config.Admin.enabled` | `true` | The admin view (`/gsbc admin`) for players with `Config.Permissions.adminAce`. |
| `Config.Party.enabled` | `true` | Party codes: a boombox owner shows a QR code; anyone who scans it with their own phone sees what plays and can add songs (nothing else). Phones reach it like the phone remote (`Config.Remote`). |
| `Config.Party.perGuest` | `3` | Songs one phone may have waiting in the queue at once. |
| `Config.Party.maxGuests` | `30` | Phones on one party code. |
| `Config.Playlists.enabled` | `true` | A YouTube playlist link pasted into search lists its songs, to add them all or keep them as your own playlist (needs `Config.Sources.directUrls`). |
| `Config.Playlists.maxTracks` | `100` | Songs read from one playlist. |
| `Config.Showroom.enabled` | `true` | The Vehicle app shows your own car - the game's own picture of it, real paint and parts - behind the car screen while parked; drag to turn it. On the dashboard screen (and while driving) a drawing of the car shows its doors, tyres and lights. |
| `Config.Showroom.studio` | `false` | `false`: the camera circles your real car where it stands, like a car photo - the people in it hidden (on your screen only), the car lit after dark, a soft vignette inside the preview; the rest of the screen stays the plain game world and the car screen's panes are glass over it. `true`: the car is shown in a studio: a private copy of it (made on that player's game only, never seen by anyone else) high above them, with a dark backdrop, a lit floor with a soft shadow, and studio lights - every car, add-on cars too, with its paint, parts, wheels, tint, liveries, extras, neon and plate; its doors and headlights follow the real car's. A short fade covers the cut in and out. |

### `config/server.lua` (server only)

| Option | Default | Meaning |
|---|---|---|
| `ServerConfig.Storage.driver` | `'file'` | `'file'` or `'mysql'` (oxmysql). See [Storage & Updates](storage.md). |
| `ServerConfig.Storage.file`, `table` | `data/store.json`, `gsbc_store` | Where it is kept. |
| `ServerConfig.Storage.flushSeconds` | `20` | Changes are written at most this often (and on stop). |
| `ServerConfig.Storage.maxVehicles` | `400` | Car queues remembered by plate. |
| `ServerConfig.Storage.forgetAfterDays` | `0` | Saved things (playlists, favourites, recently played, places) of a player who has not been seen for this many days are forgotten at server start. `0` = keep for ever. Empty records are always tidied away; a stowed boombox or an owed item is never forgotten. |
| `ServerConfig.Security.trustClientPositionWithoutOneSync` | `true` | Without OneSync: believe clients about position. |
| `ServerConfig.Security.slackMetres` | `6.0` | Tolerance on distance checks. |
