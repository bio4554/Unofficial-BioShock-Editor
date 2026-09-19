# Security

This document tells you how to put the game's security system in a map: security cameras,
turrets, security bots, the alarm that a camera starts, the Bot Shutdown Panel that stops
it, and the flight network that the bots need. Read it after
[27-Spawning-AI.md](27-Spawning-AI.md), which covers the spawning manager and the spawn zones.
Hacking is covered in [32-Hacking.md](32-Hacking.md).

**CAUTION:** None of the game's gameplay systems has been extensively tested on custom maps.
That includes the systems that the SDK's authors have run on maps of their own. Expect
behaviour that differs from the game's own maps, and test every setup in the game.

## Before you start

- You can place actors and edit their properties in the Properties window. See
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- The map has a spawning manager with at least one spawn zone. See
  [27-Spawning-AI.md](27-Spawning-AI.md).
- The map has a path network. Bots also need flying path nodes. See
  [26-AI-Paths.md](26-AI-Paths.md).
- You can bake the map and start it in the game with Play Map. See
  [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).
- For the scripted setups in this document, you can make a Script actor and add actions to
  it. See [20-Scripting-Basics.md](20-Scripting-Basics.md).

## How the security system works

A camera, a turret or a bot is never placed in a map as itself. You place a spawner actor,
and the spawning manager creates the pawn from it when the map starts. There are three
spawners, all in the `ShockAI` package under `Actor > SpawnerBase`:

| Spawner | Creates |
| --- | --- |
| `SecurityCameraSpawner` | One security camera. |
| `TurretSpawner` | One turret. |
| `SecurityBotSpawner` | One placed security bot. |

One spawner makes one pawn. The kind of pawn is a class from `ShockAIClasses.pkg` that you set
on the spawner: `CameraType`, `TurretType` or `SecurityBotType`. Cameras, turrets and bots are
not archetypes of the spawning system. They need no entry in the spawning manager's
`AIArchetypeNames`, and there is no section for them in `System\Spawning.ini`. Their health,
speed and weapons are built into the classes and the game's configuration. You do not set
them.

Every spawner has a `SpawnZones` list. Add the `SpawnZoneName` of your spawn zone to it. The
game's own maps set exactly one zone name on every security spawner.

**NOTE:** "Spawn zone" in this document means a logical zone of the spawning manager, a
`SpawnZoneInfo` with a name. It is not a BSP zone of
[08-Zones-and-Portals.md](08-Zones-and-Portals.md).

### The alarm

The alarm is run by a security manager that the game creates when the map starts. There is
nothing to place for it. The manager needs the map's spawning manager, so a map without a
spawning manager has no security system.

- A camera that watches the player for long enough starts the alarm. A camera of the first
  level of the game needs 4 seconds of contact; the cameras of later levels need less. The
  camera loses interest after 4 seconds without contact.
- The alarm lasts 60 seconds. During the alarm the manager creates security bots at a
  navigation point that the player cannot see, 3000 to 6000 units away from the player. When
  the alarm is against an AI, the distance is 1500 to 4000 units. The camera's class decides
  which bot and how many. A camera of the first level sends one medium bot; cameras of the
  third, fourth, sixth and seventh level send two.
- Turrets never start an alarm. They shoot what they see.
- A failed hack can start an alarm. See [32-Hacking.md](32-Hacking.md).
- The player stops an alarm at a Bot Shutdown Panel, or waits it out.
- A script can start and stop an alarm, and can shut the whole security system down. See
  "Scripting actions".

## Place a security camera

1. In the Actor Class browser, use File > Open Package to load `Content\ShockAIClasses.pkg`
   from the content library. It holds the camera classes.
2. Expand Actor > SpawnerBase and select `SecurityCameraSpawner`.
3. Right-click the ceiling, or the wall for a wall camera, and click **Add
   SecurityCameraSpawner here**.
4. Rotate the spawner so that it faces the direction the camera watches when nothing is
   in view. The spawner's rotation is the camera's rest direction. The yaw limits and the
   panning limits below are measured from it.
5. Press F4 and set the properties in the table below.

