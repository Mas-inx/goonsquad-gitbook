# Usage

## Customer Service

1. Target a customer armchair and choose **Get Nails Done**.
2. A technician sits at the paired station. The session starts once both participants are seated.
3. Watch the previews on your hands through the session camera.
4. When charged, accept and choose **Cash** or **Card**, or decline.
5. Accepted payment saves the set to your character; declined, expired or cancelled sessions restore the previous nails/decal.

Press X during a session to request cancellation. While payment is open, answer its prompt. Use X or the stand target when no active session blocks leaving. Closing the technician's menu ends the session and, by default, stands the technician up.

## Technician

Staff require the configured job and, when enabled, active duty. Choose **Sit at Station** at the paired stool. On a manually configured chair without a worker stool, use **Do Nails** on the client chair instead.

The menu provides **Presets**, the salon **Lookbook** and **Client's designs**. Select a set to preview it. **Create custom design** opens the editor and forwards changes to the customer's hands. Save a new set to the Lookbook, preview the saved result, then choose **Confirm & charge** and enter an amount within the configured range.

The customer must approve the charge. Workers cannot accept it for them. A declined, expired, out-of-range or unauthorized charge does not complete service. Payments default to the full technician payout in the same cash/bank account the customer chose.

## Self-Design and Kiosks

Target a manicure armchair and choose **Design Nails**. Create and save a set under **My designs**, select it to try on, then use **Apply nails** if enabled. Default self-application is free; servers may configure a price or require technician service instead.

Closing without applying restores your previous appearance. A separate configured kiosk allows saving/preview only. Sit as a normal salon customer later so a technician can find the design in **Client's designs**.

## Editor

Choose a shape and base polish. Add ombré, French tips, patterns, stickers, text or imported images. Paint with the brush or eraser; use layers to reorder, adjust opacity, hide, duplicate and transform elements. Supported finishes include gloss, matte, chrome, glitter and pearl.

| Shortcut | Action |
|---|---|
| V | Move tool |
| B | Brush |
| E | Eraser |
| Ctrl+Z | Undo |
| Ctrl+Shift+Z / Ctrl+Y | Redo |
| Delete / Backspace | Delete selected layer when not typing |
| Ctrl+V | Paste an image or supported image link |
| Escape | Close the save popover/expanded canvas, return to the catalog, then close the menu |

Save a design with a short name. Editing a saved design creates a copy; the original remains unchanged. Personal designs belong to their creator. Staff can save salon designs and view the current customer's library during an authorized session. A design owner can delete their own sets; managers can delete salon sets. Deletion hides a set from lists while characters already wearing it retain its image.

One design texture is shared across all ten nails. Different art per individual finger and physical 3D charms are not included features.

## Gestures

- **J** or **/nails**: inspect your nails. Toggle again to stop. Switch to first-person view using your normal view control if desired.
- **O** or **/shownails**: hold your nails out for nearby players. Toggle again to lower your hands.

These are the only intentional resource chat commands in the default release. Players can rebind keys under Settings → Key Bindings → FiveM. Gestures respect login, vehicle and configured nail requirements and are unavailable while the salon UI/seating animation is active.

## Management

Configured duty targets allow staff to clock in/out when their framework exposes a duty state. Older ESX builds hide that target and count salon staff as on duty. The boss target requires manager/boss status and `qbx_management`, `qb-management` or `esx_society`, according to your framework. Personnel and society-account behavior comes from that management resource. GS Nails service payments credit the technician directly when `PayWorker` is enabled.
