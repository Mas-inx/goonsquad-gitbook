# Usage

This page covers the player, designer, and admin workflows in GS Cloth Designer 1.3.0 RC.

---

## Open the Designer

Players with a configured job and grade can approach a designer station and press the interaction key. The default station is at:

```text
-1194.96, -767.87, 17.32
```

A server owner can also open the UI through an integration:

- `openDesigner()` opens the normal job-validated studio.
- `summonDesigner(componentAmount, aiGenAmount)` opens a limited session for the local player.
- The matching server export opens a limited session for a target server ID.

See [API & Exports](exports.md#limited-designer-access).

---

## Create a Design

1. Choose male or female and select a clothing or prop category.
2. Select an available silhouette.
3. Add drawing, shape, text, image, or externally registered studio-tool layers.
4. Reorder and transform layers until the canvas and preview match the intended result.
5. Save a draft or print the finished design.

The library only mounts previews close to the visible scroll area. Off-screen preview images and 3D models are released as the player scrolls, which keeps large template libraries responsive.

### Image Uploads

The UI accepts PNG, JPEG, and WebP by default. When a selected file exceeds `MaxUploadBytes`, the player is notified before the browser reads it. The server validates MIME type, dimensions, base64 size, and payload limits again before saving or forwarding it.

Uploads, URL imports, previews, and generated-media hosting share the configured upload queue. A full queue returns the configured busy message instead of starting more expensive work.

### 3D Preview Background

The preview starts with a black background. Use the small switch below the preview to change it to light gray. The choice is stored in the player's NUI browser data and is restored the next time the designer opens.

### AI or Other Studio Tools

AI is shown only when a compatible companion or third-party resource registers a studio tool. A generation returns an image layer that can be moved, resized, edited, or removed like another image.

In a limited session:

- Each successful AI request consumes one AI generation through the provider integration.
- Failed generations can be refunded by the provider integration.
- When `RequireRemainingComponentsForAI = true`, generation is blocked after the clothing allowance reaches zero even if AI uses remain.

---

## Save and Print

**Save** stores a draft that the creator can continue editing.

**Print** publishes the design and adds a metadata-bearing custom clothing item, unless approval is required. With the recommended limited-pass setting, a clothing use is consumed only when printing succeeds or an admin approves the print request.

The active allowance appears in the limited designer UI:

```text
2/2 pieces | 4/4 AI
```

If a pass allows two clothing pieces, the player chooses which two templates to create. A top, pants, shoes, or prop each consumes one clothing use when printed. Reprinting the same design in the same active pass does not consume a second clothing use.

{% hint style="info" %}
`ConsumeComponentOn = 'save'` changes the flow so a new saved design consumes the clothing use immediately. `print` is the recommended value.
{% endhint %}

---

## Wear and Remove Clothing

### Inventory Item

Use a printed item once to equip its design. Use that same item again while its design is equipped to remove it and restore the previous component or prop appearance.

When several matching custom items exist and the inventory backend cannot identify the exact used slot, Cloth Designer opens a picker so the player can select the intended design.

### Player Wardrobe

Open the created-clothing UI with:

```text
/gscd_wardrobe
```

The wardrobe lists up to the 100 most recently updated published, pending, or rejected designs owned by that player. Players can preview approved clothing, wear it, or take it off.

Inventory-item and wardrobe actions use the same server wearable state. Equipping in one UI is reflected in the other, and removing a design clears its persisted state so it does not return a moment later or after reconnecting.

### Persistence

Equipped designs are restored after supported QBCore, Qbox, and ESX player-load events. The runtime stores the previous appearance for each component or prop so removing custom clothing restores the correct pants, footwear, hat, or other base item.

---

## Approval Workflow

Set `Wardrobe.RequireApproval = true` to moderate prints.

### Player Flow

1. The player finishes a design and selects Print.
2. The design enters `pending_approval`; no wearable item is granted yet.
3. The player can see its pending status in `/gscd_wardrobe`.
4. Approval publishes the design and attempts to add the inventory item.
5. Rejection records the admin note and shows the rejected status in the player's wardrobe.

### Admin Flow

Admins with the configured ACE open:

```text
/gscd_clothing_review
```

The review UI shows the oldest pending requests first. Admins can preview, approve, or reject a design with a note.

```cfg
add_ace group.admin gscd.clothdesigner.admin allow
```

For limited or Tebex sessions, approval consumes the clothing use. This can happen while the creator is offline. If the item cannot be delivered, the approved design is still available through the player's wardrobe when they return.

---

## Tebex Passes

When Tebex integration is enabled, a package can grant any configured combination of clothing and AI allowances.

If `AutoOpenOnPurchase = true`, an online buyer is taken directly into the limited designer. Offline entitlements remain pending. Players claim their next available pass with:

```text
/gscd_claim_designer
```

The command name is configurable. See [Tebex Integration](tebex.md).

---

## Template Capacity

Each generated drawable has 26 texture variants. Cloth Designer allocates additional drawables when a silhouette runs out of texture slots. A template can temporarily show as full until pool expansion is materialized and the generated clothing resource is restarted.

To add a template, place its YDD and any companion YTD in the correct `cloth_templates/<gender>/<category>/` folder, then restart. See [Installation](installation.md#adding-templates-later).

---

## Commands

| Command | Purpose | Access |
|---|---|---|
| `/gscd_wardrobe` | Preview, wear, and remove the player's created clothing | Player |
| `/gscd_clothing_review` | Review pending designs | Configured wardrobe admin ACE |
| `/gscd_claim_designer [package]` | Claim the next or named Tebex pass | Player |
| `gscd_tebex_grant <package> <target> <transaction>` | Grant a configured Tebex pass | Console or `gscd.tebex` ACE |
| `gscd_delete_design <designId>` | Delete a design and release its wearable state and slot | Protected command ACE |
| `gscd_rescan` | Rescan source templates | Protected command ACE |
| `gscd_rebuild_pool` | Reattach and rebuild the generated pool | Protected command ACE |
| `gscd_fill_slots [sourceKey]` | Fill slots for development testing | Development only |

Maintenance commands are advanced tools. Normal template installation still ends with a server restart so FiveM can register generated apparel.
