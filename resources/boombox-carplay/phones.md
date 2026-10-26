# Phones & QR Remotes

Phone support is optional. Playback works without a phone resource.

## In-Game Apps

`Config.PhoneApp` controls the app label, installation setting and enable switch. Adapters in `shared/phoneapps.lua` register with supported running phones: `lb-phone`, `qs-smartphone-pro`, YSeries, GKS Phone and okokPhone. Start the phone before this resource. These use the main resource's prebuilt `phoneapp.html`.

NPWD uses the separate prebuilt `npwd_gs_boombox` resource:

1. Extract its ZIP alongside your NPWD apps.
2. Start the app before `npwd`.
3. Add `"npwd_gs_boombox"` to the `apps` array in `npwd/config.json`.
4. Restart NPWD and reconnect after adding the app.

```cfg
ensure gs-boombox-carplay
ensure npwd_gs_boombox
ensure npwd
```

Do not replace NPWD's existing configuration with a partial example; add the app to its current list. The old qb-phone does not support this app registration path; use the real-phone remote instead. Provider versions and API changes can require adapting the open adapter file.

## Contacts, Messages and Calls

The dashboard's Messages app uses the configured phone's contacts and messaging APIs; supported message bridges are described under `Config.Phone` in [Configuration](configuration.md). Calls support NPWD, lb-phone, YSeries and GKS Phone through the selected provider. App registration and contact/message support are separate capabilities; installing a remote app does not add a contacts bridge to every phone. Typed messages require parking; quick replies are available in keyboard driving mode.

## Real-Phone Remote

Enable `Config.Remote` and use **Settings > Phone remote**. A QR pairing code is valid for five minutes by default and can be redeemed once. A player may pair three devices by default. Each device uses that player's server-checked permissions while the player remains connected; revoke devices from Settings.

The page is served by this resource's server HTTP handler. Set `Config.Remote.url` to an address reachable by the phone, preserving proxy routing to the handler. Without a public address the automatic LAN address only works on a network able to reach the server. Check `Config.Remote.port`, server TCP reachability, firewall and proxy configuration. A page reached over the internet should use an HTTPS reverse proxy. Never publish saved pairing/device tokens.

## Guest Party Code

The speaker owner enables **Settings > Party code**. Guests scan its QR code, view playback and queue songs. They cannot control transport. Defaults allow three queued songs per guest and 30 guests per code. Disable the code to end guest access. Guests need the same network reachability as the real-phone remote.
