# GS Announcements

A premium business announcement system for FiveM. Staff and approved business employees can create branded announcements in-game, send them immediately or on a schedule, and measure reach and engagement from a single admin suite.

**Version:** 2.6.0 | **Price:** $20.00 | [Buy on Tebex](https://store.goonsquadstudios.com/package/7626066)

{% embed url="https://www.youtube.com/watch?v=JISMSUGXW80" %}

---

## Features

### Announcement Builder
- Seven layouts: Cinematic Banner, Modern Card, Minimal Toast, Neon Pulse, Split Showcase, Breaking News, and Showcase Hero
- Business name, tagline, message, category, colors, typography, entrance animation, and duration controls
- Background, logo, and artwork media slots
- Twelve CSS background presets (including `none`) with adjustable speed and intensity
- Eleven ready-made lightshows plus custom patterns, colors, speed, and brightness
- Card scale and width controls that travel with the broadcast
- GTA frontend sounds, configurable GPS label, and optional action buttons
- Map-based waypoint picker with configured business coordinates as a fallback

### Publishing and Scheduling
- Immediate server-wide broadcasts
- One-time, daily, weekday, and weekly schedules
- Saved profiles with search, filters, duplication, status changes, and resend workflows
- Broadcast history with reach, GPS activity, stored announcement snapshots, and CSV export
- Configurable history retention limit

### Player Publishing
- Optional limited mode for regular players
- Automatic Qbox, QBCore, and ESX job detection
- Server-enforced business-name lock to the player's job
- Pending approval queue for player broadcasts and schedules
- Approve or reject submissions with a reviewer note
- Per-player pending-submission limit
- Optional direct publishing: with `Config.PlayerMode.RequireApproval = false`, whitelisted players broadcast or schedule immediately with no review step

### Access and Media Control
- Identifier or ACE-based administrator access
- In-game whitelist for jobs and individual players
- Permanent, one-time, and super-admin access tiers
- Minimum job-grade requirements
- Per-layout, background-preset, and lightshow restrictions
- Asset Library with submit, review, approve, reject, and delete flows
- Player media submissions: whitelisted players add their own backgrounds, GIFs, images, and videos, which stay pending until an approver signs them off
- Server-side approved-media enforcement

### Analytics
- Total reach, broadcasts, GPS waypoints, action clicks, and peak audience
- 24-hour, 7-day, and 30-day reporting windows
- Usage, reach, business engagement, layout usage, and best-hour breakdowns
- Deduplicated engagement per player and announcement
- Per-player event rate limits and automatic retention pruning

---

## Framework Support

| Framework | Support | Usage |
|------------|---------|-------|
| Qbox (`qbx_core`) | Full | Automatic job name and grade detection for player mode |
| QBCore (`qb-core`) | Full | Automatic job name and grade detection for player mode |
| ESX (`es_extended`) | Full | Automatic job name and grade detection for player mode |
| Standalone | Admin operation | Identifier/ACE admins can use the full suite; player job publishing requires a framework or custom job provider |

The resource is framework-agnostic outside player job detection. `ox_lib` is not required.

---

## Requirements

| Dependency | Required | Purpose |
|------------|----------|---------|
| oxmysql | Yes | Persistence, automatic table creation, paging, and analytics |
| MySQL/MariaDB | Yes | Profiles, history, schedules, access rules, assets, and engagement data |
| Qbox, QBCore, or ESX | Optional | Player job and grade detection |

---

## Quick Start

See [Installation](installation.md) for setup, then use `/gsannounce` or the default **F6** keybind to open the panel.
