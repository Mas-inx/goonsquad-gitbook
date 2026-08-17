# Configuration

All server configuration is in `config.lua`. Restart `gs-annoucements` after editing it.

---

## Panel Command and Keybind

```lua
Config.Command = "gsannounce"
Config.Keybind = "F6"
```

| Option | Description |
|--------|-------------|
| `Command` | Command that opens or closes the panel |
| `Keybind` | Default FiveM key mapping; set to an empty string to disable |

Players can rebind the key in FiveM settings.

---

## Administrator Access

```lua
Config.PermissionMode = "identifiers"
Config.AcePermission = "gsannounce.admin"
Config.Admins = {}
Config.AllowConsole = true
Config.NotifyOnDenied = true
Config.LogPermissions = true
```

| Option | Description |
|--------|-------------|
| `PermissionMode` | `identifiers` checks `Config.Admins`; `ace` also checks `Config.AcePermission` |
| `AcePermission` | ACE node used in ACE mode |
| `Admins` | Identifier allow-list honored in both modes |
| `AllowConsole` | Allow source `0` to use administrative commands |
| `NotifyOnDenied` | Show a chat notification when panel access is denied |
| `LogPermissions` | Print permission decisions to the server console |

`Config.Admins` accepts a plain identifier string or a labeled object:

```lua
Config.Admins = {
    "license:abc123...",
    { id = "discord:123456789", name = "Senior Admin" },
}
```

Matching ignores case and surrounding whitespace. A raw identifier without a type prefix is compared against every identifier type.

---

## Player Mode

```lua
Config.PlayerMode = {
    Enabled = true,
    JobProvider = "auto",
    RequireApproval = true,
    MaxPending = 10,
}
```

| Option | Values | Description |
|--------|--------|-------------|
| `Enabled` | `true` / `false` | Allow non-admins to open the limited Builder |
| `JobProvider` | `auto`, `qbx`, `qb`, `esx`, `none` | Source used for the player's job name and grade |
| `RequireApproval` | `true` / `false` | `true` routes player publishes through the admin approval queue; `false` broadcasts or schedules them directly |
| `MaxPending` | Number | Maximum pending submissions owned by one player (used while `RequireApproval` is `true`) |

Automatic detection checks `qbx_core`, then `qb-core`, then `es_extended`. Players without a detected job cannot publish.

With `RequireApproval = false`, every other guard still applies: the whitelist and its role locks, the job-locked business name, the approved-media requirement, and any content restrictions. Only the review step is skipped, and the Builder relabels its publish flow accordingly.

---

## Whitelist

```lua
Config.Whitelist = {
    Enabled = true,
}
```

When enabled, a non-admin must match a player or job entry created on the **Access Control** page. When disabled, all players with a detected job can use the limited Builder. Content restrictions continue to apply in either mode.

Whitelist entries and content rules are stored in MySQL, not `config.lua`.

---

## Asset Approval

```lua
Config.AssetApprovers = {}

Config.Assets = {
    MaxPerPlayer = 40,
    MaxNameLength = 60,
    PageSize = 12,
    ShareApproved = true,
}
```

| Option | Description |
|--------|-------------|
| `AssetApprovers` | Identifier list allowed to moderate assets; empty means every admin. Reviewers using the bundled page also need admin-suite access |
| `MaxPerPlayer` | Maximum asset submissions retained per owner |
| `MaxNameLength` | Maximum submitted display-name length |
| `PageSize` | Default rows per Asset Library page |
| `ShareApproved` | Share approved assets with every Builder; when false, only the owner's approved assets are available |

Asset approvers use the same identifier formats as `Config.Admins`.

---

## Announcement Defaults

```lua
Config.Defaults = {
    duration = 8,
    position = "top-center",
    layout = "cinematic",
    volume = 70,
    bgPreset = "aurora",
    bgSpeed = 1,
    bgIntensity = 70,
    bgScrim = 55,
    scale = 100,
    cardWidth = 0,
}
```

