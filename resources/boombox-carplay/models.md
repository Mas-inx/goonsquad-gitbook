# Radio & Custom Models

Edit `config/stations.lua` (the list `Config.Stations`). Two kinds:

```lua
{   -- a live internet stream (Icecast / Shoutcast, mp3 or ogg)
    id = 'my-stream', name = 'My Station', genre = 'House', type = 'stream',
    url = 'https://example.com/stream.mp3',
    colors = { '#f97316', '#ef4444' },
    ident = { speech = 'GENERIC_HI', voice = 'A_M_Y_HIPSTER_01_WHITE_FULL_01' },   -- optional
},
{   -- a rotation: your own files in a loop, the same second for everybody
    id = 'my-rotation', name = 'Server FM', genre = 'Mix', type = 'rotation',
    colors = { '#7c5cff', '#22d3ee' },
    tracks = {
        { url = 'https://cdn.example.com/one.mp3', title = 'One', artist = 'Someone', duration = 214 },
        { url = 'https://cdn.example.com/two.mp3', title = 'Two', artist = 'Someone', duration = 187 },
        { demo = 'neon-drive' },
    },
},
```

- `id` must be unique: letters, digits, `-`, `_`.
- A rotation track needs its `duration` in seconds (the server cannot measure a file).
- Links must be `https`.
- A station with a mistake is left out and named by `/gsbc doctor` and in the server console; the
  other stations still work.
- The stream stations shipped in the file are public streams run by others. They are examples;
  you are responsible for having the right to play a stream on your server.

## Speaker Models

`config/props.lua` lists the models (`Config.Props`); the first is the default. `Config.CarryAnim`
in the same file is the animation played while carrying by hand.

It ships with the five GOONSQUAD STUDIOS speakers (streamed from `stream/`; their Blender sources
are in `props-src/`) - **RGB Boombox** `gs_rgb`, **Retro Boombox** `gs_retro`, **Hi-Fi 2.1 Set**
`gs_hifi`, **Party Speaker** `gs_party`, **Cylinder Speaker** `gs_cylinder` - and the game's own
`boombox`, `ghetto` and `hifi`. In `stream/` are their models (`gs_boombox_rgb`, `gs_boombox_retro`,
`gs_speaker_set`, `gs_party_speaker`, `gs_speaker_cyl` `.ydr`), the running RGB lights (`*_uv.ycd`)
and `gs_speakers.ytyp` (loaded by `fxmanifest.lua`). Keep only game files in `stream/`: the game
tries to stream everything in it and warns about anything else.

- **A model already in the game:** add a block with its name:
  ```lua
  { id = 'radio', label = 'Radio', model = 'prop_radio_01', description = 'A small radio.',
    carry = { style = 'attach', bone = 57005, pos = { 0.27, 0.0, 0.0 }, rot = { 0.0, 263.0, 58.0 } }, visualHeight = 0.0 },
  ```
- **A custom model:** put the `.ydr` / `.ytyp` in this resource's `stream/` folder, add
  `data_file 'DLC_ITYP_REQUEST' 'stream/yourfile.ytyp'` to `fxmanifest.lua`, then add the block.

The fields of a block (`Config.Props[]`):

| Field | Meaning |
|---|---|
| `id`, `label`, `model` | What is saved, what players read, the game model. |
| `description`, `features` | One line and a few short chips, shown in the model picker. |
| `item` | With `Config.Boombox.select = 'items'` / `'both'`: the inventory item that is this model ([Inventory](inventory.md)). |
| `allow` | Optional: `{ jobs = { 'police' }, ace = 'gsbc.vip' }` - only those (either) may choose it; others see it locked in the picker. Admins always may. |
| `visualHeight` | Raises the floating visualiser for tall models. |
| `carry` | How it is held. `false`: it cannot be carried (it can still be moved with **Move**). `style = 'hand'`: it hangs from the right hand by its handle - `grip` is the middle of the handle in the model's own space (metres from its base), put exactly in the palm; it stays upright and swings a little as you walk; `yaw` turns it, `out` holds it off the leg. `style = 'trolley'`: pulled on its wheels like a suitcase - the handle in the hand, the speaker leaning back to its `wheels` (the axle, with `wheelRadius`) on the ground behind you, following the ground. `heavy = true`: a slower walk, no sprint or jump while carrying it. `style = 'attach'`: fixed to a bone with `bone`, `pos`, `rot` (the way the game's props are held). |

**Fine-tuning how a model sits in the hand:** an admin carries it and types `/gsbc carry`. The
arrow keys move it right / forward, Page Up / Down raise and lower it, with Shift held the arrows
turn and tilt it - live, standing or walking. Enter saves it for every player (`data/carry.json`);
Backspace leaves without saving.

**Move and Change model:** with ox_target / qb-target, looking at a standing speaker offers
**Move** (the placement copy, then it is put there playing on); a model built to stand (the Hi-Fi
set) also has **Move** in its panel instead of Carry. With `select = 'picker'` or `'both'` there is
also **Change model** (the model picker; it becomes that model where it stands, queue and all - one
placed from a model's own item stays that model). With model items (`'items'`, the default) a
speaker is the model that was bought, so Change model is not offered.

A model the game does not know is named in F8 and by the doctor ("Boombox models").

## Physical Car Screens

The four supplied car screen meshes have glass geometry defined in protected runtime code. The open configuration selects among those supported models and their inventory items. It does not define arbitrary replacement glass geometry. Use `/carscreen` for per-vehicle positioning; keep vehicle plates unique because persistence is keyed by plate.
