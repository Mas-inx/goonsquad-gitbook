# Configuration

Edit `lift/config.lua` and restart `gs-lumacafe` after making changes. `lift/client/bridge.lua` is also editable for custom target and notification adapters.

| Setting | Default | What it controls |
|---|---|---|
| `Config.Target` | `'auto'` | Tries `ox_target`, then `qb-target`, then the key prompt. Set `'ox_target'` or `'qb-target'` to select a provider, or `'none'` for the key prompt. |
| `Config.Notify` | `'auto'` | Tries `ox_lib`, then Qbox/QBCore, then ESX, then the game feed. Explicit modes are `'ox_lib'`, `'qb'`, `'esx'` and `'native'`. |
| `Config.HidePlacedObjects` | `true` | Hides large script-placed objects around the venue and car park. Set `false` to keep furniture and job props placed by your scripts. The map still removes its listed street clutter. |
| `Config.SpawnDistance` | `45.0` | Distance in metres at which lift props spawn for a player. |
| `Config.DuiDistance` | `25.0` | Distance in metres at which the lift screens start rendering. |
| `Config.UseDistance` | `2.5` | Base range for the server's landing call check. Cabin controls use the cabin volume. |
| `Config.Debug` | `false` | Draws lift debug geometry and logs state changes. |

`Config.Timing` controls the door animation, travel speed, minimum ride time, dwell time, automatic closing and the rider's screen fade. By default, the doors take 1.6 seconds to open or close and close automatically 5 seconds after opening. Set `autoCloseSeconds = nil` to disable automatic closing while the lift is idle.

## Floors and access

`Config.Elevators.luma` holds the four floor entries in ascending order, **G**, **1**, **2**, **R**. Keep that order and edit the display ID, label, subtitle or access rule in place. `start = 1` starts the car on **G** after a restart.

The `job` value is `nil` for public access, a job name for one job, a list such as `{ 'luma', 'security' }` for several jobs, or `{ job = 'luma', grade = 2 }` for a minimum job grade. All four floors are public by default. With no supported framework running, leave floors public.

The lift marks restricted destinations in the cabin and checks access when a player selects one. This setting applies to destination selection. Secure the rest of the venue separately if you need access control for stairs, entrances or passengers riding with someone else.

`Config.Targets`, `Config.Text` and `Config.Sounds` hold interaction labels, prompts and sounds. A sound can be set to `false` to mute it. The screen dimensions, hit areas and camera settings match the supplied lift models and display pages.
