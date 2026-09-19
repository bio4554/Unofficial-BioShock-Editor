# Machines

This document tells you how to place the machines that the player uses: the Circus of Values
and El Ammo Bandito vending machines, the Gatherer's Garden, the Health Station, the U-Invent,
the Gene Bank, the Power to the People station and the Vita-Chamber. It tells you which class
to place, the one or two names you set on a machine, where the stock of a vending machine
comes from, what the bake does, and how to make a Vita-Chamber catch the player. Hacking is
covered in [32-Hacking.md](32-Hacking.md) and the security system in
[31-Security.md](31-Security.md).

**CAUTION:** None of the game's gameplay systems has been extensively tested on custom maps.
That includes the systems that the SDK's authors have run on maps of their own. Expect
behaviour that differs from the game's own maps, and test every setup in the game.

## Before you start

- You can place actors and edit their properties in the Properties window. See
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- You can bake the map and start it in the game with Play Map. See
  [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).
- For the Vita-Chamber, you can make a trigger and a Script with one action. See
  [20-Scripting-Basics.md](20-Scripting-Basics.md).
- To give the player something to spend, you can fill a loot slot. See
  [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md).

## How machines are set up

Every machine is a preset class of `ShockDesignerClasses.pkg`. In the Actor Class browser, use
File > Open Package to load `Content\ShockDesignerClasses.pkg` from the content library. The
machine classes sit at these paths in the tree:

```
Actor
  ShockMachine                          (ShockGame)
    DispenserMachine                    (ShockGame)
      VendingStation                    (ShockGame)
        PlaceableVendingStation         Circus of Values
          PlaceableVendingStationAlt    El Ammo Bandito
        GrowthStation                   (ShockGame)
          PlaceableGrowthStation        Gatherer's Garden
      CraftingStation                   (ShockGame)
        PlaceableCraftingStation        U-Invent
      HealthStation                     (ShockGame)
        PlaceableHealthStation          Health Station
    PlasmidEquipStation                 (ShockGame)
      PlaceablePlasmidEquipStation      Gene Bank
    WeaponUpgradeStation                (ShockGame)
      PlaceableWeaponUpgradeStation     Power to the People
  BaseResurrectionStation               (ShockGame)
    ResurrectionStation                 Vita-Chamber
```

Place the class named in the right column. The `ShockGame` classes above them cannot be
placed.

The game's own maps set nothing per machine except its position, its rotation, its `Label`
and, at most, two names: `VendingTableName`, which picks the stock of a vending machine, and
`HackInfoName`, which picks its hack puzzle. Prices, meshes, costs, animations and whether a
machine can be hacked are built into the classes. You do not set them.

| Machine | Class | What to set |
| --- | --- | --- |
| Circus of Values | `PlaceableVendingStation` | `VendingTableName`, `HackInfoName` |
| El Ammo Bandito | `PlaceableVendingStationAlt` | `VendingTableName`, `HackInfoName` |
| Gatherer's Garden | `PlaceableGrowthStation` | `VendingTableName` |
| Health Station | `PlaceableHealthStation` | `HackInfoName` |
| U-Invent | `PlaceableCraftingStation` | `HackInfoName` |
| Gene Bank | `PlaceablePlasmidEquipStation` | Nothing |
| Power to the People | `PlaceableWeaponUpgradeStation` | Nothing |
| Vita-Chamber | `ResurrectionStation` | `StaticMesh`, `Label`, and a script that activates it |

`VendingTableName` is in the Machine category and `HackInfoName` in the Hacking category of
the Properties window. Both are name fields without a drop-down: type the name. `Label` is in
the Scripting category.

### Place a machine

1. In the Actor Class browser, select the class, for example `PlaceableVendingStation`.
2. Right-click the floor and click **Add PlaceableVendingStation here**.
3. Rotate the machine so that its front faces the room. The player is not moved to a stand
   position; the machine only needs to be reachable.
4. Press F4 and set the names from the table above.

## The stock of a vending machine

A Circus of Values, an El Ammo Bandito and a Gatherer's Garden sell what their table holds.
The table is a section of `System\LootTables.ini`, and `VendingTableName` is the name of that
section without the brackets. The same file holds the loot tables of
[24-Pickups-and-Loot.md](24-Pickups-and-Loot.md); the vending tables are a different kind of
section.

The valid names are the `VendingTableName=` lines of the section `[ShockGame.TableList]` at
the top of the file, one line per table. The game's own machines use these tables:

