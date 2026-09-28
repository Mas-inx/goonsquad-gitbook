# Installation

## Step 1: Add the Resource

1. Download `gs-djsystem` from [Tebex](https://store.goonsquadstudios.com/package/7702129) and place it in your server's `resources` directory. Keep the folder name exactly `gs-djsystem`.
2. Add the resource to `server.cfg`, **after** your framework and any optional resources (`ox_lib`, a target resource, `oxmysql`):

   ```cfg
   ensure gs-djsystem
   ```

3. Recommended: enforce a modern game build in `server.cfg`:

   ```cfg
   sv_enforceGameBuild 3095
   ```

   The DJ desks, the tintable club smoke machine, and several speaker cabinets ship with GTA updates. On older builds the resource swaps in base-game models automatically, but the stages look plainer.

{% hint style="info" %}
Keep the `data/` folder and everything inside it. Stations, playlists, history, the media index, uploads, and exports are all written there, and a FiveM resource cannot create folders that do not exist.
{% endhint %}

---

## Step 2: Grant Access

Add the aces to `server.cfg` (or `permissions.cfg`):

```cfg
# Full control: manage every station, creator, import/export, reload, scan, delete
add_ace group.admin gsdj.admin allow

# Allow a group to open the station creator and build stations
add_ace group.mod gsdj.creator allow
```

The creator can also be unlocked by job or by identifier in `config.lua`:

```lua
Config.Creator = {
    Ace = 'gsdj.creator',
    Jobs = { 'dj', 'nightclub' },
    Identifiers = { 'license:1234567890abcdef1234567890abcdef12345678' },
    ...
}
```

Who may DJ at each station is set per station, in the creator's **Manage** tab or in `Config.Stations`. See [Administration](administration.md#dj-access-rules).

Apply ace changes without a restart with `exec permissions.cfg` if you used that file.

---

## Step 3: Set Your Server Address (Uploads and Local Music)

Songs uploaded from a player's PC and files in `data/media/` are streamed from your server over HTTP, so players must be able to reach it.

- **Most servers:** leave `Config.LocalMedia.PublicBaseUrl = ''`. The address is taken from FiveM's `web_baseUrl` convar automatically.
- **If uploads hang or local songs stay silent**, set it to the address players actually connect to:

  ```lua
  Config.LocalMedia.PublicBaseUrl = 'http://YOUR.SERVER.IP:30120'
  ```

An **https** address, for example through a reverse proxy, is the fastest option: uploads go through in a single request and there are no mixed-content limits. On a plain-http address playback works normally and uploads use a slower chunked route.

YouTube and direct https audio links work without any of this.

{% hint style="warning" %}
An unregistered server reports a placeholder `web_baseUrl` such as `deprecated-xxxxxxx.users.cfx.re`, which does not resolve. The console warns about it on boot. Set `PublicBaseUrl` explicitly in that case.
{% endhint %}

---

## Step 4: Optional Database Storage

JSON files in `data/` are used by default. To store stations and playlists in MySQL:

1. Import `sql/gs_djsystem.sql` into your database.
2. Make sure `oxmysql` starts before `gs-djsystem`.
3. In `config.lua` set:

   ```lua
   Config.Persistence.UseDatabase = true
   ```

Queues, play history, and the media index stay file-based either way. If `UseDatabase` is on but `oxmysql` is not started, the resource logs a warning and uses file storage.

---

## Step 5: Optional YouTube Search

Pasting any YouTube link works out of the box and resolves the real video title. To also **search YouTube by name** and get exact track lengths, create a YouTube Data API v3 key in the Google Cloud Console and set:

```lua
Config.YouTube.ApiKey = 'your-key-here'
```

---

## Step 6: Verify the Installation

Restart the resource and check the server console. On boot you should see lines like:

```text
[gs-djsystem] framework bridge: qbox
[gs-djsystem] 3 stations ready (3 from config)
[gs-djsystem] media scan: 0 indexed (+0, -0)
[gs-djsystem] local media base: https://dj.example.com
[gs-djsystem] ready - framework qbox, 3 stations, interaction "custom"
```

Then build your first venue in game:

1. Stand where you want the venue and run `/gsdjcreator`.
2. Pick a template from the dropdown, stand where the **crowd** will be, look at the DJ spot, and press **Use stage template**.
3. Adjust anything with the gizmo, then press **Save station**.
4. Walk to the booth and press **E** (or use your target) to open the DJ panel.

{% hint style="info" %}
Three example stations ship in `Config.Stations`: Vanilla Unicorn, Bahama Mamas (needs a Bahama Mamas MLO), and Del Perro Pier. Adjust their coordinates to your map or remove them.
{% endhint %}
