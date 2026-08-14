# API & Exports

GS Banking exposes a versioned server API for shops, jobs, missions, heists, MDTs, HUDs, and other economy resources.

Unless noted otherwise:

- Server exports return `success, dataOrError`.
- Money amounts must be positive integer dollars.
- Account references accept an internal account ID (`acc_...`) or IBAN.
- Export callers should prevent duplicate business operations when retrying; NUI idempotency applies to the banking interface, not arbitrary external callers.

---

## Account Exports

| Export | Signature | Description |
|---|---|---|
| `GetVersion` | `() -> string` | Return the API version. |
| `GetBridge` | `() -> table` | Return detected framework, inventory, target, notification, phone, dispatch, and society adapters. |
| `GetAccount` | `(idOrIban) -> boolean, table|string` | Return public account details and current balance. |
| `GetAccountBalance` | `(idOrIban) -> boolean, number|string` | Return the current balance; framework mirrors are read live. |
| `GetPlayerAccounts` | `(citizenId) -> boolean, table` | Return active accounts owned by the character. |
| `CreateAccount` | `(citizenId, type, name?, options?) -> boolean, table|string` | Create an account directly. |
| `CreateSocietyAccount` | `(society, label?, isGang?) -> boolean, table|string` | Create or return a society account. |
| `FreezeAccount` | `(idOrIban, frozen, reason?) -> boolean, string?` | Freeze or unfreeze an account. |

Example:

```lua
local ok, account = exports['gs-banking']:GetAccount('GS12345678901234')
if not ok then
    print(('Account lookup failed: %s'):format(account))
    return
end

print(account.id, account.name, account.balance, account.frozen)
```

Create an internal player account:

```lua
local ok, created = exports['gs-banking']:CreateAccount(
    citizenId,
    'savings',
    'Event Savings',
    {
        ownerName = playerName,
        tier = 'plus',
    }
)
```

---

## Money Exports

| Export | Signature | Description |
|---|---|---|
| `AddMoney` | `(idOrIban, amount, reason?, meta?) -> boolean, string?` | Credit an account and balance it against the external-money ledger account. |
| `RemoveMoney` | `(idOrIban, amount, reason?, meta?) -> boolean, string?` | Debit an account and write a purchase/withdrawal-style ledger entry. |
| `TransferMoney` | `(fromIdOrIban, toIdOrIban, amount, memo?) -> boolean, transactionId|string` | Transfer between two internal GS accounts. |
| `Purchase` | `(source, amount, merchant?, category?) -> boolean, string?` | Charge the player's primary account, apply sales tax, and trigger round-up saving. |
| `PayIncome` | `(citizenId, grossAmount, label?) -> boolean, netAmount|string` | Pay income into the primary account with configured withholding. |
| `GetTransactions` | `(idOrIban, page?, perPage?) -> boolean, table|string` | Return paginated, UI-shaped transaction rows. |

### Add Money

```lua
local ok, err = exports['gs-banking']:AddMoney(
    accountIban,
    2500,
    'Delivery contract',
    {
        kind = 'salary',
        category = 'income',
        counterparty = 'Los Santos Logistics',
        issuer = GetCurrentResourceName(),
    }
)

if not ok then
    print(('Bank credit failed: %s'):format(err))
end
```

### Remove Money

```lua
local ok, err = exports['gs-banking']:RemoveMoney(
    accountId,
    450,
    'Vehicle repair',
    {
        kind = 'purchase',
        category = 'transport',
        counterparty = 'Benny’s',
        issuer = GetCurrentResourceName(),
    }
)
```

### Shop Purchase

`Purchase` is the recommended player-shop integration because it selects the primary account, applies configured sales tax, records the merchant/category, updates state, and runs round-up saving.

```lua
local ok, err = exports['gs-banking']:Purchase(
    source,
    125,
    '24/7 Supermarket',
    'shopping'
)

if not ok then
    TriggerClientEvent('my-shop:purchaseFailed', source, err)
end
```