| Tables | Used by |
| --- | --- |
| `DeckOneVendingTable1` to `DeckOneVendingTable3`, `DeckTwoVendingTable1` to `DeckTwoVendingTable5`, ... `DeckSevenVendingTable1` to `DeckSevenVendingTable4` | Circus of Values, by level of the game |
| `DeckOneBanditoTable` to `DeckSevenBanditoTable` | El Ammo Bandito |
| `DeckOneGrowthTable` to `DeckSevenGrowthTable`, `MarketGrowthTable`, `SlumsGrowthTable` | Gatherer's Garden |
| `PeachVendingTable`, `DeckOneBulletsOnlyVendingTable`, `AtlasVendingTable` | Special machines of the game's own maps |

Useful choices for a first map: `DeckOneVendingTable1` sells pistol rounds, shotgun shells,
machine gun rounds, a first aid kit, an EVE hypo, vodka and chips. `PeachVendingTable` sells
consumables only. `DeckOneBulletsOnlyVendingTable` sells ammunition only. `DeckOneBanditoTable`
is the first El Ammo Bandito.

### A vending table

This is the start of `[DeckOneVendingTable1]`:

```ini
[DeckOneVendingTable1]
VendingLootSpec=(ItemClass=class'ShockGame.Pistol_Bullet', PickupClass=class'ShockDesignerClasses.StandardBulletPickup', StackSize=6,CostAdjustment=1, DisplayWhenUnHacked=True, DisplayWhenHacked=False)
VendingLootSpec=(ItemClass=class'ShockGame.Pistol_Bullet', PickupClass=class'ShockDesignerClasses.StandardBulletPickup', StackSize=6,CostAdjustment=0.75, DisplayWhenUnHacked=False, DisplayWhenHacked=True)
VendingLootSpec=(ItemClass=class'ShockDesignerClasses.MedHypo', PickupClass=class'ShockDesignerClasses.MedHypoPickup', StackSize=1,CostAdjustment=1, DisplayWhenUnHacked=True, DisplayWhenHacked=False)
VendingLootSpec=(ItemClass=class'ShockDesignerClasses.MedHypo', PickupClass=class'ShockDesignerClasses.MedHypoPickup', StackSize=1,CostAdjustment=0.75, DisplayWhenUnHacked=False, DisplayWhenHacked=True)
VendingLootSpec=(ItemClass=class'ShockDesignerClasses.Vodka', PickupClass=class'ShockDesignerClasses.VodkaPickup', StackSize=1,CostAdjustment=1, DisplayWhenUnHacked=True, DisplayWhenHacked=False)
VendingLootSpec=(ItemClass=class'ShockDesignerClasses.Vodka', PickupClass=class'ShockDesignerClasses.VodkaPickup', StackSize=1,CostAdjustment=0.5, DisplayWhenUnHacked=False, DisplayWhenHacked=True)
```

Each `VendingLootSpec=` row is one item for sale:

| Field | Meaning |
| --- | --- |
| `ItemClass` | The inventory item that the player receives. |
| `PickupClass` | The world pickup that the machine drops. The item comes out of the machine as a pickup. |
| `StackSize` | How many units one purchase gives. Six pistol rounds, one first aid kit. |
| `CostAdjustment` | The price multiplier. The game computes the price as the item's `CreditValue` times `CostAdjustment`. `CreditValue` is built into the item class. |
| `DisplayWhenUnHacked` | The row is for sale before the machine is hacked. |
| `DisplayWhenHacked` | The row is for sale after the machine is hacked. |
| `SupplySize` | How many units the machine holds. A row without it is not limited. |

The game's tables list each item twice: one row for the unhacked machine at the full price,
and one row for the hacked machine at a lower `CostAdjustment`. Some tables add rows that are
for sale only after a hack, for example `ShockGame.Film`. That is what a hacked machine shows:
the hacked rows, at their lower prices, plus the hack-only rows. See
[32-Hacking.md](32-Hacking.md).

### A table of your own

A custom table is a new section in the same format plus a `VendingTableName=` line under
`[ShockGame.TableList]`. The game reads the vending tables from its own copy of the
configuration, not from the SDK folder. A table that exists only in the SDK's
`System\LootTables.ini` is not expected to work in the game. Choose an existing table.

## The machines

### Circus of Values and El Ammo Bandito

`PlaceableVendingStation` is the Circus of Values. `PlaceableVendingStationAlt` is the El
Ammo Bandito: the same machine with the ammunition skin and the other voice. Set
`VendingTableName` to a vending table and `HackInfoName` to a hack puzzle, for example
`VendingStationDeck1`. The game's own maps give the Bandito a `DeckNBanditoTable` and the same
`HackInfoName` as the Circus of Values of the level. Use is free; the rows are priced in
dollars.

### Gatherer's Garden

`PlaceableGrowthStation` is the same vending machine with a growth table and a different
mesh. Its rows are plasmids, tonics and upgrades, and the currency is ADAM. This is
`[DeckOneGrowthTable]`:

