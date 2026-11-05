# Integrations

## Framework and inventory

Framework adapters are editable for Qbox, QBCore and ESX. They cover job identity, money, usable items, needs, job grades and staff management. The framework inventory fallback supports QBCore-style data or ESX item counts; metadata and shared stash capabilities differ.

ox_inventory supplies direct stash hooks for order trays and supply pickups. Other supported adapters use direct handover. qb-inventory, ps-inventory and lj-inventory share an adapter; qs-inventory has its own adapter. Extra stash-opening compatibility does not imply a complete item adapter for every inventory.

ESX APIs may return nil on successful legacy operations. The bridges accept that convention while rejecting explicit false returns and exceptions. Test your actual installed version and custom bridges before launch.

## Target, notification and map

Target adapters support ox_target and qb-target, with native E/G prompts when no target runs. Notifications select an available provider or use the resource's floating toast. Configure provider choices in config/config.lua; the implementations remain editable.

The separately installed 10store-swirlz map and script should enable the same locations. Keep internal prop names and screen geometry intact. Adapting a different interior needs measured machine/tablet/camera/target locations; this package does not ship the building MLO.

## Hooks and trust

Config.Consumables.onConsume, Orders.OnPaid and Supplies.OnPaid are server hooks. Their configured callbacks can integrate other business systems. OnPaid runs after the built-in payment, so it is not a replacement for that charge by itself.

Validate permissions and amounts inside any server integration you add. Client job/UI checks alone are not authorization. Society exports and external order deletion are trusted server entry points. See [API & Exports](exports.md).