| Option | Range / Values | Description |
|--------|----------------|-------------|
| `duration` | 2–120 seconds | Time an announcement remains visible |
| `position` | `top-center`, `top-right`, `bottom-right`, `bottom-center` | Default overlay anchor |
| `layout` | One of the seven layout IDs | Default card layout |
| `volume` | 0–100 | UI volume hint; GTA frontend sounds have fixed native volume |
| `bgPreset` | Preset ID or `none` | Default animated background |
| `bgSpeed` | 0.25–3 | Background motion multiplier |
| `bgIntensity` | 0–100 | Animated-effect strength |
| `bgScrim` | 0–100 | Darkening behind text over media |
| `scale` | 50–200 | Whole-card scale percentage |
| `cardWidth` | 320–1400, or `0` | Fixed card width; `0` uses the position default |

### Layout IDs

| ID | Display Name |
|----|--------------|
| `cinematic` | Cinematic Banner |
| `modern-card` | Modern Card |
| `minimal-toast` | Minimal Toast |
| `neon-pulse` | Neon Pulse |
| `split-showcase` | Split Showcase |
| `breaking-news` | Breaking News |
| `showcase-hero` | Showcase Hero |

### Background Preset IDs

`none`, `aurora`, `ember`, `particles`, `grid`, `scanlines`, `sheen`, `rings`, `wave`, `spotlight`, `stripes`, and `noise`.

---

## Media

```lua
Config.Media = {
    MaxEmbedBytes = 700 * 1024,
}
```

Announcement media accepts:

- Direct `https://` image, GIF, MP4, or WEBM links
- Self-hosted `media/filename.ext` resource paths
- Small `data:image/` or `data:video/` values up to `MaxEmbedBytes`

The Asset Library accepts direct HTTPS and resource media paths. It rejects embedded data URLs because reviewers need a stable, shared URL.

{% hint style="info" %}
Use `web/public/media` for reliable self-hosting and rebuild the NUI. Discord CDN URLs expire, while Google Drive and Dropbox share pages usually return HTML rather than a media file.
{% endhint %}

---

## GPS and Sounds

```lua
Config.GpsKey = 38

Config.Sounds = {
    chime_soft = { name = "SELECT", set = "HUD_FRONTEND_DEFAULT_SOUNDSET" },
    none = nil,
}
```

`Config.GpsKey` is a FiveM control ID. The default `38` is **E**. `Config.Sounds` maps Builder sound IDs to GTA frontend sound names and soundsets.

---

## Businesses

```lua
Config.Businesses = {
    {
        name = "Bean Machine",
        category = "Cafe",
        coords = { x = -634.6, y = 226.6, z = 81.9 },
    },
}
```

Businesses seed the Business Status page and supply fallback waypoint coordinates. The name comparison is exact.

---

## Scheduler, Analytics, and Paging

```lua
Config.SchedulerInterval = 30
Config.HistoryLimit = 250

Config.Analytics = {
    RefreshInterval = 5,
    RetentionDays = 30,
    MaxEventsPerMinute = 20,
}

Config.Paging = {
    ProfilesPageSize = 12,
    HistoryPageSize = 15,
    MaxPageSize = 100,
}
```

| Option | Description |
|--------|-------------|
| `SchedulerInterval` | Seconds between due-schedule checks |
| `HistoryLimit` | Maximum broadcast-history rows; oldest rows are trimmed |
| `Analytics.RefreshInterval` | Seconds between queries while the Analytics page is open |
| `Analytics.RetentionDays` | Age at which engagement events are pruned |
| `Analytics.MaxEventsPerMinute` | Per-player event rate limit |
| `Paging.*PageSize` | Default SQL page sizes |
| `Paging.MaxPageSize` | Server-side cap for page-size requests |

---

## Database and Legacy Storage

```lua
Config.Database = {
    TablePrefix = "gs_announce_",
}

Config.Storage = {
    profiles = "data/profiles.json",
    history = "data/history.json",
    statuses = "data/statuses.json",
    scheduled = "data/scheduled.json",
    prefs = "data/prefs.json",
}
```

`Config.Storage` paths are only used for the one-time legacy JSON import. After the import marker is written, MySQL is the sole persistence layer.
