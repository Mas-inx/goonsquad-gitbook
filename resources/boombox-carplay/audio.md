# Audio Sources & Limits

**How the sound is made.** GTA's own sound system can only play sounds that are packed into the
game's sound banks; it cannot play a link. So music is played by the game's built-in browser (the
"NUI" page of this resource) with the Web Audio API: one stream per speaker group, a gain for
distance, a panner for direction, a low-pass for walls and closed cars. GTA's native sounds are
used where they fit: interface feedback (front-end sounds) and the spoken station ident.

**What that gives you**

- Distance volume per surface type, left / right direction (HRTF), muffling, three effects, a real
  spectrum for the visualiser.
- Sync to the server clock, typically within a few hundredths of a second between players.

**What it cannot do - these are limits of the browser, not bugs**

| Limit | Why | What happens |
|---|---|---|
| **Direct links** to audio files or radio streams, and **YouTube** video links | a Spotify / SoundCloud *page* is a web page, not audio. A YouTube link is recognised and played by YouTube's own embedded player in a hidden frame (from youtube-nocookie.com) | other pages are refused when added, with the reason. YouTube plays with distance volume but **without direction, effects and spectrum**, and is kept in sync to about half a second; videos whose owner blocked embedding are refused |
| **https only** | the game's browser page is https and blocks plain http audio | refused when added |
| **Codecs**: use MP3, OGG, Opus, WAV or FLAC | codec availability depends on the game browser; local music imports exclude AAC/M4A | `/gsbc doctor` → "Audio engine" reports what that client can decode; verify your actual host and format |
| No `.m3u8` (HLS) playlists | not supported by this browser | use the direct stream address |
| Sites without **CORS** headers play **without direction, effects and spectrum** | the browser will not let Web Audio touch a stream whose server does not allow it | distance volume still works; the interface and the log say so |
| Sites without **range requests** cannot be skipped into | the browser can only jump inside a file if the host serves partial downloads | late joiners hear such a file out of step; warned when added, logged when it happens. Normal file hosts and CDNs do not have this limit |
| A **live stream has no position** | it is live | everyone hears what is on air; no seek, no "same second" guarantee beyond the stream's own delay |
| It is **not part of GTA's sound mix** | the browser's sound is added after the game's | the game's SFX / music volume sliders do not affect it (each player has a volume in the panel, and `/gsbc mute`); it is not affected by GTA's own reverb zones (the wall / building muffling here is this resource's approximation); recording tools that capture only game audio channels may miss it |
| Occlusion is an **approximation** | one ray per speaker a few times a second | a wall muffles; it does not model rooms |

**Who fetches the stream.** Each player's game downloads the stream itself (nothing passes through
your server), so the host of a link sees the addresses of the players who listen - as with any
web link. To allow only hosts you trust, set `Config.Sources.hostMode = 'allow'` and list them in
`allow`; to let only the people who run a speaker add links, set `guestLinks = false`; or switch
pasted links off with `directUrls = false`.

**Wording.** Lua notifications are editable in `shared/messages.lua`. Browser labels are part of the compiled UI and are not provided as editable browser source in this release.

Sound itself cannot be tested outside the game. What was tested is in [Release Verification](troubleshooting.md#release-verification).
