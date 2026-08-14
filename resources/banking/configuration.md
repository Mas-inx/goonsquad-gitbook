# Configuration

GS Banking separates configuration by domain. File values are the shipped defaults; most runtime-safe values can also be edited from **Administration → Config** in the banking interface.

| File | Controls |
|---|---|
| `config/config.lua` | Framework bridges, features, cash, security, fees, overdraft, commands, logging, and compatibility |
| `config/accounts.lua` | Account products, product caps, account tiers, government account, and default account |
| `config/cards.lua` | Physical cards, card tiers, issue fees, score gates, and card limits |
| `config/loans.lua` | Loan products, approval policy, repayment/default rules, and credit scoring |
| `config/savings.lua` | Interest, APY tiers, term deposits, goals, and round-up saving |
| `config/tax.lua` | Income brackets, tax types, exemptions, wealth tax, and tax holidays |
| `config/investments.lua` | Market assets, price ticks, orders, dividends, wallets, and market news |
| `config/locations.lua` | Branches, blips, banker peds, ATM models, brands, cash pools, and receipts |
| `config/crime.lua` | Stolen cards, skimmers, dirty deposits, robbery rules, police tools, and offshore accounts |

---

## Live Configuration Panel

Trusted administrators can open the **Administration** page and select **Config** to edit registered settings without touching Lua files.

The live editor provides:

- Typed validation for money, percentages, numbers, text, booleans, and tags
- Row editors for branches, loan products, tax brackets, APY tiers, term deposits, market assets, news events, and ATM brands
- A **use my location** action for branch coordinates
- Per-field and per-section restore-to-default actions
- Immediate synchronization to connected players
- Audit logs and admin webhook entries

Live overrides are stored in the database and applied over the Lua defaults after restart. If a file change appears to have no effect, check whether the same field has a live override.

The following stay file-only for security or because they require code/functions:

- Framework and bridge forcing
- `Config.Security.pepper`
- `Config.DemoMode`
- PIN length
- Loan score-to-rate function
- External logging credentials

Settings marked as requiring a restart are saved immediately but bind after the resource restarts.

---

## General and Branding

```lua
Config.Locale = 'en'
Config.Currency = '$'
Config.Debug = false
Config.DemoMode = false

Config.Branding = {
    bankName = 'Gruppe Sechs',
    bankTag = 'GS',
    accent = '#a3e635',
    mode = 'dark', -- 'dark' or 'light'
}
```

`bankTag` is the prefix used when generating account numbers. Existing account numbers are not renamed when the tag changes.

The appearance editor can override bank name, accent, and mode at runtime. Saved themes are broadcast to open banking sessions.

---

## Framework and Integration Bridges

```lua
Config.Framework = 'auto'
Config.Inventory = 'auto'
Config.Target = 'auto'
Config.Notify = 'auto'
Config.TextUI = 'auto'
Config.Phone = 'auto'
Config.Dispatch = 'auto'
Config.Society = 'auto'
Config.Permissions = 'auto'
```

`auto` selects the first supported resource that is already started. Force an adapter only when automatic detection selects the wrong provider.

Common forced values:

| Setting | Values |
|---|---|
| `Framework` | `qbx`, `qb`, `esx`, `ox`, `standalone` |
| `Inventory` | `ox`, `qb`, `qs`, `ps`, `codem`, `origen`, `core` |
| `Target` | `oxtarget`, `qbtarget`, `qtarget`, `textui` |
| `Society` | `qbmanagement`, `esxsociety`, `none` |
| `Phone` | `lb`, `qs`, `gks`, `npwd`, `road`, `yflip`, `none` |

Use `/gsbanking bridge` to see the selected values.

---

## Feature Switches

```lua
Config.Features = {
    accounts = true,
    sharedAccounts = true,
    business = true,
    cards = true,
    atm = true,
    atmDeposit = true,
    invoices = true,
    standingOrders = true,
    loans = true,
    credit = true,
    savings = true,
    termDeposits = true,
    savingsGoals = true,
    investments = true,
    crypto = true,
    tax = true,
    payroll = true,
    security = true,
    notifications = true,
    crime = true,
    phonePush = true,
}
```

Disabled modules are removed from the player interface where applicable and protected server-side.

---

## Cash and Interactions

