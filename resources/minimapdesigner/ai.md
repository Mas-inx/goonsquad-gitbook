# AI Companion

`gsmd-ai` is the optional server-side image-generation companion for GS Minimap Designer. It is delivered separately in `private_modules/gsmd-ai`.

The main editor, map tools, storage and exports work without it.

---

## Installation

1. Copy the `gsmd-ai` folder into your server's resources directory.
2. Open its server-only `config.lua` and set your image-generation API key.
3. Start it after the main designer.

```cfg
ensure gs-minimapdesigner
ensure gsmd-ai
```

Keep both resource names unchanged. `gsmd-ai` declares the main designer as a dependency.

For **Snip in-game**, install and start `screenshot-basic` before using the capture tool. **Generate** and **Snip from map** do not need that resource.

---

## Configuration

The companion's configuration remains editable under Asset Escrow:

```lua
GSMD_AI.Config = {
    GoogleApiKey = 'YOUR_API_KEY',
    Model = 'gemini-3.1-flash-image',
    MaxPromptLength = 900,
    RequestTimeoutMs = 240000,
    AcePermission = 'minimapdesigner.edit',
    Queue = {
        MaxConcurrent = 1,
        MaxPending = 4,
    },
}
```

| Setting | Behaviour |
|---|---|
| `GoogleApiKey` | Server-only provider credential; blank in the release |
| `Model` | Model identifier passed to the provider; use one available to your account |
| `MaxPromptLength` | Maximum prompt length accepted by the companion |
| `RequestTimeoutMs` | Provider request timeout in milliseconds |
| `AcePermission` | Fallback ACE if the main designer's access export cannot be used |
| `Queue.MaxConcurrent` | Number of simultaneous generation requests |
| `Queue.MaxPending` | Maximum waiting requests before new requests are rejected as busy |

The normal access check delegates to the designer's `hasEditAccess` export, so its ACE and identifier allowlist apply to AI too.

{% hint style="warning" %}
Keep the key only in the companion's server-side configuration. API usage is billed and limited by your provider account. A configured model name does not guarantee that the account has access to it.
{% endhint %}

---

## Generate Artwork

1. Open the **AI** tab and select **Generate**.
2. Enter a prompt describing the desired map stamp or artwork.
3. Select **Generate** and wait for the result.
4. Optionally enable **Match surrounding colors**.
5. Select **Place on map**, then adjust the new image element.
6. Save or publish the sheet when ready.

---

## Restyle a Snip

Select **Restyle snip**, then choose a source:

| Source | Use |
|---|---|
| Snip from map | Drag a region on the editor map to restyle its imagery |
| Snip in-game | Capture the world from the top-down camera, including custom buildings |

**Match map style** sends a reference crop of the current design with the request. Describe the change, generate the image, and place the result. Georeferenced snips retain their map location for placement.

Generated artwork becomes an ordinary image element and follows the main resource's save, media-hosting and tile-baking settings. Captures and style reference images are sent to the configured generation provider.

---

## Temporary Files

The companion creates `generated_media/` at runtime and normally deletes result files after sending them to the player. This working directory is not part of the clean release package.

For unavailable-addon, queue and provider errors, see [Troubleshooting](troubleshooting.md#ai-is-unavailable-or-fails).