| Property | Category | Meaning |
| --- | --- | --- |
| `CameraType` | Camera | The camera class. Use `Class'ShockAIClasses.SpawnedDeck1SecurityCamera'` for a ceiling camera and `Class'ShockAIClasses.SpawnedDeck1SecurityCameraWall'` for a wall camera. The game's own maps use the class of their level number, `SpawnedDeck1...` to `SpawnedDeck7...`; the ceiling class exists for every level, the wall class for every level but the fourth. The class decides which bots the alarm sends. |
| `SpawnZones` | SpawnerBase | The name of your spawn zone. |
| `LeftYawLimit`, `RightYawLimit` | Camera | How far the camera turns to track a target, in degrees from the rest direction. Left is negative. Default -30 and 30. The game's own maps use -50 to 50 and -60 to 60. |
| `PanningLeftYawLimit`, `PanningRightYawLimit` | Camera | How far the camera sweeps while idle, in degrees from the rest direction. Default -30 and 30. Set both to 0 for a camera that does not sweep. The game's own maps use 0 and 0 on wall cameras. |
| `DefaultPitchDegrees` | Camera | The pitch of the camera at rest, in degrees. Negative looks down. Default -20. The game's own maps use -25 to -35. |
| `FOV` | Camera | The width of the view cone, in degrees. Default 60. The game's own maps use 60, 80 and, on one special camera, 180. |
| `SightDistance` | Camera | How far the camera sees, in units. Default 1000. The game's own maps use 1500 to 2000. |
| `HackInfoName` | Hacking | The hack puzzle, a section name of `System\Hacking.ini`. Default `SecurityCameraDefault`. See "Hacking". |
| `CanBeHacked` | Hacking | True by default. False makes a camera that the player cannot hack. |
| `StartHackedByPlayer` | Camera | True makes the camera start on the player's side. The game's own maps use it once, in the intro. |
| `SpawnedCameraLabel` | Camera | The Label that the created camera gets. `ActionToggleSecurityCameraSpotlight` finds the camera by it. |
| `LootContainer` | Camera | What searching the destroyed camera gives. See [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md). |

`UpperPitchLimit` and `LowerPitchLimit`, default 0 and -65, bound the pitch. Leave them.

**NOTE:** The `Spotlight` row in the Camera category is read-only. In the game's own maps it
points to a spotlight actor that sits at the camera's position. The BioShock editor does not
create that actor. See "Limits".

**NOTE:** The game's own maps give each spawner a preview model of the camera. That model is
the `Mesh` property in the Display category, and the spawner classes hide that category in
the Properties window. A spawner on a custom map has no preview model. The camera model
appears in the game, where the spawner creates the camera.

## Place a turret

A turret is a free-standing pawn on a wheeled base. It needs no mount.

1. Load `Content\ShockAIClasses.pkg` as for the camera.
2. Expand Actor > SpawnerBase and select `TurretSpawner`.
3. Right-click the floor and click **Add TurretSpawner here**.
4. Rotate the spawner so that it faces the direction the turret watches when it has no
   target. The spawner's rotation is the turret's standby direction.
5. Press F4 and set the properties in the table below.

| Property | Category | Meaning |
| --- | --- | --- |
| `TurretType` | Turret | The turret class. There are three grades, one per weapon: `Class'ShockAIClasses.SpawnedDeck1MinimumSecurityTurret'` is the machine gun turret, `Class'ShockAIClasses.SpawnedDeck1MediumSecurityTurret'` the flame turret, `Class'ShockAIClasses.SpawnedDeck1MaximumSecurityTurret'` the rocket turret. The game's own maps use the classes of their level number. |
| `SpawnZones` | SpawnerBase | The name of your spawn zone. |
| `SightDistance` | Turret | How far the turret sees, in units. Default 1000. The game's own maps use 650 to 4000. |
| `HackInfoName` | Hacking | The hack puzzle, a section name of `System\Hacking.ini`. Default `TurretDefault`. See "Hacking". |
| `CanBeHacked` | Hacking | True by default. False makes a turret that the player cannot hack. The game's own maps use False on the turrets of the intro. |
| `SpawnedTurretLabel` | Turret | The Label that the created turret gets. `ActionHackTurret` and `ActionReorientTurret` find the turret by it. |
| `ForScriptedSpawn` | Turret | False by default: the turret exists when the map starts. True: the turret is created only when a script runs `ActionSpawnTurret` with this spawner in `Spawner`. See "A turret as a trap". |
| `LootContainer` | Turret | What searching the destroyed turret gives. See [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md). |