### Taxed Income

```lua
local ok, netOrError = exports['gs-banking']:PayIncome(
    citizenId,
    5000,
    'Weekly salary'
)
```

The export calculates configured income tax, credits the net amount, routes withholding to the government account, records tax history, and notifies the player.

### Internal Transfer Caveat

`TransferMoney` supports internal-to-internal account transfers only. It refuses a transfer when either side is a Qbox/QBCore/ESX framework-mirrored checking account.

For mirrored accounts, use the appropriate `AddMoney` and `RemoveMoney` operation or a player-authorized banking flow so framework money and ledger history remain synchronized.

---

## Credit and Invoice Exports

| Export | Signature | Description |
|---|---|---|
| `GetCreditScore` | `(citizenId) -> true, score` | Return the current score. |
| `AddCreditScore` | `(citizenId, delta, reason?) -> true, score` | Apply a score change and return the new score. |
| `CreateInvoice` | `(options) -> boolean, invoiceId|string` | Create a player or society invoice. |

Create an invoice:

```lua
local ok, invoiceId = exports['gs-banking']:CreateInvoice({
    society = 'ambulance',
    fromName = 'Pillbox Medical',
    toCid = patientCitizenId,
    amount = 1800,
    reason = 'Emergency treatment',
    dueDays = 3,
    autoPay = false,
})
```

For a player-issued invoice, pass `fromCid` instead of `society`.

---

## ATM and Crime Exports

| Export | Signature | Description |
|---|---|---|
| `DrainAtm` | `(atmId, percent?, coords?) -> amount, error?` | Drain a configured portion of the machine pool using robbery rules. |
| `SetAtmBroken` | `(atmId, minutes?) -> true` | Mark a machine out of service. |
| `PlaceSkimmer` | `(atmId, ownerCitizenId?) -> boolean` | Place a skimmer when the feature allows it. |
| `TakeSkimmer` | `(atmId) -> table|nil` | Remove a skimmer and return harvested data. |
| `DepositDirtyMoney` | `(source, accountId, amount) -> boolean, error?` | Deposit configured dirty money with a flagged ledger entry. |
| `OpenOffshoreAccount` | `(source) -> boolean, iban|string` | Charge the configured fee and open an offshore account. |
| `PoliceFreeze` | `(source, target, frozen, reason?) -> boolean, error?` | Freeze/unfreeze after police job and grade validation. |
| `PoliceSeize` | `(source, target, amount, reason?, caseRef?) -> boolean, error?` | Seize funds into the configured destination. |
| `PoliceTrace` | `(source, ibanOrCitizenId) -> table, error?` | Return trace results after police authorization. |
| `GetFlaggedTransactions` | `(source, limit?) -> table, error?` | Return the AML review queue after police authorization. |

Heist example:

```lua
local taken, err = exports['gs-banking']:DrainAtm(atmId, 50, robberyCoords)
if not taken then
    return false, err
end

exports['gs-banking']:SetAtmBroken(atmId, 45)
```

The banking resource does not supply the robbery minigame or loot delivery. `DrainAtm` enforces the ATM pool, robbery percentage, police count, cooldown, and configured coordinates/rules.

---

## Investment and Cheque Exports

| Export | Signature | Description |
|---|---|---|
| `GetCryptoWallet` | `(citizenId, symbol) -> address` | Get or create the character's wallet address for a configured symbol. |
| `CryptoSend` | `(fromCitizenId, toAddress, symbol, quantity) -> result` | Send a supported crypto holding to another GS wallet. |
| `WriteCheque` | `(source, amount, memo?) -> boolean, error?` | Give a cheque item backed by the writer's primary account. |
| `DepositCheque` | `(source, chequeMetadata, accountId) -> boolean, error?` | Deposit a cheque into an account; insufficient or unsupported drawers bounce. |

Cheque support requires the `gs_cheque` inventory item. The deposit export expects the metadata originally placed on that item.

---

