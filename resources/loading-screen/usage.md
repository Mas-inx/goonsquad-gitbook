# Usage

---

## Commands

| Command | Access | Action |
|---------|--------|--------|
| `/lsbuilder` | Admin | Open/close the visual builder |
| `/lspreview` | Admin | Preview loading screen in-game |
| `lsadminadd <serverId> <label>` | Console / admin | Add player to DB admins |
| `lssetactive <identifier>` | Console / admin | Publish a profile by slug |

---

## Builder Overview

Open `/lsbuilder` to access the visual builder:

### Left Panel
- **Widgets** — browse and add widgets to the canvas
- **Content Library** — manage rules, changelog, staff, tips, keybinds, links, custom pages
- **Presets** — apply built-in or user-saved presets
- **Theme** — accent color, fonts, density, animation intensity
- **Scene** — background type (video/image/slideshow/gradient/solid), audio playlist

### Canvas
- Drag widgets to position them (percentage-based layout)
- Resize with handles
- Alignment guides and snap-to-grid
- Right-click for widget options (duplicate, lock, hide, delete)

### Right Panel
- Properties for selected widget (content, design, layout, animation, advanced)

### Top Bar
- **Save** — save draft to current profile
- **Publish** — save + set as live (players see this on connect)
- Import/Export JSON
- Undo/Redo
- Profile switcher (create, rename, delete profiles)

### Canvas Modes

| Mode | How to Activate | Behavior |
|------|----------------|----------|
| Editor | Default | Drag overlays, no widget interaction, no audio |
| Player Preview | Click play icon in top bar | Full interaction, simulated progress, audio plays |

---

## Widget Types

| Widget | Description |
|--------|-------------|
| Server Brand | Logo, server name, tagline, badge |
| Loading Progress | Progress bar, percentage, stage text |
| Audio Controls | Music player with skip/play/pause |
| Image | Static image |
| Video | Embedded video |
| Logo | Logo only |
| Rules | Rules list from content library |
| Changelog | Update history |
| Tips | Rotating tips from content library |
| Staff List | Staff roster |
| Server Stats | Stat metrics |
| Social Buttons | Discord/web/store links |
| Button Group | Custom link buttons |
| Action Button | Single button with action |
| Overlay Menu | Icon menu that opens overlays (rules, updates, staff, keybinds) |
| Keybind Showcase | Keybind display with mechanical keyboard variant |
| Queue Status | Queue position display |

---

## Content Library

Stored per-profile. Accessible from the builder's left panel:

- **Rules** — categorized rules with titles
- **Changelog** — versioned update history
- **Staff** — staff roster with names, roles, status
- **Keybinds** — key + action pairs
- **Tips** — rotating tip text
- **Links** — labeled URLs with icons
- **Custom Pages** — custom content pages with titles and body text

---

## Media

Place files in `html/media/`:

```
html/media/audio/    -- .mp3 files
html/media/video/    -- .mp4 files
html/media/images/   -- .png, .jpg files
```

The builder's media browser scans these directories automatically. For video uploads, configure FiveManage in `config.lua`.

---

## Profiles vs Presets

| Concept | Purpose |
|---------|---------|
| **Config Profile** | Full server layout saved to MySQL. One is **live** at a time. |
| **Preset** | Builder shortcut to re-apply a layout. Not what players see. |

Create multiple profiles in the builder (e.g. "default", "halloween", "event") and publish the one you want live.

---

## Queue Integration

To show queue position on the loading screen, call from any resource:

```lua
exports['gs_loadingscreen']:SendQueueStatus({
    position = 3,
    total = 10,
    estimatedWait = 45,
})
```

The queue status widget will display this data if added to the layout.

---

## Keyboard Shortcuts (Loading Screen)

Once the loading screen is interactive (continue gate or after progress completes):

| Key | Action |
|-----|--------|
| Space | Play/pause audio |
| M | Mute/unmute |
| Arrow keys | Skip track |
| Enter | Continue to game (when gate is enabled) |
