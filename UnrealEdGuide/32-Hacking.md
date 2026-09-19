# Hacking

This document tells you how hacking is set up on a map: which actors the player can hack, the
one property you set on each of them, what a puzzle section of `System\Hacking.ini` holds, what
a successful hack does, what a failed hack does, and how a script reacts to a hack. The machines
themselves are covered in [25-Machines.md](25-Machines.md) and the turrets, cameras and bots in
[31-Security.md](31-Security.md).

**CAUTION:** None of the game's gameplay systems has been extensively tested on custom maps.
That includes the systems that the SDK's authors have run on maps of their own. Expect
behaviour that differs from the game's own maps, and test every setup in the game.

## Before you start

- You can place actors and edit their properties in the Properties window. See
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- You have placed the machine, safe, keypad or spawner that the player hacks. See
  [25-Machines.md](25-Machines.md) and [31-Security.md](31-Security.md).
- You can bake the map and start it in the game with Play Map. See
  [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).
- For a script that reacts to a hack, you can make a Script with one action. See
  [20-Scripting-Basics.md](20-Scripting-Basics.md).

## How hacking works

Hacking is not a system that you build. It is a property of certain actors. When the player
looks at one of them, the game shows the hack prompt next to the use prompt. When the player
hacks, the game opens the pipe puzzle. The puzzle, the buy-out price, the alarm bots and the
damage on failure all come from one section of `System\Hacking.ini`.

The one thing you set is `HackInfoName`: the name of that section. Everything else, the rewards
and the failures, is built into the classes. A hacked vending machine sells cheaper, a hacked safe
opens, a hacked turret fights for the player. You do not set any of that.

## Which actors can be hacked

| Actor | Class | Where | What to set |
| --- | --- | --- | --- |
| Health Station | `PlaceableHealthStation` | `ShockDesignerClasses.pkg` | `HackInfoName` (Hacking) |
| Circus of Values | `PlaceableVendingStation` | `ShockDesignerClasses.pkg` | `HackInfoName` (Hacking) |
| El Ammo Bandito | `PlaceableVendingStationAlt` | `ShockDesignerClasses.pkg` | `HackInfoName` (Hacking) |
| U-Invent | `PlaceableCraftingStation` | `ShockDesignerClasses.pkg` | `HackInfoName` (Hacking) |
| Floor safe | `SecurityCrate_Safe` | `ShockDesignerClasses.pkg` | `HackInfoName` (Hacking) |
| Wall safe | `SecurityCrate_WallSafe` | `ShockDesignerClasses.pkg` | `HackInfoName` (Hacking) |
| Door keypad | `DoorKeypadControl` | The `ShockGame` package | `HackInfoName` (Hacking), `DoorLabel` (Door) |
| Turret | `TurretSpawner` | The `ShockAI` package | `HackInfoName` (Hacking) on the spawner |
| Security camera | `SecurityCameraSpawner` | The `ShockAI` package | `HackInfoName` (Hacking) on the spawner |
| Security bot | `SecurityBotSpawner` | The `ShockAI` package | The three names on the spawning manager, see below |

The category of the property is in parentheses. To place the machines and the safes, load
`Content\ShockDesignerClasses.pkg` in the Actor Class browser with File > Open Package. The
safes sit under `Actor` > ... > `ReactiveAnimatedMesh` > `AnimatedContainer` > `SecurityCrate`.
`DoorKeypadControl` sits under `Actor` > `DoorAttachment` > `DoorAccessControl`; set its
`DoorLabel` to the Label of the door it controls. The spawners are covered in
[31-Security.md](31-Security.md). A turret, a camera and a bot exist only in the game, so their
hack settings live on the spawner that makes them.

These cannot be hacked: the Gene Bank, the Power to the People station, the Gatherer's Garden,
the Bot Shutdown Panel and the Little Sisters. They have no puzzle to set.

## Set the puzzle

1. Select the actor and press F4.
2. In the Hacking category, click `HackInfoName` and type the full section name, for example
   `HealthStationDeck1`. There is no drop-down. Type the name exactly as it appears in
   `System\Hacking.ini`, without the brackets.
3. Leave `bCanBeHacked`, `CanBeHacked` and `HackPurchaseOptionEnabled` at their defaults. The
   game's own maps never change them.