```lua
Config.Cash = {
    asItem = false,
    item = 'money',
}

Config.OpenCommand = 'bank'
Config.OpenKeybind = false
Config.AtmCommand = false
Config.InteractDist = 2.0
Config.RequireCardAtAtm = false
```

Set `Cash.asItem = true` only when carried cash is an inventory item. Deposits and withdrawals then add or remove `Config.Cash.item` through the inventory bridge.

`OpenKeybind` registers a FiveM-mappable keybind. `AtmCommand` is intended for development; normal ATMs are opened by targeting configured ATM models.

---

## Security

```lua
Config.Security = {
    pepper = 'your-private-secret',
    pinLength = 4,
    adminAces = { 'gsbanking.admin', 'group.admin' },

    pinRequired = {
        app = false,
        atm = true,
        transferAbove = 10000,
    },

    maxPinAttempts = 3,
    lockoutMinutes = 15,
    largeTransferAlert = 50000,
    amlFlagAbove = 75000,
    amlRapidCount = 6,
    amlRapidWindow = 60,
    maxMemoLength = 140,
    sessionTimeout = 0,
}
```

| Option | Description |
|---|---|
| `pepper` | Private value mixed into PIN hashes; set once and do not rotate casually |
| `pinRequired.app` | Require an existing banking PIN when opening protected app endpoints |
| `pinRequired.atm` | Require the selected card's PIN at ATMs |
| `transferAbove` | Ask for a recently verified banking PIN at or above this transfer amount; `0` disables |
| `largeTransferAlert` | Add a security event for transfers at or above the amount |
| `amlFlagAbove` | Flag transactions for staff review at or above the amount |
| `amlRapidCount` / `amlRapidWindow` | Flag rapid mutation activity |
| `sessionTimeout` | Inactivity timeout in minutes; `0` disables |

Every client mutation is also rate-limited, validated, and protected by an idempotency key.

---

## Fees and Overdraft

```lua
Config.Fees = {
    transfer = {
        percent = 0.5,
        min = 0,
        max = 2500,
        ownAccounts = 0,
        societyFree = true,
    },
    withdrawal = { percent = 0, flat = 0 },
    levyPercent = 1.5,
    maintenance = {
        enabled = false,
        weekly = { standard = 0, plus = 25, business = 150, private = 400 },
    },
    cardReplacement = 150,
    receiptPrint = 2,
}
```

Transfer fees and government transfer levies are separate charges. Own-account transfers are free. Taxes are collected only when the matching type is enabled in `config/tax.lua` and the player is not exempt.

```lua
Config.Overdraft = {
    enabled = true,
    limits = {
        standard = 1000,
        plus = 5000,
        business = 25000,
        private = 50000,
    },
    dailyFee = 15,
    creditHit = 4,
}
```

---

## Account Products and Limits

Each entry in `Config.AccountProducts` defines an account players can open:

```lua
savings = {
    label = 'High-Yield Savings',
    tier = 'plus',
    openingFee = 500,
    minDeposit = 500,
    interestApy = 4.25,
    maxPerPlayer = 2,
}
```

Supported player-facing product keys are `checking`, `savings`, `shared`, and `business`.

```lua
Config.MaxAccountsTotal = 6

Config.TierLimits = {
    standard = {
        perTransaction = 25000,
        dailyWithdrawal = 20000,
        dailyTransfer = 50000,
    },
    -- plus, business, private...
}
```

Daily limits are calculated from ledger activity, so reconnecting or restarting does not reset them.

`Config.DefaultAccount` controls the first checking account. On Qbox, QBCore, and ESX this account mirrors framework bank money.

---

## Cards

```lua
Config.Cards = {
    item = 'gs_bankcard',
    giveItem = true,
    expiryYears = 3,
    replacementFee = 150,
    maxPerAccount = 3,
    pinAttempts = 3,
    defaultContactless = true,
    tiers = {
        debit = {
            label = 'Debit',
            fee = 250,
            spendLimit = 5000,
            atmLimit = 2000,
            contactlessLimit = 250,
            minScore = 0,
            orderable = true,
        },
    },
}
```

`orderable = false` hides unfinished or staff-only card products. The issue fee is charged to the linked account. Credit-score requirements are checked server-side.

---

## Loans and Credit