## Client Exports

| Export | Signature | Description |
|---|---|---|
| `OpenBank` | `()` | Open the full banking interface. |
| `OpenAtm` | `(atmId?)` | Open an ATM session. Normal world interactions should supply a real configured ATM. |
| `CloseBank` | `()` | Close the banking/ATM interface. |
| `IsOpen` | `() -> boolean` | Return whether the interface is open. |
| `GetCurrentAtm` | `() -> string|nil` | Return the active ATM ID. |
| `GetCachedBalance` | `() -> number` | Read the replicated primary-account balance without a server callback. |

```lua
exports['gs-banking']:OpenBank()

local balance = exports['gs-banking']:GetCachedBalance()
```

---

## Server Events

Listen with `AddEventHandler` from server code:

| Event | Arguments |
|---|---|
| `gs-banking:transactionCreated` | `transactionId, kind, actorCitizenId` |
| `gs-banking:balanceChanged` | `accountId, newBalance` |
| `gs-banking:accountCreated` | `accountId, iban` |
| `gs-banking:accountFrozen` | `accountId, frozen` |
| `gs-banking:accountClosed` | `accountId` |
| `gs-banking:loanFunded` | `citizenId, loanId, principal` |
| `gs-banking:loanDefaulted` | `citizenId, loanId, collateral` |
| `gs-banking:loanSettled` | `citizenId, loanId` |

```lua
AddEventHandler('gs-banking:balanceChanged', function(accountId, newBalance)
    print(('Bank account %s is now %d'):format(accountId, newBalance))
end)
```

These are server-side events. Do not register them as trusted client network events.

---

## Statebag

When `Config.Statebags = true`, the primary-account balance is replicated as:

```lua
local balance = Player(source).state.bankBalance
```

Client HUDs can use the client export instead:

```lua
local balance = exports['gs-banking']:GetCachedBalance()
```

---

## Compatibility Exports

When enabled, GS Banking registers common compatibility names on the `gs-banking` export table.

### qb-banking-Compatible Names

- `GetAccount`
- `GetAccountBalance`
- `AddMoney`
- `RemoveMoney`
- `CreatePlayerAccount`
- `CreateJobAccount`
- `CreateGangAccount`
- `CreateBankStatement`

### Renewed-Banking-Compatible Names

- `getAccountMoney`
- `addAccountMoney`
- `removeAccountMoney`
- `handleTransaction`

### ESX Society Compatibility

When ESX is active and esx_addonaccount is not running, GS Banking answers:

```lua
TriggerEvent('esx_addonaccount:getSharedAccount', 'society_police', function(account)
    -- account.money, account.addMoney(), account.removeMoney(), account.setMoney()
end)
```

When qb-management or esx_addonaccount is running, GS Banking uses two-way synchronization instead of replacing the provider.

{% hint style="info" %}
The compatibility functions are provided by the running `gs-banking` resource. New integrations should call the stable API documented above rather than relying on a legacy compatibility shape.
{% endhint %}

---

## Database Integration

Treat all `gs_*` tables as resource-owned. External dashboards may read them, but mutations should go through exports.

Important tables include:

| Table | Purpose |
|---|---|
| `gs_accounts` | Account ownership, type, balance, holds, and state |
| `gs_ledger` / `gs_ledger_archive` | Append-only transaction legs |
| `gs_members` | Shared and society permissions |
| `gs_cards` / `gs_pins` | Cards and salted PIN records |
| `gs_loans` / `gs_loan_schedule` | Loan state and installments |
| `gs_invoices` / `gs_payment_requests` | Billing and requests |
| `gs_goals` / `gs_term_deposits` | Savings products |
| `gs_holdings` / `gs_invest_orders` / `gs_market` | Investments and prices |
| `gs_security_events` / `gs_admin_log` | Security and administration audit trails |

Direct balance edits bypass framework mirrors, double-entry balancing, statebags, limits, webhooks, and notifications and will trigger reconciliation drift.

