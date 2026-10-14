# Troubleshooting

## Resource Does Not Start

Check that `oxmysql`, `ox_lib`, `qbx_core` and `ox_target` are started, OneSync is enabled, and the server supports Node 22. Keep the resource named `gs_nails`. Use the portal-delivered asset associated with your server account for an escrowed purchase.

## No Catalog or Nails

Verify `generated/nail_catalog.json`, `generated/shapes/`, both apparel metadata files and `stream/` are present. The release does not generate missing files at startup. Female is enabled by default; male requires `Config.Materialize.male = true`. Use a supported freemode ped.

If the stream pack was replaced while the server was running, perform a full server restart and reconnect. Ensure the collection/component configuration still matches the supplied packs. Avoid another clothing resource repeatedly writing component 10.

## Chairs, Hands or Brush Do Not Align

The default stations expect the separate Fable nail salon interior. Check that map, then measure the client/tech seat coordinates, headings, entry offsets, targets and hand-camera points. Other furniture can require different alignment. Confirm `stream/anim/` contains both animation dictionaries and the brush prop/type definition.

## Staff Cannot Open the Menu

Check the active job name/grade, optional duty requirement and worker-chair reservation. A worker must use their station while the customer is seated in its armchair. A customer using **Design Nails** reserves the chair for self-design and is not available for a technician session until leaving.

If automatic job registration is disabled, define the job in Qbox before assigning it. Check console output for job-registration failures. Inspect-only gestures do not grant technician access.

## Boss Menu or Duty Target

Verify `Config.Management` coordinates and enabled fields, manager/boss flags and the optional `qbx_management` resource. Defaults are near the salon; verify their exact in-game placement. The target must belong to your installed interior, and the player must have the nail job.

## Payment Fails or Does Not Finish

The customer must have enough money in the selected cash/bank account, respond before timeout and remain near the station. The worker needs the job/duty permissions. Charges require a valid preview and finite amount within bounds. A payment token is usable once.

Check bridge return values if you adapted the economy. A failed payout/metadata save attempts a customer refund and restores the previous nails. A logged refund failure needs staff investigation in your economy. `PayWorker = false` intentionally removes the service amount without a worker payout.

## Custom Designs Will Not Save

Check database permissions and the automatically created table, personal-design count, valid shape/gender and upload/layer/thumbnail size limits. Save from an active studio or technician session. Salon-scope designs require staff access. Broken editor JSON is rejected.

Deleting a design removes it from visible lists and the active personal count; existing wearers retain it. Editing creates a copy and consumes another personal slot when saved personally.

## Empty Lists or Texture Capacity

An empty new personal/Lookbook library is normal. Texture slots are leased by distinct active designs and by live previews, per shape/gender. Many saved designs do not reserve slots until used. If a shape is full, finish previews, change shape or wait for users of other designs to leave. Increasing the personal save limit does not increase streamed slots.

## Hosted Images or URL Imports

Database storage is the easiest initial setup. For a host, verify provider credentials and inspect upload/fallback messages. Preserve Discord webhook messages and the webhook needed to refresh their attachments. A host timeout may prevent an old hosted design from being fetched even though its SQL row still exists.

Image-link imports require an active studio, PNG/JPEG/WebP content, a public destination, valid redirects and a file within the configured cap. Loopback/LAN/link-local, mapped IPv4-in-IPv6 and other nonpublic forms are rejected. Use a direct image link, or import the image locally.

## UI or Gesture Issues

Keep the supplied `web/dist` folder intact. Browser labels are English. Fonts use a hosted stylesheet with normal fallback fonts if unavailable. Use F8/console diagnostics and check for missing assets; no customer UI build step is needed.

J and O are default gesture keys. Existing players can have saved bindings; change them under FiveM key settings. Gestures are disabled while using the salon UI/seat animation and, by default, in vehicles. Check `requireNails` and `enabled` if they do not play.

## Support

Provide version 1.0.0, server/framework versions, relevant console/F8 errors, configured interior/gender pack and clear reproduction steps. Remove credentials from anything shared. See [Support & Discord](../../support.md).