`UpperPitchLimit`, `LowerPitchLimit` and `DefaultPitchDegrees`, default 15, -45 and 0, bound
the pitch. Leave them.

Not every grade exists for every level number in `ShockAIClasses.pkg`. The three grades all
exist for levels 1, 2, 3 and 7. The classes without a level number,
`SpawnedMinimumSecurityTurret`, `SpawnedMediumSecurityTurret` and
`SpawnedMaximumSecurityTurret`, exist too.

### A turret as a trap

The game's own maps hide a turret until a script wants it. Set `ForScriptedSpawn` to True on
the spawner and give the spawner a `Label`. In the script, add `ActionSpawnTurret` and set its
`Spawner` to the spawner. The turret appears when the action runs. See
[22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md).

### A turret on a mover

A turret can ride a platform. The game's own maps do it in the intro. Set the spawner's
`AttachTag` to the `Tag` of the mover and set `bHardAttach` to True, both in the Movement
category. The spawner then moves with the platform.

## Place a security bot

A `SecurityBotSpawner` makes one placed bot: a bot that exists from the start of the map, a
dormant bot that a script gives to the player, or an ally.

**NOTE:** The bots that an alarm sends do not come from this spawner. The security manager
creates them at a navigation point, and they need no spawner at all. See "The flight
network for bots".

1. Load `Content\ShockAIClasses.pkg` as for the camera.
2. Expand Actor > SpawnerBase and select `SecurityBotSpawner`.
3. Right-click the floor below the place where the bot appears and click **Add
   SecurityBotSpawner here**.
4. Press F4 and set the properties in the table below.

| Property | Category | Meaning |
| --- | --- | --- |
| `SecurityBotType` | SecurityBot | The bot class. `Class'ShockAIClasses.SpawnedDeck1MediumSecurityBot'` is the medium bot of the first level; `SpawnedDeck1...` to `SpawnedDeck7...` exist. `Class'ShockAIClasses.SpawnedMinimumSecurityBot'` and `Class'ShockAIClasses.SpawnedMaximumSecurityBot'` are the weakest and the strongest bot and have no level number. |
| `SpawnZones` | SpawnerBase | The name of your spawn zone. |
| `SpawnedSecurityBotLabel` | SecurityBot | The Label that the created bot gets. `ActionActivateSecurityBot` finds the bot by it. |
| `ForScriptedSpawn` | SecurityBot | False by default: the bot exists when the map starts, hostile. True: the bot is created only when a script runs `ActionSpawnSecurityBot` with this spawner in `Spawner`. |
| `StartHacked` | SecurityBot | True makes the bot start on the player's side. |
| `StartFrozen` | SecurityBot | True makes the bot start frozen. |
| `PlayStartupSequence` | SecurityBot | True by default: the bot plays its start-up when it appears. The game's own maps set it False on some scripted bots. |
| `HackInfoName` | SecurityBot | Empty by default. Retail tutorial data uses `FirstBotHack` here. Its precedence over manager settings is unverified outside that tutorial. For custom maps, set the manager's three puzzle names. See "Hacking". |
| `LootContainer` | SecurityBot | What searching the destroyed bot gives. See [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md). |

`ActionSpawnSecurityBot` can hand the bot to a pawn at once: set `ImmediatelyGiveBotToPawn`
to True and `ReceivingPawnLabel` to the pawn's Label. The game's own maps use this to give a
boss bots of its own. See [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md).

## Place a Bot Shutdown Panel

The Bot Shutdown Panel is the machine that stops an alarm. It is the class
`PlaceableSecurityStation` in `ShockDesignerClasses.pkg`, a machine like the ones of
[25-Machines.md](25-Machines.md).

1. In the Actor Class browser, use File > Open Package to load
   `Content\ShockDesignerClasses.pkg` from the content library.