The sections are named `<Kind>Deck<N>`. `Kind` is `HealthStation`, `VendingStation`,
`CraftingStation`, `SecurityCrate`, `Keypad`, `Turret`, `SecurityCamera` or `SecurityBot`. `N`
is 1 to 7, or `7Plus`; `CraftingStationDeck` starts at 3. The Circus of Values and the El Ammo
Bandito share the `VendingStation` sections, and the floor and wall safes share the
`SecurityCrate` sections. The game's own maps use the deck number of the level, and move single
actors one deck up or down to vary the difficulty. Any section works on any actor: one safe in
the game's own maps uses `HealthStationDeck2`.

**NOTE:** The section names of one kind: `HealthStationDeck1`, `HealthStationDeck2`, ...,
`HealthStationDeck7`, `HealthStationDeck7Plus`. The `7Plus` boards are the hardest.

### The security bots

Bot initialization reads a puzzle name from the spawning manager, according to bot grade.
For custom maps, configure the manager settings for placed bots and alarm bots:

1. Open Level Properties with F6 and expand Spawning > `SpawningManager` > SecurityBotHackInfo.
2. Set `MinimumSecurityBotHackInfoName`, `MediumSecurityBotHackInfoName` and
   `MaximumSecurityBotHackInfoName` to a `SecurityBotDeck<N>` section. The game's own maps set
   all three to the same section, for example `SecurityBotDeck2`.

