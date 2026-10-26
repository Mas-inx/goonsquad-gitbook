# Performance

Design rules: nobody streams what they cannot hear; nothing runs per frame unless something is
being drawn; the server does arithmetic, never polling of clients.

**Client (Lua)** - measured in the simulator, asserted by tests:

| Situation | Work |
|---|---|
| No speaker in range | one scan per `scanIntervalMs` (a distance check per known speaker). **0 draw calls, 0 messages to the browser, no per-frame thread.** |
| A speaker in range, silent | the same, plus its prop (a local object, created once) |
| A speaker audible | + one position message to the browser per `updateIntervalMs` (10/s), + at most one ray per `occlusion.intervalMs` per speaker |
| A floating visualiser in view | A per-frame thread draws a perspective-correcting 4 × 4 grid: **64 textured draw calls per display**; + `spectrumHz` (12/s) small messages. |
| Sitting in a car with the dashboard screen | The floating fallback uses an 8 × 8 grid (**256 textured draw calls**); a fitted screen uses 4 × 4 (**64**). A nearby external screen uses 2 × 2 (**16**); counts apply per screen, plus configured decoration and telemetry. |
| Keyboard control on | + 18 `DisableControlAction` + 1 `DrawSprite` per frame, only while it is on (it times out) |

The per-frame thread exists only while a quad or sprite is registered; when the last one goes, the
thread ends. `/gsbc doctor` shows how many textures are being drawn right now.

**Browser** - at most `maxVoices` audio streams per player (default 3). Linked speakers share one
stream. Out of range, the audio element is torn down (no download, no decoding). One WebGL canvas
only while a panel or a visualiser is shown; a hidden visualiser page renders nothing.

**Server** - one tick: every second, or shortly before the next track ends. Per speaker it compares
two numbers. Per connected player it reads one number a second (which instance they are in, with
OneSync); the framework is asked who a player is at most every 15 seconds, and only for players
who have a boombox out. State is pushed only to players in range of a speaker, and only when it
changes. A speaker nobody is near is paused after `idleSeconds`. The store is written at most
once per `flushSeconds` (20 s), and only when something other than a playback position changed.

**Network** - a play / pause / seek is one small event per listener in range. No audio travels
through your server: each client fetches the stream itself.

These are code and simulator budgets, not measured in-game resmon results. Actual frame time depends on hardware, visible screens, browser decoding and nearby sources. Test your intended player counts in FiveM. Keep `maxVoices`, `maxVisualizers` and other-screen limits conservative.