2. Expand the tree down to ShockMachine > SecurityStation and select
   `PlaceableSecurityStation`. The `ShockGame` class `SecurityStation` above it cannot be
   placed.
3. Right-click the floor at the foot of a wall and click **Add PlaceableSecurityStation
   here**.
4. Rotate the panel so that its front faces the room. There is nothing to set.

The security manager finds every panel in the map by its class when the map starts. There is
nothing to link. What the panel does:

- The player can use it only while an alarm is on. Using it stops the alarm. When no alarm
  is on, the game shows "Alarm is already off."
- The panel cannot be hacked unless `AllowHacking`, in the Machine category, is True. The
  game's own maps never set it, and `System\Hacking.ini` has no puzzle section for a panel.
  Leave it False. A script shuts the security system down with `ActionHackSecuritySystem`
  instead.

The game's own maps set nothing on a panel except its position, its rotation and its
`Label`.

## The flight network for bots

Security bots fly. They move over the map's FlyingPathNodes, a second set of navigation
points that you place in the air. The BioShock editor does not place them for you.
Cameras and turrets do not move and need no flying nodes. The placement rules, the
distances and the collision radius are in the "Place flying path nodes" section of
[26-AI-Paths.md](26-AI-Paths.md). This document adds only what the alarm needs:

- The alarm creates its bots at a navigation point 3000 to 6000 units from the player that
  the player cannot see. Place flying nodes in the rooms and corridors around the fight, not
  only in the camera's room. A map whose only nodes are in view of the player, or closer
  than 3000 units, has nowhere to create the bots. What the game does in that case is not
  tested.
- The bots must be able to fly from that point to the player. Connect the far nodes to the
  nodes around the camera without a gap.
- The bot class that the alarm sends must be in the baked map. The bake embeds an AI class
  only when a spawner in the map references it, and security bots have no archetype, so
  `AIArchetypeNames` does not help. Place a `SecurityBotSpawner` whose `SecurityBotType` is
  the class of your camera's level, for example
  `Class'ShockAIClasses.SpawnedDeck1MediumSecurityBot'`, with `ForScriptedSpawn` True so
  that no bot appears from it. What the alarm does when the class is not in the map is not
  tested.
- To force the point where the next alarm bots appear, give a navigation point a `Label`
  and run `ActionAssignNextSecurityBotSpawnLocation` with that Label in `SpawnLocationLabel`
  before the alarm. The game's own maps use a FlyingPathNode for this in one place and a
  ground PathNode in another; both kinds work.

Run Build > Rebuild AI Paths after you place the nodes, and check the links with Show Paths
as described in [26-AI-Paths.md](26-AI-Paths.md). The flying links draw in their own colour.

## Hacking

`HackInfoName` picks the section of `System\Hacking.ini` that defines the puzzle: the cost of
a buy-out, the tiles, and the bots that a failed hack sends. Set it to a full section name.
Use `TurretDeck1` to `TurretDeck7`, `SecurityCameraDeck1` to `SecurityCameraDeck7`, or
`SecurityBotDeck1` to `SecurityBotDeck7`. Each kind also has a `...Deck7Plus` section.
The game's own maps use `<Kind>Deck<N>`, where `N` is the level's deck number.
For a custom map, use a complete section name, for example `TurretDeck1`.

**CAUTION:** Do not use `TurretDefault`, `SecurityCameraDefault` or `SecurityBotDefault`.
These are placeholder boards with revealed overload tiles in every cell.
See [32-Hacking.md](32-Hacking.md).

What a successful hack does:

- A hacked camera works for the player. It starts an alarm against the enemies that attack
  the player.
- A hacked turret fights for the player. `ActionHackTurret` does the same from a script.
- A hacked bot fights for the player.

Alarm bots have no spawner. Bot initialization reads one of three puzzle names from the spawning manager, according to bot grade.
For custom maps, configure these manager settings for placed bots and alarm bots.
Open Level Properties with F6. Expand Spawning > `SpawningManager` > SecurityBotHackInfo.

