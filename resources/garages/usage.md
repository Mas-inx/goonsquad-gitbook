# Usage

---

## Opening a Garage

Players can open a garage in any enabled way:

- Walk to the interaction point — an in-world `E — OPEN GARAGE` prompt fades in on approach
- Use the target zone (ox_target / qb-target) or the marker prompt, depending on server configuration
- Run `/garage` (configurable) near a garage
- Use the optional keybind if the server has set one

Each garage shows its name, type, capacity usage, and fees in the header. Impound lots open the same interface in release mode.

---

## The Garage Interface

The interface is a full-screen showroom: the live game render is the hero image, with the selected vehicle staged on a turntable in front of composed camera presets.

### Vehicle List

The left rail lists every vehicle stored in (or out from) the garage:

- **Search** by name, plate, or nickname
- **Category filters** (cars, motorcycles, utility, and so on) matching what the garage accepts
- **Favorites** pin to the top of the list
- Each entry shows plate, brand, condition segments (engine, body, fuel), mileage, and status — stored, out, shared with you, or in impound
- A vehicle that is currently out still shows in the garage it came from, so it can be located

### Preview

Selecting a vehicle stages it live:

- **Drag** to orbit the turntable, **scroll** to zoom
- **Camera presets** — Hero, Low front, Profile, Rear quarter, Detail — from the shot bar
- **Cinematic mode** speeds the turntable and hides the interface chrome
- Performance bars (speed, acceleration, braking, traction) plus weight, drivetrain, gears, and seats are read from the model's real handling data
- A world-anchored nameplate stands behind the car with the model name, plate, category, and condition readouts — the car physically occludes it

### Actions

The action dock offers, depending on server features and the vehicle's state:

| Action | What happens |
|---|---|
| **Take out** | Charges the retrieve fee, plays a delivery wipe, and spawns the vehicle on the first free bay with an arrival camera |
| **Transfer** | Moves a stored vehicle to another garage you can access, for the transfer fee |
| **Find my car** | Sets a GPS waypoint to a vehicle that is currently out |
| **Give keys** | Hands temporary keys to a nearby player; with shared access enabled, the share persists |
| **Favorite / Rename** | Pins the vehicle or sets a nickname |
| **Release** | At an impound lot: pays the depot fee and returns the vehicle to circulation |

Fees are paid from the configured accounts in order (cash, then bank by default).

---

## Parking

Parking never involves the menu. Drive up to any garage that accepts your vehicle's category and a world-anchored `E — PARK` prompt appears at the nearest spawn bay, showing the garage name, your distance, and a ready state.

Press `E` inside the store radius and the vehicle is committed: its condition, fuel, mods, and mileage are saved, keys are taken back, and a park cinematic covers the despawn.

The prompt pre-filters by category on the client, but ownership is checked on the server — pressing `E` on a vehicle that is not yours gets a clean rejection notice.

---

## Impound

When a vehicle is impounded (by a police/tow script through the export, or by an admin command), it moves to an impound lot and leaves its home garage.

- The **owner** sees it at the impound lot and can pay the depot fee to release it (if the server allows owner pay).
- **Police and configured jobs** see every impounded vehicle at the lot, and can release any of them — free, if the server has `freeForJobs` on.
- Released vehicles return to the default garage (or stay stored at the depot, per server configuration).

---

## Shared Vehicles and Fleets

### Shared access

When a player gives you keys with shared access enabled, the vehicle appears in your garage list marked as shared, with the owner's name. Depending on server rules, you can take it out and park it, but not transfer it.

### Job and gang fleets

Job and gang garages can carry a shared fleet — vehicles nobody owns, gated by grade. Take one out and it exists for the shift: it uses a generated fleet plate, is never written to the database, and parking it at any garage simply returns it.

---

## House Garages

With a supported housing script running, every house you own — or hold keys to — is a private garage at the house's own garage spot. Buy a house and the garage appears; sell it and it is gone; hand someone keys and they can park there too.

House garages behave like any other garage (open prompt, park prompt, cinematic take-out, transfers), but only people with access ever see them. Vehicles parked at a house survive restarts, appear under "Find my car", and never leak into public lots.