```ini
[DeckOneGrowthTable]
VendingLootSpec=(ItemClass=class'ShockGame.BerserkRage', PickupClass=class'ShockDesignerClasses.MedHypoPickup', StackSize=1,CostAdjustment=2, DisplayWhenUnHacked=True, DisplayWhenHacked=False, SupplySize=1)
VendingLootSpec=(ItemClass=class'ShockGame.ArmoredBody', PickupClass=class'ShockDesignerClasses.MedHypoPickup', StackSize=1,CostAdjustment=1, DisplayWhenUnHacked=True, DisplayWhenHacked=False, SupplySize=1)
VendingLootSpec=(ItemClass=class'ShockGame.MedHypoOmnisynthesis', PickupClass=class'ShockDesignerClasses.MedHypoPickup', StackSize=1,CostAdjustment=1, DisplayWhenUnHacked=True, DisplayWhenHacked=False, SupplySize=1)
VendingLootSpec=(ItemClass=class'ShockGame.HealthUpgrade', PickupClass=class'ShockDesignerClasses.MedHypoPickup', StackSize=1,CostAdjustment=1, DisplayWhenUnHacked=True, DisplayWhenHacked=False, SupplySize=1)
VendingLootSpec=(ItemClass=class'ShockGame.BioAmmoUpgrade', PickupClass=class'ShockDesignerClasses.MedHypoPickup', StackSize=1,CostAdjustment=1, DisplayWhenUnHacked=True, DisplayWhenHacked=False, SupplySize=1)
```

- `SupplySize=1` means that each item can be bought one time per Garden. Every row of the
  game's growth tables has it.
- `PickupClass` has no meaning for a plasmid; the game's tables put `MedHypoPickup` there.
- A Garden cannot be hacked, so it has no `HackInfoName` and only the unhacked rows count.
- The Garden does not give ADAM. The player's ADAM comes from the Little Sisters and the
  corpses of [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md)
  and [30-Corpses.md](30-Corpses.md), or from a script. A map with a Garden and no ADAM income
  has a Garden that sells nothing the player can afford.

The game's own maps set `DrawScale` to 1.25 on the Garden in the Display category. Do the
same to match the game's size.

### Health Station

`PlaceableHealthStation` heals the player to full health for 16 dollars. A player at full
health cannot use it. Set `HackInfoName`, for example `HealthStationDeck1`. The station can be
destroyed: when it takes enough damage it changes to its broken mesh and drops first aid
kits.

**NOTE:** Splicers cannot use health stations on custom maps. The AI needs a navigation point
at the station that the SDK does not write. `bUsableByAIs` on the station and the
scripting action `ActionSetHealthStationUsableByAIs` change nothing on a custom map. The
player is unaffected.

### U-Invent

`PlaceableCraftingStation` shows the formulas the player owns and makes the item when the
player has the components. It has no catalogue of its own. A formula is an inventory item,
never a placed actor. Give it to the player through a loot slot, for example with
`ItemClass` = `Class'ShockDesignerClasses.AntipersonnelBulletFormula'` in a
`LootItemSpecification`, and give the components the same way. The formula classes are in
`ShockDesignerClasses.pkg` under `CraftingFormulas`. See
[24-Pickups-and-Loot.md](24-Pickups-and-Loot.md). Set `HackInfoName`, for example
`CraftingStationDeck3`. A hacked U-Invent needs 20 percent fewer components.

### Gene Bank

`PlaceablePlasmidEquipStation` opens the plasmid and tonic loadout. There is nothing to set.
It cannot be hacked and it costs nothing.

### Power to the People

`PlaceableWeaponUpgradeStation` offers one upgrade for one of the player's weapons. After one
upgrade the station closes and shows as a closed station. There is nothing to set. It cannot
be hacked. The game's own maps set `DrawScale` to 0.6 in the Display category. Do the same to
match the game's size.

### Vita-Chamber

`ResurrectionStation` is the Vita-Chamber. It is not a `ShockMachine`; it sits directly under
`Actor` > `BaseResurrectionStation` in the tree. It needs a mesh, a Label and an activation.

1. In the Static Mesh browser, open `Content\MA_Resurrection.pkg` with File > Open... The
   chamber's stand-in mesh is in it.
2. Select `ResurrectionStation` and add it to the map. Leave room in front: the doors open.
3. Press F4. In the Display category, set `StaticMesh` to
   `StaticMesh'MA_Resurrection.Resurrection'`. Every chamber of the game's own maps carries
   this mesh. The BioShock editor shows this stand-in; the game shows the animated chamber.