| Property | Meaning |
| --- | --- |
| `MinimumSecurityBotHackInfoName` | The puzzle of the weakest bots. Default `SecurityBotDefault`. |
| `MediumSecurityBotHackInfoName` | The puzzle of the medium bots. Default `SecurityBotDefault`. |
| `MaximumSecurityBotHackInfoName` | The puzzle of the strongest bots. Default `SecurityBotDefault`. |

Set all three names to a `SecurityBotDeck<N>` section, for example `SecurityBotDeck1`.
The game's own maps set all three to the `SecurityBotDeck<N>` section of their level.

The scripted tutorial bot has `HackInfoName=FirstBotHack` on its spawner.
Outside that tutorial, the spawner setting's effect and its precedence over manager settings are unverified.
Do not rely on a general spawner override. See [32-Hacking.md](32-Hacking.md#the-security-bots).

## What the bake writes

The bake registers every security spawner in the spawning manager, one line per spawner in
`System\Editor.log`:

```
Bake: registered SecurityCameraSpawner_0 in SpawningManager.SecurityCameraSpawners
Bake: registered TurretSpawner_0 in SpawningManager.TurretSpawners
Bake: registered SecurityBotSpawner_0 in SpawningManager.SecurityBotSpawners
```

The bake finds the spawners by class, so every spawner in the map gets its line. A spawner
that is missing from the log is not in the map. The spawn zones keep no list of security
spawners; the `SpawnZones` names are matched when the map starts.

## Scripting actions

These actions drive the security system. Every one is described, with its fields and its
row text, in [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md).

| Action | What it does |
| --- | --- |
| `ActionStartSecurityAlarm` | Starts an alarm against a pawn and sends bots of a given class and count. |
| `ActionStopSecurityAlarm` | Stops the running alarm. |
| `ActionIsSecurityAlarmOn` | Returns whether an alarm is on, and whether its target has a given Label. |
| `ActionHackSecuritySystem` | Shuts the security system down for a time. |
| `ActionUnHackSecuritySystem` | Ends the shutdown. |
| `ActionHackTurret` | Makes every turret with the Label fight for the player, or hostile again. |
| `ActionReorientTurret` | Turns the standby direction of every turret with the Label toward an actor. |
| `ActionToggleSecurityCameraSpotlight` | Turns the spotlight of every camera with the Label on or off. |
| `ActionSpawnTurret` | Creates the turret of a `TurretSpawner` whose `ForScriptedSpawn` is True. |
| `ActionSpawnSecurityBot` | Creates the bot of a `SecurityBotSpawner` and can hand it to a pawn. |
| `ActionActivateSecurityBot` | Gives every dormant bot with the Label to a pawn as its ally. |
| `ActionAssignNextSecurityBotSpawnLocation` | Sets the navigation point where the next alarm bots appear. |
| `ActionMakeBotsAttack` | Makes the bots of a pawn attack a target. |
| `ActionSetSpawnerRepopulationState` | Lets a spawner with the Label spawn, or not. |
| `ActionEvaluateHack` | Returns the buy-out cost of hacking a class. |
| `ActionDisableOrEnableMachine` | Disables or enables a machine with the Label, the Bot Shutdown Panel included. |

## Limits

- The BioShock editor does not create the camera's spotlight actor. In the game's own maps
  every camera spawner points to one, placed at the camera's position. A camera on a custom
  map may have no light cone. This is not tested.
- The BioShock editor does not draw the camera's tracking arc or its view cone. Check the
  yaw limits, the pitch and the sight distance in the game.
- The spawner classes hide the Display category, so a spawner on a custom map has no preview
  model.
- Flying path nodes are placed by hand. The path build does not create them.
- Where the alarm creates its bots, and how the game tests that the player cannot see the
  point, is not documented beyond the distance rule above.
- The `System\Ai.ini`, `System\Spawning.ini` and `System\Hacking.ini` files in the SDK
  folder are copies for reading. The game reads its own configuration. A section that you
  add or change in the SDK's copies has no effect in the game.

## Related documents

- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [26-AI-Paths.md](26-AI-Paths.md)
- [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)
- [27-Spawning-AI.md](27-Spawning-AI.md)
- [25-Machines.md](25-Machines.md)
- [32-Hacking.md](32-Hacking.md)