The scripted tutorial bot has `HackInfoName=FirstBotHack` on its spawner, in the SecurityBot category.
Outside that tutorial, the spawner setting's effect and its precedence over manager settings are unverified.
Do not rely on a general spawner override. See [31-Security.md](31-Security.md#hacking).

**CAUTION:** A name that has no section is not an error. The game opens a default board with a
10-credit entry fee, no pipe tiles and three alarm bots. There is no warning in the BioShock
editor, at bake time or in the game. Check the spelling against `System\Hacking.ini`.

**NOTE:** Every kind has a `<Kind>Default` section, for example `HealthStationDefault`. These are
placeholder boards: every cell is a revealed overload tile. Never use them, and never copy them
for a puzzle of your own. `MachineDefault`, the class default of every machine, is the same kind
of placeholder.

## What a section holds

A section describes one board. This is `[HealthStationDeck1]`, the first health station puzzle
of the game:

```ini
[HealthStationDeck1]
MinimumHackingLevel=0
InitSpeed=10
NormalSpeed=8
SlowSpeed=16
FastSpeed=4
TradeSpeed=0.33
TotalPieces=36
CheckPointCount=0
AlarmCount=0
ShortCircuitCount=0
AcceleratorHCount=0
AcceleratorVCount=0
ResistorVCount=0
ResistorHCount=0
StraightHCount=6
StraightVCount=5
ElbowTRCount=4
ElbowBRCount=6
ElbowTLCount=6
ElbowBLCount=5
StartRow=3
StartColumn=-1
EndRow=6
EndColumn=1
WinRow=1
WinColumn=6
HackCost=0
AlarmBotType="ShockAIClasses.SpawnedDeck1MediumSecurityBot"
AlarmBotCount=1
DamageDealtOnOverload=150
DamageDealtOnShortCircuit=50
RevealedTile=(Type=elbowTR,Row=3,Column=0)
RevealedTile=(Type=straightV,Row=4,Column=0)
RevealedTile=(Type=elbowBL,Row=5,Column=0)
RevealedTile=(Type=elbowTR,Row=5,Column=1)
MinimumPurchaseOptionCost=10
```

The keys:

| Key | Meaning |
| --- | --- |
| `MinimumHackingLevel` | The engineering skill the player needs to open the puzzle. 0 in every section of the game. |
| `HackCost` | The credits the player must have to attempt the hack. Every section of the game sets 0. A section without this key charges 10. |
| `InitSpeed`, `NormalSpeed`, `SlowSpeed`, `FastSpeed` | Seconds per tile for the flow: before it starts, on a plain pipe, on a resistor tile, on an accelerator tile. Lower is harder. |
| `TradeSpeed` | Seconds for a tile swap. 0.33 everywhere. |
| `TotalPieces` | The size of the board: 25 for a 5 by 5 board, 36 for a 6 by 6 board. |
| `StraightHCount`, `StraightVCount`, `ElbowTRCount`, `ElbowBRCount`, `ElbowTLCount`, `ElbowBLCount` | The pipe tiles in the tray: straight horizontal, straight vertical, and the four elbows. |
| `AlarmCount` | Alarm tiles. The flow reaching one starts the alarm. |
| `ShortCircuitCount` | Overload tiles. The flow reaching one hurts the player. |
| `AcceleratorHCount`, `AcceleratorVCount` | Fast-flow tiles. |
| `ResistorVCount`, `ResistorHCount` | Slow-flow tiles. They lower the buy-out price. |
| `CheckPointCount` | Green checkpoint tiles. 0 in every section of the game. Not part of the tile budget. |
| `StartRow`, `StartColumn`, `EndRow`, `EndColumn`, `WinRow`, `WinColumn` | Where the flow enters, where it must exit, and the win socket. A value of -1 or of the board size is the edge outside the board. |
| `DamageDealtOnOverload` | The damage the player takes on an overload tile. |
| `DamageDealtOnShortCircuit` | The damage the player takes on any other failure. |
| `RevealedTile=(Type=,Row=,Column=)` | A tile placed face-up before the puzzle starts. One line per tile. `Type` is `straightH`, `straightV`, `elbowTR`, `elbowBR`, `elbowTL`, `elbowBL`, `alarm`, `ShortCircuit`, `acceleratorV` and the other tile kinds. |
| `MinimumPurchaseOptionCost` | The floor of the buy-out price. |
| `AlarmBotType`, `AlarmBotCount` | The bot class and the number of bots that an alarm tile releases. |

### The board sizes and the tile budget

The game's sections use two sizes: `TotalPieces=25` for the keypads, the U-Invents and the
safes, and `TotalPieces=36` for the rest. The twelve tile counts, the six pipe counts plus
`AlarmCount`, `ShortCircuitCount`, the two accelerator counts and the two resistor counts, add
up to `TotalPieces` minus the number of `RevealedTile` lines. In `[HealthStationDeck1]`, 6 + 5 +
4 + 6 + 6 + 5 = 32, and 36 minus 4 revealed tiles = 32. Every deck section of the game keeps
this budget.

### How the difficulty grows

A deck 1 board has no alarm tiles and no overload tiles, a slow flow and a buy-out floor of 10.
From deck to deck the flow gets faster, alarm and overload tiles replace pipe tiles, the floor
rises, the damage rises and the alarm releases more bots. `[HealthStationDeck7Plus]` has 8 alarm
tiles, 8 overload tiles, `NormalSpeed=3`, three `SpawnedDeck7MediumSecurityBot` on an alarm,
500 damage on an overload and 200 on another failure. The `AlarmBotType` of a deck section is
always the medium security bot of that deck, `ShockAIClasses.SpawnedDeck<N>MediumSecurityBot`.

### The buy-out price

The puzzle offers a buy-out. The game computes the price from the board: the alarm, overload
and accelerator tile counts add to it, the resistor tiles take away from it, and a faster
`NormalSpeed` multiplies it. The price is never below `MinimumPurchaseOptionCost`. The player's
tonics remove tiles before the count, so the price falls with them. A deck 1 board has no alarm,
overload or accelerator tiles, so its price is the floor, 10. `[TurretDeck3]`, with 3 alarm
tiles, 2 overload tiles and `NormalSpeed=4`, costs 100. `[SecurityCrateDeck7]` costs 1200. The
scripting action `ActionEvaluateHack` returns this number.

## What a hack does

The reward is built into the class. Nothing is set on the map.

- A machine costs less to use. A hacked Health Station heals for 10 dollars in place of 16, and
  it hurts a splicer that uses it. Splicers do not use health stations on custom maps, so only
  the price counts there; see [25-Machines.md](25-Machines.md).
- A hacked Circus of Values or El Ammo Bandito sells its hacked rows: the same items at their
  lower prices, plus the rows that are for sale only after a hack. See "The stock of a vending
  machine" in [25-Machines.md](25-Machines.md).
- A hacked U-Invent needs 20 percent fewer components.
- A hacked safe opens and offers its `Container` loot. See "Safes" below.
- A hacked keypad unlocks its door, the door with the keypad's `DoorLabel`.
- A hacked turret fights for the player. A hacked camera reports the player's enemies to the
  alarm and its spotlight turns green. A hacked bot becomes the player's bot and fights for the
  player. A bot can be hacked only while it is dormant, frozen or shocked.

A hacked machine and a hacked safe stay hacked: the puzzle does not open on them again. A
keypad can be hacked again when its door is locked again.

## What a failure does

The puzzle ends in one of these ways:

- The flow reaches an alarm tile. The game starts the alarm against the player and releases
  `AlarmBotCount` bots of the class `AlarmBotType`. When an alarm against the player is already
  running, the game adds one bot to the running alarm, up to four bots.
- The flow reaches an overload tile. The player takes `DamageDealtOnOverload`.
- The flow runs out at any other point. The player takes `DamageDealtOnShortCircuit`.
- The player closes the puzzle without a try, or the puzzle closes because the player was
  attacked. Nothing happens.

The damage of a failure is never lethal: it is capped one point below the player's health. After
a failure the actor stays unhacked and the player can try again. A dormant security bot is the
exception: after a failure other than closing the puzzle, the bot explodes.

**CAUTION:** The alarm bots of a section must exist in the map. The bake embeds an AI class
only when a spawner in the map references it, not from `Hacking.ini`, and security bots
have no archetype. For a section of deck N, place a `SecurityBotSpawner` whose
`SecurityBotType` is `Class'ShockAIClasses.SpawnedDeck<N>MediumSecurityBot'`, with
`ForScriptedSpawn` True so that no bot appears from it. The behaviour of an alarm tile when
the bot class is not in the map is not tested. See [31-Security.md](31-Security.md) for the
alarm and the bots.

## Custom puzzles

A puzzle of your own is a new section in `System\Hacking.ini`:

1. Copy a `<Kind>Deck<N>` section under a new name. Never copy a `*Default` section.
2. Change the tile counts and keep the budget: the twelve counts add up to `TotalPieces` minus
   the number of `RevealedTile` lines.
3. Keep `HackCost=0`, or the player pays an entry fee.
4. Set `AlarmBotType` to a bot class that is in the map.
5. Add a `HackInfoName=<YourSection>` line under `[ShockGame.HackInfoList]` at the top of the
   file.
6. Type the new name in `HackInfoName` on the actor.

The game reads its own copy of the configuration, not the SDK folder. A section that exists
only in the SDK's `System\Hacking.ini` is not expected to work in the game: the actor opens
the default board with the 10-credit entry fee. Choose an existing section.

## Safes

`SecurityCrate_Safe` and `SecurityCrate_WallSafe` are locked containers. The player opens one
with a hack. The loot is the `Container` of the class: three slots, `LootSlots[0]` to
`LootSlots[2]`, each with its own `LootSpec`. The preset classes carry a container. To change the
loot, edit `Container` on the placed safe. See "Containers" in
[24-Pickups-and-Loot.md](24-Pickups-and-Loot.md).

Set `HackInfoName` to a `SecurityCrateDeck<N>` section.

## Keypad-only doors

A `DoorKeypadControl` offers two ways in: the player hacks it, or the player types the code. The
code is not on the keypad. The keypad sends `MessageDoorKeypadUsed` with the typed code in its
`Keycode` field, and a script judges it. Example 11 in
[23-Scripting-Examples.md](23-Scripting-Examples.md) shows the script.

For a door that opens only with the code, set `bHackable` to False on the keypad. The hack prompt
does not appear and the puzzle never opens. One keypad in the game's own maps is set this way.

## Scripting

- `MessagePlayerStartedHacking` — sent by the hacked actor when the puzzle opens. Set the
  Script's `TriggeredBy` to the actor's Label.
- `MessagePlayerFinishedHacking` — sent by the hacked actor when the puzzle ends. Its
  `SuccessfulHack` field is True on a success. Example 5 in
  [23-Scripting-Examples.md](23-Scripting-Examples.md) springs an ambush from a hacked safe.
- `ActionEvaluateHack` — returns the buy-out price of hacking the class named in
  `HackedClassName`.
- `ActionHackTurret` — makes every turret with `TurretLabel` friendly to the player, or hostile
  again, without a puzzle.
- `ActionHackSecuritySystem` — shuts the security system down for `ShutdownTime` seconds, as a
  hacked security station would.
- `ActionUnHackSecuritySystem` — ends that shutdown.

See "Machines, hacking and security" in
[22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md) for the fields and the row
text, and the message table in [20-Scripting-Basics.md](20-Scripting-Basics.md).

## Limits

- `HackInfoName` has no drop-down. Type the section name exactly.
- A name with no section gives the default board with the 10-credit entry fee. Nothing warns
  you.
- A section of your own is not delivered to the game. Choose an existing section.
- The alarm bots of a section are not embedded by the bake unless a spawner in the map
  references the class. The behaviour without them is not tested.
- The Bot Shutdown Panel cannot be hacked.
- Hacking has not been tested on a custom map by the SDK's authors.

## Related documents

- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)
- [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md)
- [25-Machines.md](25-Machines.md)
- [31-Security.md](31-Security.md)
