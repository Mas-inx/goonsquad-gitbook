# Configuration

All configuration is in `config.lua`.

---

## Commands

```lua
Config.AdminCommand = 'lsbuilder'     -- Open builder
Config.PreviewCommand = 'lspreview'   -- Open in-game preview
```

---

## Admin Access

```lua
Config.AdminIdentifiers = {}
Config.UseDatabaseAdmins = true
```

| Option | Description |
|--------|-------------|
| `AdminIdentifiers` | Array of license/discord/steam identifiers with builder access |
| `UseDatabaseAdmins` | Also allow admins from `gs_loadingscreen_admins` DB table |

---

## Default Profile

```lua
Config.DefaultConfigIdentifier = 'default'
```

Slug for the default config profile created on first start.

---

## Loading Stages

```lua
Config.LoadingStages = {
    "INITIALIZING", "CONNECTING", "AUTHORIZING", "DOWNLOADING",
    "LOADING", "FINALIZING", "SYNCHRONIZING", "READY"
}
```

Labels shown beneath the progress bar during loading.

---

## Queue Simulation

```lua
Config.EnableQueueSimulation = false
```

Shows a fake queue countdown for testing the queue status widget.

---

## Continue Gate

```lua
Config.RequireEnterToContinue = true
Config.ContinueTimeoutMs = 120000
Config.LoadingCompleteDelayMs = 500
Config.ContinueFadeMs = 600
Config.AutoCloseDelayMs = 10000
```

| Option | Description |
|--------|-------------|
| `RequireEnterToContinue` | Show "Session Ready" and wait for Enter before spawn |
| `ContinueTimeoutMs` | Auto-close after timeout (ms) |
| `LoadingCompleteDelayMs` | Delay after 100% before continue screen |
| `ContinueFadeMs` | Fade duration after Enter (ms) |
| `AutoCloseDelayMs` | Auto-close when Enter gate is disabled |

---

## Config Size Limit

```lua
Config.MaxConfigBytes = 524288
```

Max JSON size for configs (512KB). Large media should use URLs, not base64.

---

## FiveManage (Video Uploads)

```lua
Config.FiveManage = {
    ApiKey = '',
    -- Additional settings for video hosting
}
```

Required for uploading videos from the builder to FiveManage CDN. API key stays server-side.

---

## Debug

```lua
Config.Debug = false
```