4. In the Scripting category, set a unique `Label`, for example `ResStation_Lobby`. The
   scripting actions address the chamber by this Label.
5. Leave `CollisionRadius` and `CollisionHeight` at 100 and 260. The doors need the space.

**CAUTION:** A chamber that is not activated is ignored. When the player dies and no chamber
in the map is activated, the game ends and returns to the main menu. Activate the chamber with
a script.

The game's own maps activate each chamber from a trigger that the player crosses on the way
past it, with `ActionActivateResurrectionStation`:

1. Place a TriggerVolume or a TriggerRadius in front of the chamber, on the player's route.
   Set its `Label`, for example `ResTrigger_Lobby`.
2. Place a Script. Set `TriggeredBy` to the trigger's Label and `scriptMessageClass` to the
   trigger's enter message.
3. Add an action, pick `ActionActivateResurrectionStation` and click **New**. Set
   `ResurrectionStationLabel` to the chamber's Label. Leave `ActivateStation` True. The row
   reads `Activate resurrection station with label ResStation_Lobby.`
4. Save the map and run Build > Play Level (Ctrl+P). Walk past the chamber, then die. The
   chamber revives the player.

A script that runs the same action with `ActivateStation` False deactivates the chamber. The
game's own maps do this when the player leaves an area. When several chambers are activated,
the game revives the player at the nearest one.

The class also carries `ActivateByPlayer`, True by default, which should let the player
activate a chamber by using it. This is not tested; use the script.

`ActionDisableOrEnableResurrectionStation` with `Enable` False makes a chamber unavailable, as
the broken chambers of the game's own maps. An unavailable chamber does not revive the player
even when it is activated. See
[22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md).

## Hacking

`HackInfoName` picks the section of `System\Hacking.ini` that defines the machine's puzzle:
the cost of a buy-out, the tiles, the alarm bots. The sections are named `<Type>Deck<N>`,
for example `VendingStationDeck1`, `HealthStationDeck2` and `CraftingStationDeck3`, and each
type has a `<Type>Default` section. The game's own maps use the section of their level number.
A hacked vending machine shows its hacked rows and a hacked U-Invent needs 20 percent fewer
components. A Gatherer's Garden, a Gene Bank and a Power to the People station cannot be
hacked. The puzzle, the buy-out and the alarm are covered in [32-Hacking.md](32-Hacking.md).

## Bot Shutdown Panel

The Bot Shutdown Panel, `PlaceableSecurityStation`, is a machine too. It belongs to the
security system and is covered in [31-Security.md](31-Security.md).

## What the bake does

- When the map has a spawning manager, the bake registers every health station in it and
  writes one line per station to `System\Editor.log`:

  ```
  Bake: registered PlaceableHealthStation_0 in SpawningManager.HealthStations
  ```

  A map without a spawning manager gets no line. The registration is harmless either way;
  splicers do not use the stations on custom maps.
- The bake writes the machines' sounds and effects from the effects database. The
  `Bake: fx-db` lines record it. There is nothing to add for a machine.
- The bake embeds the chamber's animation package whole, and counts it in the line
  `Bake: N animation package(s) registered for whole embedding`.
- The machine meshes come from `MA_Vending.pkg`, `MA_plasmidgrowth.pkg`, `MA_Health.pkg`,
  `MA_Crafting.pkg`, `MA_Plasmid_equip.pkg`, `MA_WeaponUpgrade.pkg` and
  `MA_Resurrection.pkg`. The classes reference them and the bake embeds them. You load
  `MA_Resurrection.pkg` by hand only because you set the chamber's `StaticMesh` yourself.

## Give the player money and ADAM for a test

A machine on an empty map has a player with no dollars and no ADAM. Two ways to give some:

- A loot slot with a `LootItemSpecification` whose `ItemClass` is `Class'ShockGame.Credits'`
  or `Class'ShockGame.ADAM'`, and `MinStackSize` and `MaxStackSize` set to the amount, on a
  pickup or in a container. See [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md).
- A script with `ActionGiveItemsToPlayer`, `ItemClass` = `Credits` or `ADAM` and `StackSize`
  = the amount, run from a level-start script. See
  [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md).

## Limits

- `VendingTableName` and `HackInfoName` have no drop-down. Type the names exactly as they
  appear in `System\LootTables.ini` and `System\Hacking.ini`.
- Splicers do not use health stations on custom maps.
- A vending table of your own is not delivered to the game. Choose an existing table.
- The BioShock editor draws the Vita-Chamber as its static stand-in mesh, not as the animated
  chamber.

## Related documents

- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)
- [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md)
- [30-Corpses.md](30-Corpses.md)
- [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md)
- [31-Security.md](31-Security.md)
- [32-Hacking.md](32-Hacking.md)