`Config.Loans.products` defines the amount range, base APR, term choices, minimum score, and collateral requirement for every product.

```lua
Config.Loans.approval = 'auto' -- 'auto' or 'manual'
Config.Loans.approverJobs = { 'banker' }
Config.Loans.graceDays = 2
Config.Loans.missedForDefault = 4
Config.Loans.offlineRepayments = true
```

Manual approval uses:

```text
/gsbank loan <loan_id> approve
/gsbank loan <loan_id> reject
```

Credit factor weights in `Config.Credit.weights` must total exactly 100. Startup validation rejects an invalid total.

---

## Savings

```lua
Config.Savings.compounding = 'daily'
Config.Savings.payoutHour = 5

Config.Savings.apyTiers = {
    { upTo = 50000, apy = 4.25 },
    { upTo = 250000, apy = 3.10 },
    { upTo = nil, apy = 1.10 },
}
```

Interest uses the lowest daily balance snapshot to prevent last-minute deposit farming.

`termDeposits` controls duration, APR, and early-break penalty. `goals` controls the per-player cap and payday auto-deposit. `roundUp` can sweep purchase change into the first savings account.

---

## Taxes

```lua
Config.Tax.enabled = true
Config.Tax.types = {
    income = true,
    transfer = true,
    withdrawal = false,
    sales = true,
    wealth = false,
}
```

Income brackets must be contiguous. Tax revenue is credited to `Config.GovernmentAccount`.

Use `exemptJobs`, `exemptGangs`, or `holidayUntil = 'YYYY-MM-DD'` for exemptions and tax holidays.

---

## Investments

```lua
Config.Investments.tickMinutes = 5
Config.Investments.feePercent = 0.35
Config.Investments.limitOrders = true
Config.Investments.maxOpenOrders = 12
```

Each asset defines `symbol`, `name`, `kind`, starting price, volatility, daily drift, yield, volume, and market cap. Prices and holdings persist across restarts. Optional news events apply scheduled shocks to configured symbols.

---

## Branches and ATMs

Add a branch in `Config.Banks`:

```lua
{
    label = 'Legion Square',
    coords = vec3(149.44, -1042.11, 29.37),
    heading = 335.0,
}
```

Every public branch receives its configured blip, interaction zone, and optional banker ped. Set `society` on a branch to restrict it to a job.

ATM brands define surcharge, cash capacity, restock interval, deposit support, and matching models:

```lua
lombank = {
    name = 'Lombank',
    surcharge = 1.5,
    capacity = 120000,
    restockHours = 8,
    deposit = true,
    models = { 'prop_atm_02' },
}
```

Do not significantly increase `Config.AtmMaxDistance`; the server uses it to reject forged remote ATM sessions.

---

## Criminal and Police Systems

All criminal features are configurable in `config/crime.lua`:

- Stolen-card PIN attempts and dispatch probability
- ATM skimmer item, capacity, and detection chance
- Dirty-money deposit flagging
- ATM robbery police requirement, cooldown, downtime, and maximum take
- Police jobs and minimum grades for freeze, seizure, and trace exports
- Offshore account price, transaction fee, and trace grade

The resource does not provide an ATM robbery minigame. A heist resource calls `DrainAtm` and `SetAtmBroken`; GS Banking enforces the configured cash, police, cooldown, and broken-state rules.

---

## Logging and Performance

`Config.Webhooks` provides separate Discord destinations for transactions, accounts, cards, loans, invoices, society actions, administration, security, economy reports, and reconciliation.

Empty webhook strings disable that category.

```lua
Config.Perf = {
    statePushDebounceMs = 250,
    txHistoryInState = 400,
    archiveAfterDays = 90,
    cacheAccounts = true,
}
```

Old ledger rows move into the archive table after `archiveAfterDays`. Reconciliation includes both current and archived history.

---

## Important Rules

- All money values are integer dollars. Floats, strings, negative values, NaN, and infinity are rejected where a positive amount is expected.
- Do not update `gs_accounts.balance` directly. Use the public exports so framework mirrors, ledger entries, statebags, notifications, and audit events remain consistent.
- Do not change the PIN pepper after PINs exist unless you are deliberately performing a full PIN reset.
- Keep credit weights at 100 and income brackets contiguous.
- Live database overrides can supersede Lua defaults.

