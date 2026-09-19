# Pickups and Loot

This document tells you how to place a weapon, ammunition or item pickup that the player can
take, and how loot is specified: on a pickup, in a corpse or a container, and on the AI that
the spawning manager creates. The "Loot specifications" section is the reference that
[29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md) and
[30-Corpses.md](30-Corpses.md) point to.

**CAUTION:** None of the game's gameplay systems has been extensively tested on custom maps.
That includes the systems that the SDK's authors have run on maps of their own. Expect
behaviour that differs from the game's own maps, and test every setup in the game.

## Before you start

- You can place actors and edit their properties in the Properties window. See
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- The map has built BSP geometry. Pickups rest on it. See
  [07-BSP-Geometry.md](07-BSP-Geometry.md).
- You can bake the map and start it in the game with Play Map. See
  [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).

## Which classes to place

The placeable pickups are the preset classes in `ShockDesignerClasses.pkg`. In the Actor
Class browser, use File > Open Package to load `Content\ShockDesignerClasses.pkg` from the
content library. The weapon pickups sit at this path in the tree:

```
Actor
  ReactiveActor                 (VengeanceShared)
    PhysicalReactiveActor       (ShockGame)
      Pickup                    (ShockGame)
        WeaponPickups           (ShockDesignerClasses)
          WrenchPickup
          PistolPickup
          ShotgunPickup
          ...
```

`WrenchPickup`, `PistolPickup`, `ShotgunPickup`, `GrenadeLauncherPickup` and
`ResearchCameraPickup` are the world actors: each has its world mesh preset. The package
holds more groups under `Pickup` for the other item kinds: `AmmoPickups`,
`ConsumablePickups`, `HypoPickups`, `PlasmidPickups`, `CraftingComponentPickups`,
`LiquorPickups`, `QuestPickups` and others. Expand a group to see its classes, for example
`PistolAmmoPickup` and `MachineGunBulletPickup` under `AmmoPickups`.

**CAUTION:** Do not place `ShockGame.WeaponPickup`. It is the inventory-side item, not a
world actor. The classes whose name ends in `PickupItem`, for example `WrenchPickupItem` and
`PistolPickupItem`, are inventory items too. They are what you put inside a loot slot, not what
you place in the map.

The models of the ammunition pickups are in the weapon packages of the content library:
`WP_Pistol.pkg` holds the pistol rounds and `WP_Shotgun.pkg` the shotgun shells.

## Place a pickup

1. In the Actor Class browser, select the pickup class, for example `WrenchPickup`.
2. Right-click the floor and click **Add WrenchPickup here**.
3. Press F4 and set the loot slot. See the next section.

### The loot slot

The class of a pickup does not say what the player receives. The pickup draws its item from
its `LootSlot`. Without a loot slot, the game shows the use prompt, but the use does nothing.

1. Click the `LootSlot` row. Click its `New` row, pick `LootSlot` and click **New**.
2. Inside it, click the `LootSpec` row. Click its `New` row, pick `LootItemSpecification` and
   click **New**.
3. Set `ItemClass` to the matching inventory item, for example
   `Class'ShockDesignerClasses.WrenchPickupItem'`.
4. Set `MinStackSize` to 1 and `MaxStackSize` to 1.

A `LootTableSpecification` works in a loot slot too. See "Loot specifications".

### bShowHudElements

`bShowHudElements`, in the HUDDisplay category, must be True for the pickup to show its use
prompt. It is True on a placed pickup. A pickup whose `bShowHudElements` is False shows no
prompt and cannot be used. Scripts in the game's own maps switch it off and on with
`ActionSetProperty` during the intro. See [23-Scripting-Examples.md](23-Scripting-Examples.md).

### Physics

A pickup is a Havok rigid body. On built BSP geometry it rests on the floor. On geometry
that has no Havok shape it falls through the floor when the map starts. When that happens,
set `Physics` in the Movement category to `PHYS_None`. The pickup then stays where you
placed it.

### Weapon models

The first-person and third-person models of a weapon are not in the map. They are in the
game's own packages and the weapon class finds them. The map only needs the pickup's world
mesh, which the pickup class references and the bake embeds. There is nothing to embed by
hand.

### Test

Save the map and run Build > Play Level (Ctrl+P). Walk to the pickup and use it. The item
enters the inventory. A weapon can be equipped from the weapon wheel and used.

## Loot specifications

A loot specification says what a slot holds. Two classes exist. Both are used in the same
places: the `LootSlot` of a pickup, the three `LootSlots` of a `Container`, and the
`LootContainer` of an AI's loot entry.

### LootItemSpecification

A fixed item. Fields:

| Property | Meaning |
| --- | --- |
| `ItemClass` | The inventory item class, for example `Class'ShockDesignerClasses.WrenchPickupItem'` or `Class'ShockGame.ADAM'`. |
| `MinStackSize` | The smallest count. |
| `MaxStackSize` | The largest count. The game rolls a count between the two, inclusive. |

For one item, set both sizes to 1.

### LootTableSpecification

A random roll on a named table. One field:

| Property | Meaning |
| --- | --- |
| `TableName` | The name of a section of `System\LootTables.ini`, without the brackets. |

### The loot tables

`System\LootTables.ini` holds the game's loot tables. Each table is one section. A section
holds `LootSpec=` lines, one per possible result. This is the table for a Little Sister of
the fourth level of the game, and one of the three tables of a melee splicer of that level:

```ini
[Deck4GathererA]
LootSpec=(Chance=100, ItemClass=class'ShockGame.ADAM',MinStackSize=160,MaxStackSize=160)

[Deck4MeleeThugB]
LootSpec=(Chance=28, ItemClass=None)
LootSpec=(Chance=40, ItemClass=class'ShockGame.Credits',MinStackSize=4,MaxStackSize=8)
LootSpec=(Chance=6, ItemClass=class'ShockGame.Pistol_Bullet', MinStackSize=1,MaxStackSize=8)
LootSpec=(Chance=2, ItemClass=class'ShockGame.Pistol_ArmorPiercing',MinStackSize=2,MaxStackSize=4)
LootSpec=(Chance=6, ItemClass=class'ShockGame.Shotgun_00Buck', MinStackSize=1,MaxStackSize=8)
LootSpec=(Chance=2, ItemClass=class'ShockGame.Shotgun_IonicBuck', MinStackSize=4,MaxStackSize=8)
LootSpec=(Chance=6, ItemClass=class'ShockGame.ChemicalThrower_Kerosene', MinStackSize=50,MaxStackSize=100)
LootSpec=(Chance=2, ItemClass=class'ShockGame.ChemicalThrower_LiquidNitrogen', MinStackSize=40,MaxStackSize=80)
LootSpec=(Chance=6, ItemClass=class'ShockGame.MachineGun_Bullet', MinStackSize=20,MaxStackSize=40)
LootSpec=(Chance=2, ItemClass=class'ShockGame.MachineGun_FrozenBullet', MinStackSize=15,MaxStackSize=25)
```

How a table is read:

- Each `LootSpec` line is one weighted entry. `Chance` is the weight. The game picks one entry
  per roll. The `Chance` values of a table in the game's own tables add up to 100, so read
  each one as a percentage.
- `ItemClass` is the item, and the count is rolled between `MinStackSize` and `MaxStackSize`.
- `ItemClass=None` is a deliberate empty result. In `Deck4MeleeThugB`, 28 percent of the rolls
  give nothing.
- `TableName=` in place of `ItemClass` nests another table: when that entry is picked, the game
  rolls on the named table instead. Example: `LootSpec=(Chance=20,TableName=TestTableD)`.
- The section `[ShockGame.TableList]` at the top of the file lists every table by name, one
  `TableName=` line per table. Every table of the game is listed there.

The gatherer tables hold one entry with `Chance=100`, which is why a Little Sister's ADAM is
always the same amount.

**NOTE:** The game reads the loot tables from its own configuration, not from the SDK
folder. Expect a table that exists only in the SDK's `System\LootTables.ini` to be unknown
to the game. For loot that the game's tables do not offer, use a `LootItemSpecification`.

### Containers

A `Container` has three slots, `LootSlots[0]` to `LootSlots[2]`. Each slot has its own
`LootSpec`, and each slot rolls on its own. That is how the game's splicers vary: one slot
rolls a crafting components table, one a table with an empty result and money, one an
ammunition table. A slot with no `LootSpec` is empty. The container's `AllowDifficultySpawn`
and `AllowDifficultyRemove` flags are best left at their defaults.

The scripting actions that fill, empty and count containers are listed under "Containers and
inventory" in [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md).

## What an AI carries

What the player finds in a dead splicer, and how much ADAM a Little Sister
pays, is not on the archetype and not on the spawner. It is on the spawning manager, in
`DefaultAILoot`. When an AI spawns, the spawning manager looks for the entry whose `AIType`
has the same class name as the spawned AI, and gives the AI a copy of that entry's container.
No entry means no container: the corpse holds nothing, and a Little Sister pays 0 ADAM.

1. Open Level Properties with F6 and expand Spawning > `SpawningManager` > `DefaultAILoot`.
2. Click the `DefaultAILoot` row and click **Add**. Click the entry's `New` row, pick
   `AIContainerInfo` and click **New**.
3. Set `AIType` to the spawned class, for example `Class'ShockAIClasses.SpawnedMeleeThug'`,
   `Class'ShockAIClasses.SpawnedBouncer'` or `Class'ShockAIClasses.SpawnedGatherer'`.
   `ShockAIClasses.pkg` must be loaded for the classes to appear.
4. Expand `LootContainer`, click its `New` row, pick `Container` and click **New**.
5. Fill the `LootSpec` of each slot you want, with a `LootTableSpecification` or a
   `LootItemSpecification`.
6. Repeat for every class that the map spawns.

The game's own maps add one entry per spawned class. Their tables are named
`Deck<N><Type><A|B|C>`: `N` is the level number, `Type` the AI kind, and `A`, `B`, `C` are the
three slots. Examples: `Deck1MeleeThugA`, `Deck2RosieB`, `Deck3AssassinC`, `Deck4GathererA`.
Pick the tables of the level whose loot you want.

See [27-Spawning-AI.md](27-Spawning-AI.md) for the spawning manager and the spawned classes,
and [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md) for the
Little Sister's ADAM.

## Related documents

- [07-BSP-Geometry.md](07-BSP-Geometry.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)
- [27-Spawning-AI.md](27-Spawning-AI.md)
- [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md)
- [30-Corpses.md](30-Corpses.md)
