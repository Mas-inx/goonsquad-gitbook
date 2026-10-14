# Usage

## Staff Access

The default bar stations, ice machine, monitor and storage shelf require the `laflavia` job. All four grades can work the bar; only a boss grade can use the office laptop. Customer tablets and the customer's own pickup remain public.

Use third eye when a target backend is installed. Native interaction uses E, with G for the second option at a shared tablet/tray spot.

## Make a Drink

1. Obtain an empty glass and the recipe's ingredients from your inventory or the supplies shop.
2. Open the left or right bar station and select a drink. The menu shows availability and missing ingredients.
3. Follow the recipe card. Point at objects, hold left mouse to grab/drag and move bottles over the container to pour.
4. Stop within the target band, manipulate tools for motion steps, and click to open bottles or the shaker.
5. Garnish and finish. E confirms serving or skips an optional step; Backspace cancels the session.
6. For an iced drink, carry the prepared glass to the machine, select Add ice to complete, press START on its screen and serve with E after dispensing. Serving also completes automatically after the configured delay.

| Input | Action |
|---|---|
| Mouse | Select an object on the counter |
| Left mouse held | Grab/drag, pour, stir, shake, muddle or squeeze |
| Right mouse | Rotate held objects where supported |
| Left click | Bottle/shaker open/close actions |
| E | Confirm/serve or skip an optional step |
| Backspace | Cancel |

Only one bartender occupies a station or ice machine at a time. Carrying a drink frees its station for the next person. Moving too far away, taking too long or cancelling loses the active preparation; no finished drink is awarded. Ingredients are normally consumed on serving, not at session start.

Wine, champagne and tequila shots finish at the station. Mojito, Virgin Mojito, Martini, Cosmopolitan, Whiskey on the Rocks, Rum & Cola, Gin & Tonic and Orange Fizz use the machine with the shipped configuration.

## Quality and Drinking

The score reflects pour accuracy and recipe-step measurements. The server clamps measurements, checks the session/token, minimum timing, access, position and required items. A refused final inventory reward restores consumed ingredients. If a minimum quality is configured, a poor drink can consume ingredients without producing a reward.

Use the finished inventory item to play the drinking animation and receive configured thirst/stress and intoxication effects. A partly used bottle keeps its remaining milliliters on metadata-capable inventories; otherwise it is consumed whole. Quality and alcohol values appear in metadata where the backend supports them.

## Customer Orders

1. Choose Order drinks at one of the four tablets.
2. Select drinks/quantities, choose cash or bank and submit payment. A card-tap scene accompanies bank payment.
3. Note the order number and tray number shown by the tablet.
4. Wait until the public TV reports Ready for pickup.
5. Collect at the matching tray. Another customer's order is not accessible.

Each tablet has its own numbered tray. The server determines prices, service fee and limits. Invalid quantities are refused before charging money.

## Fulfill and Cancel Orders

Staff open Bar orders on the monitor. Make the requested drinks, then open the order's tray. With ox_inventory, drag the ordered drinks into its stash. Other backends transfer ordered drinks from staff inventory directly. The order becomes ready when every requested drink is present.

The monitor's DELETE button requires a second confirmation. Returned drinks retain metadata. A full/refusing staff inventory leaves the order intact. Uncollected cancellations refund the original buyer/account when enabled; offline customers must join before a refund can settle. Partially collected and ready-expired orders have different refund behavior described in [Configuration](configuration.md#customer-orders).

## Restock Supplies

Switch the monitor to SUPPLIES, browse/search, choose quantities, select pickup or delivery and pay from staff cash/bank.

- Pickup: go to the supplier at Cypress Flats and collect.
- Delivery: the courier arrives at the building's ground-floor gate; collect there.
- ox_inventory: the buyer's supplier stash opens beside their inventory.
- Other backends: items are handed over up to available capacity; the rest stays waiting.

The newest matching supply order is collected first. Supply orders survive restart, and the default configuration keeps them indefinitely. A supplier stash cannot be opened remotely or by another buyer.

## Storage

Use the back-bar shelf to open the shared staff stash. It belongs to the inventory backend and requires the configured job and distance. Default ESX inventory has no stash support; the server owner must disable it or add a compatible backend.

## Boss Office

A boss can use the office laptop to view staff and finances, hire nearby players, promote/demote lower ranks or fire lower ranks. A boss cannot edit themselves or staff at/above their own grade.

A hire target receives a 30-second accept/decline offer. Acceptance is checked against the stored offer and the issuing boss's current access/location. The Finances tab allows deposits and withdrawals between the boss's selected cash/bank account and the society.

The lift's Boss Office floor uses `bossFloorRule()`, requiring grade 3 by default. Its access rule is separate from the framework's boss flag.

## Persistence and Commands

Open drink orders, order numbering and supply orders use resource KVP. Society money/history use SQL. Inventory stashes are persisted by their backend. Active preparations, props and intoxication are transient.

GS La Flavia registers no console/chat commands. The framework's job-assignment command is only used by server admins during setup. Integrations use exports rather than development commands.
