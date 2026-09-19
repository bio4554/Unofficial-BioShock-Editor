# Spawning AI

This document tells you how to put splicers and Big Daddies in a map: how the spawning system
is built, how to create the spawning manager and its spawn zones, how to place and set a
spawner, what an archetype is and how the game matches one to a spawned AI, what the bake
does with all of it, and how to test. It is the document that
[28-Patrols.md](28-Patrols.md), [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md),
[30-Corpses.md](30-Corpses.md), [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md) and
[31-Security.md](31-Security.md) point to for the spawning manager, the spawn zones and the
spawners.

**CAUTION:** None of the game's gameplay systems has been extensively tested on custom maps.
That includes the systems that the SDK's authors have run on maps of their own. Expect
behaviour that differs from the game's own maps, and test every setup in the game.

## Before you start

- You can place actors and edit their properties in the Properties window. See
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- The map has a floor, a PlayerStart and a built geometry. See
  [07-BSP-Geometry.md](07-BSP-Geometry.md).
- The map has a path network. A spawned AI that cannot use the path network stands still. See
  [26-AI-Paths.md](26-AI-Paths.md).
- You can bake the map and start it in the game with Play Map. See
  [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).

## How the system works

The spawning system has three layers. You author all three.

1. **The spawning manager.** One object on the map's LevelInfo. It holds the list of spawn
   zones, the list of archetype names the map uses, the patrols and the AI loot. When the map
   starts, the game reads the manager and creates the AI from the spawners it knows about.
   A map with no spawning manager spawns no AI.
2. **The spawners.** Actors in the map. A spawner says where an AI appears, which AI classes it
   can be, in which spawn zone it lives, and what Label and patrol the AI gets. An
   `AggressorSpawner` creates splicers. A `ProtectorSpawner` creates Big Daddies.
3. **The archetypes.** Named sections of `System\Spawning.ini`. An archetype says what a
   spawned AI looks like and is made of: its mesh, its skins, its hats and masks, its health.
   The map never edits an archetype. It only names the archetypes it uses, and the game
   picks one that fits the spawned class.

**NOTE:** "Spawn zone" in this document means a logical zone of the spawning manager, a
`SpawnZoneInfo` with a name. It is not a BSP zone of
[08-Zones-and-Portals.md](08-Zones-and-Portals.md). A spawn zone has no volume. A spawner, a vent or a corpse belongs to a spawn zone because
it names the zone.

The Little Sister is not spawned by a spawner. She comes out of a vent. See
[29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md). Cameras,
turrets and security bots have their own spawners and no archetype. See
[31-Security.md](31-Security.md).

## Create the spawning manager

1. Press F6 to open Level Properties.
2. Expand Spawning. Click the `SpawningManager` row, click its `New` row, pick
   `SpawningManager` and click the **New** button. The manager's properties appear under it.
3. Set the properties in the table below. `SpawningZones` and `AIArchetypeNames` are the two
   that every map needs.

| Property | Meaning |
| --- | --- |
| `SpawningZones` | The list of spawn zones. Each entry is a `SpawnZoneInfo`. See "Add a spawn zone". |
| `AIArchetypeNames` | The archetype names the map uses, one per entry. Each name is a section of `System\Spawning.ini` without the brackets. Type the name; the field has no drop-down. See "Archetypes". |
| `Patrols` | The named patrol routes of the map. See [28-Patrols.md](28-Patrols.md). |
| `DefaultAILoot` | What each spawned class carries, and the Little Sister's ADAM. See [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md). |
| `MaxLootableGatherers` | How many Little Sisters can exist at one time. Default 3. See [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md). |
| `bDontSpawnAIs` | Leave it False. True switches off all spawning in the map. |
| `DamageMessageSpecifiers` | Leave it empty. Not covered in this guide. |
| `MinimumSecurityBotHackInfoName`, `MediumSecurityBotHackInfoName`, `MaximumSecurityBotHackInfoName` | The hack puzzles of the alarm bots, in the SecurityBotHackInfo category. Default `SecurityBotDefault`. See [31-Security.md](31-Security.md) and [32-Hacking.md](32-Hacking.md). |

`AIClassesInLevel` is read-only. It stays empty on a custom map. That is harmless.

## Add a spawn zone

1. In Level Properties, expand Spawning > `SpawningManager` > `SpawningZones`.
2. Click the `SpawningZones` row and click the **Add** button. A new entry appears. Click its
   `New` row, pick `SpawnZoneInfo` and click the **New** button.
3. Set `SpawnZoneName`, for example `MyZone`. Every spawner, vent and corpse of the zone names
   this name.
4. Set the counts and timers in the table below, or leave the defaults.

| Property | Category | Meaning |
| --- | --- | --- |
| `SpawnZoneName` | SpawnZoneInfo | The zone's name. Any name without spaces. Not case sensitive. The game's own maps use names such as `icu`, `surgery`, `TransitHub`. |
| `DesiredAggressorCount` | Aggressors | A band, `Min` and `Max`, of how many splicers the zone wants alive. The game's own maps use 0..1, 1..2, 1..3 and 0..2. |
| `AggressorRepopulationTimeDelta` | Aggressors | A range, `Min` and `Max`, in seconds, between two splicer repopulations. Default 30..60. The game's own maps use values between 60 and 400, for example 180..300. |
| `DesiredProtectorCount` | Protectors | A band, `Min` and `Max`, of how many Big Daddies the zone wants alive. The game's own maps use 0..1 and 1..1. |
| `ProtectorRepopulationTimeDelta` | Protectors | A range, `Min` and `Max`, in seconds, between two Big Daddy repopulations. Default 30..60. The game's own maps use values between 120 and 600, for example 300..600. |
| `bAggressorRepopulationEnabled` | SpawnZoneInfo | True: the zone respawns splicers over time. Default True. |
| `bProtectorRepopulationEnabled` | SpawnZoneInfo | True: the zone respawns Big Daddies over time. Default True. |

Repopulation is the respawn of AI over time, per zone, from the spawners of the zone that
have `RepopulationAITypes`. The game's own maps ship most zones with the two flags False and
switch them on from a script with `ActionManipulateSpawnZoneRepopulation`. See
[22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md). A map without such a
script leaves the two flags at their default True.

**NOTE:** The exact repopulation rule, how the desired counts and the timers pick the moment
and the spawner of a respawn, is not documented and not tested with this SDK. For a
test that must give the same result every time, use `InitialAITypes` on the spawner and no
`RepopulationAITypes`.

The zone's lists `AggressorSpawners`, `ProtectorSpawners`, `GathererVents` and `Booty` are
read-only. The bake fills them from the actors that name the zone. See "What the bake does".

## Place a spawner

1. In the Actor Class browser, expand Actor > SpawnerBase > EcologyFighterSpawner and select
   `AggressorSpawner` for a splicer or `ProtectorSpawner` for a Big Daddy. Both are in the
   `ShockAI` package, which is always loaded.
2. Right-click the floor and click **Add AggressorSpawner here**. The AI appears at the
   spawner's position.
3. Rotate the actor so that its arrow points where the AI looks when it appears. In the Top
   viewport, hold Ctrl and right-drag to turn the actor. The game's own maps set the yaw
   only.
4. Press F4 and set the properties. `SpawnZones` and one of the `...AITypes` lists are
   the two that every spawner needs.

### The AI classes

The classes you put in a spawner's `...AITypes` lists come from `ShockAIClasses.pkg`. In the
Actor Class browser, use File > Open Package to load `Content\ShockAIClasses.pkg` from the
content library. Without it, the class drop-down of the lists is empty. Use the classes
whose name starts with `Spawned`:

| Class | What it is | Spawner |
| --- | --- | --- |
| `SpawnedMeleeThug` | Thuggish splicer. Melee, with a club, a wrench, a machete or a flashlight. | `AggressorSpawner` |
| `SpawnedElectrifiedMeleeThug` | Thuggish splicer with an electrified weapon. | `AggressorSpawner` |
| `SpawnedRangedAggressorPistol` | Leadhead splicer with a pistol. | `AggressorSpawner` |
| `SpawnedRangedAggressorSMG` | Leadhead splicer with a machine gun. | `AggressorSpawner` |
| `SpawnedGrenadier` | Nitro splicer. Throws grenades. | `AggressorSpawner` |
| `SpawnedMolotovGrenadier` | Nitro splicer. Throws molotov cocktails. | `AggressorSpawner` |
| `SpawnedCeilingCrawler` | Spider splicer. Needs a ceiling network that the path build does not make; see "Limits". | `AggressorSpawner` |
| `SpawnedAssassin` | Houdini splicer. Teleports. | `AggressorSpawner` |
| `SpawnedMagicAssassin` | Houdini splicer, the second variant. Its archetypes are the `...MagicTeleport` sections. | `AggressorSpawner` |
| `SpawnedBouncer` | Big Daddy, Bouncer. | `ProtectorSpawner` |
| `SpawnedBouncerElite` | Big Daddy, Elite Bouncer. | `ProtectorSpawner` |
| `SpawnedRosie` | Big Daddy, Rosie. | `ProtectorSpawner` |
| `SpawnedRosieElite` | Big Daddy, Elite Rosie. | `ProtectorSpawner` |

The package holds more `Spawned...` classes: `SpawnedGatherer` is the Little Sister and
comes out of a vent, the `SpawnedDeck<N>...` classes are the cameras, turrets and bots of
[31-Security.md](31-Security.md), and `SpawnedAtlas`, `SpawnedUnderwaterRosie`,
`SpawnedLifeDrainCeilingCrawler` and the `SpawnedScripted...` classes belong to scripted scenes
of the game's own maps. `SpawnedRangedAggressorMachineGun` has no archetype in
`System\Spawning.ini`; do not use it.

**CAUTION:** Never put a base class of the `ShockAI` package in a spawner, for example
`ShockAI.Bouncer` or `ShockAI.MeleeThug`. No archetype matches a base class. The AI spawns
without a mesh: invisible, with no weapon. Use the `Spawned...` classes of `ShockAIClasses.pkg`.

### Spawner properties

An `AggressorSpawner` and a `ProtectorSpawner` share most properties. Many come in three
tiers: `Initial...` for the AI that the spawner creates when the map starts, `Repopulation...`
for the AI it creates later when its zone repopulates, and `Global...` for both. When a
`Global...` field is set, it is used for the initial spawn in place of the `Initial...` field.
The same is expected for the repopulation spawn, but that is not tested. Set either the
`Global...` field or the two tier fields, not both.

| Property | Meaning |
| --- | --- |
| `SpawnZones` | The names of the spawn zones this spawner belongs to. Add the `SpawnZoneName` of your zone. The game's own maps set one name on almost every spawner, three at most. Type the name; there is no drop-down. |
| `GlobalAITypes`, `InitialAITypes`, `RepopulationAITypes` | The classes the spawner can create, one per entry. One entry is picked at random for each spawn. Repeat an entry to weight it: the game's own maps use lists such as `SpawnedGrenadier`, `SpawnedRangedAggressorPistol`, `SpawnedRangedAggressorPistol`, `SpawnedRangedAggressorPistol`. A spawner with only `InitialAITypes` spawns one time. |
| `GlobalLabel`, `InitialLabel`, `RepopulationLabel` | The Label that the spawned AI gets. Scripts address the AI by it, for example in `ActionSetAIPatrol` and `ActionAttackTarget`. Leave it `None` when no script needs the AI. |
| `GlobalPatrol`, `InitialPatrol`, `RepopulationPatrol` | `AggressorSpawner` only. The `PatrolName` of a patrol of the spawning manager that the splicer walks. See [28-Patrols.md](28-Patrols.md). |
| `OverriddenAIArchetypeNames` | Forces the archetype. One of the names is picked at random and only that archetype is used. Also list the name in the manager's `AIArchetypeNames`, or the bake does not embed its content. See "The matching rule". |
| `bDontSpawnUntilCurrentSpawnDies` | True: the spawner does not respawn until the last AI it created is dead. The game's own maps set it on most repopulation spawners. |
| `GlobalSpawnStartOutAsMimic`, `InitialSpawnStartOutAsMimic`, `RepopulationSpawnStartOutAsMimic` | `AggressorSpawner` only. True: the splicer starts in a corpse pose and plays dead. An ambush. |
| `SpawnedMimicInitialPose` | `AggressorSpawner` only. The corpse pose of a mimic. Set `AnimationName` to one of the `MimicPoseAnimations` names of the class in `System\Ai.ini`, for example `ME_PlayDeadBack_Pose` for a thuggish splicer, listed under `[ShockAI.MeleeThug]`, or `CR_PlayDeadBack_Pose` for a spider splicer, under `[ShockAI.CeilingCrawler]`. Leave `PoseName` `None` and `PoseRagdollState` at its default. |
| `bCorpseCanBeRemoved` | True: the game can remove the corpse of the AI when it cleans up. Default True. False keeps the corpse. |
| `GlobalLootContainer`, `InitialLootContainer`, `RepopulationLootContainer` | Leave them empty. The game's own maps do not set them; loot comes from the manager's `DefaultAILoot`. See [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md). Not tested. |
| `EnableContinuousRagdoll` | Leave it False. The game's own maps do not set it. |
| `ProtectorGuardRange` | `ProtectorSpawner` only. A distance band, `Min` and `Max`, around the spawner. The game's own maps leave it 0..0: the Big Daddy roams from vent to vent. |

A spawner has no spawn-once, no maximum count and no difficulty property. One AI, one time,
is a spawner with one entry in `InitialAITypes` and nothing in `RepopulationAITypes`. The
counts and the timers are on the spawn zone.

The spawner is hidden in the game. It shows in the BioShock editor as a sprite with an arrow.

## Archetypes

An archetype is a section of `System\Spawning.ini`. Its name is the section name without the
brackets, for example `Bouncer` for `[Bouncer]`. The section says which AI class it dresses
and what the AI looks like. The game's own maps never change an archetype; they list names.

### What a section holds

| Key | Meaning |
| --- | --- |
| `AIType` | The class the archetype dresses, for example `class'ShockAIClasses.SpawnedBouncer'`. The matching rule uses it. |
| `Mesh` | The skeletal mesh. Its package also holds the animations. |
| `Health` | The AI's health, when set. It replaces the class's value from `System\Ai.ini`. The splicers of the first level have `Health=50`; the class's value is 100. |
| `FrozenHealth` | How much freeze damage the AI takes before it is frozen solid. |
| `DamageResistanceSetName` | A section of `System\Weapons.ini`, for example `RangedAggressorResistanceSet`. It says how much of each damage kind the AI takes. |
| `VoiceTypes` | The voice sets of the AI, one per line. One is picked at random. |
| `MaterialSlot` | One skin per line, `(AIMaterial=...,Chance=N)`. One is picked. |
| `AttachmentSlot1` to `AttachmentSlot4` | Four slots of hats, masks, wigs, goggles, the Bouncer's drill: one attachment per line, `(Chance=N,AIAttachmentClass=...)`. One is picked per slot. A line with `AIAttachmentClass=None` is the "nothing" result. |
| `WeaponSlot1` to `WeaponSlot4` | Weapon swaps: `(CurrentAIWeaponClass=...,ReplacementAIWeaponClass=...,Chance=N)`. One is picked per slot. This is how a thuggish splicer gets a wrench, a machete or a flashlight instead of the club. |
| `CollisionHeight`, `CollisionRadius` | The AI's collision cylinder, when set. Splicers use 76 for the height. |
| `FriendlyName` | The name the HUD shows for a named character, for example `Dr. Steinman`. Without it the class's name is shown. |
| `RequiredAnimationGroups` | Animation groups of the mesh package that the level keeps. Only needed for the scripted scenes of the game's own maps. |
| `TeleportOutTransitionShader`, `TeleportInTelegraphShader`, `TeleportInTransitionShader` | The teleport effect of a houdini splicer. Every `SpawnedAssassin` and `SpawnedMagicAssassin` archetype carries the three, plus the `...Fire`, `...Ice` and `...Lightning` versions. |

`Chance` is a weight, not a percent. The game adds up the weights of a slot and draws one
line by weight. The game's own sections give every line `Chance=100`, so every line has the
same chance. A line with `Chance=300` next to lines with `Chance=100` is picked three times
as often.

This is the start of `[Bouncer]`:

```ini
[Bouncer]
AIType=class'ShockAIClasses.SpawnedBouncer'
Mesh=SkeletalMesh'NewProtectorBouncer.ProtectorBouncerMESH'
bDoNotDoBurningAnimations=true
bDoNotDoBurningBehavior=true

AttachmentSlot1=(Chance=100,AIAttachmentClass=class'AIAttachments.Bouncer_Drill_Cone')
AttachmentSlot2=(Chance=100,AIAttachmentClass=class'AIAttachments.Bouncer_DrillCage_Cone')
AttachmentSlot3=(Chance=100,AIAttachmentClass=class'AIAttachments.Bouncer_Backpack')
AttachmentSlot4=(Chance=100,AIAttachmentClass=None)
```

And the start of `[ToastyMelee]`, a thuggish splicer with a random skin, a random hat or mask
and a random weapon:

```ini
[ToastyMelee]
AIType=class'ShockAIClasses.SpawnedMeleeThug'
Mesh=SkeletalMesh'AggressorBabyJane.Agg_Toasty_Mesh'
CollisionHeight=76

VoiceTypes=ToastyMelee

MaterialSlot=(AIMaterial=FacingShader'AggressorToasty.ToastyBlackRimShader',Chance=100)
MaterialSlot=(AIMaterial=FacingShader'AggressorToasty.ToastyRimShader',Chance=100)

AttachmentSlot1=(Chance=100,AIAttachmentClass=class'AIAttachments.FedoraHatMale_Brown')
AttachmentSlot1=(Chance=100,AIAttachmentClass=class'AIAttachments.SpiderMask_Red')
AttachmentSlot1=(Chance=100,AIAttachmentClass=None)

WeaponSlot1=(CurrentAIWeaponClass=class'ShockAIClasses.SpawnedMeleeThugClub',ReplacementAIWeaponClass=class'ShockAIClasses.SpawnedMeleeThugWrench',Chance=100)
```

### The families

The file holds about 300 archetypes. The section name is built from a costume and a role,
often with a level prefix. The costumes are `Toasty`, `LadySmith`, `Doctor`, `Waders`,
`Pigskin`, `BabyJane`, `Rosebud`, `Ducky` and `Breadwinner`. The prefix picks the level's
skins, masks, health and damage resistance: the second level of the game lists the
`Medical...` sections, the fourth the `Arcadia...` sections. A section without a prefix is
the base version. The names are not case sensitive.

| Class | Base sections | Sections with a level prefix |
| --- | --- | --- |
| `SpawnedMeleeThug` | `ToastyMelee`, `LadySmithMelee`, `DoctorMelee`, `WadersMelee`, `PigskinMelee`, `BabyJaneMelee`, `RosebudMelee`, `DuckyMelee`, `BreadwinnerMelee` | `MedicalLadySmithMelee`, `MedicalDoctorMelee`, `ArcadiaDoctorMelee`, `ArcadiaLadySmithMelee`, `MarketWadersMelee`, `RecreationToastyMelee`, `WelcomeToastyMelee` |
| `SpawnedElectrifiedMeleeThug` | `ToastyElectrifiedMelee`, `DoctorElectrifiedMelee`, `WadersElectrifiedMelee`, ... | `SlumsToastyElectrifiedMelee`, `ScienceDoctorElectrifiedMelee`, `EngineeringWadersElectrifiedMelee`, `ResidentialLadySmithElectrifiedMelee` |
| `SpawnedRangedAggressorPistol` | `ToastyPISTOL`, `LadySmithPISTOL`, `DoctorPISTOL`, `WadersPISTOL`, ... | `MedicalLadySmithPISTOL`, `MedicalDoctorPISTOL`, `ArcadiaDoctorPISTOL`, `FisheriesWadersPISTOL`, `MarketBabyJanePISTOL` |
| `SpawnedRangedAggressorSMG` | `ToastySMG`, `DoctorSMG`, `WadersSMG`, `LadySmithSMG`, ... | `SlumsToastySMG`, `ScienceDoctorSMG`, `EngineeringWadersSMG`, `ResidentialBreadwinnerSMG` |
| `SpawnedGrenadier` | `ToastyGrenadier`, `DoctorGrenadier`, `DuckyGrenadier`, `WadersGrenadier`, ... | `MedicalDoctorGrenadier`, `FisheriesDuckyGrenadier`, `FisheriesWadersGrenadier` |
| `SpawnedMolotovGrenadier` | `ToastyMolotov`, `DoctorMolotov`, `DuckyMolotov`, `WadersMolotov`, ... | `RecreationToastyMolotov`, `SlumsToastyMolotov`, `ResidentialDuckyMolotov`, `GauntletDoctorMolotov` |
| `SpawnedCeilingCrawler` | `BabyJaneCeilingCrawler`, `ToastyCeilingCrawler`, `WadersCeilingCrawler`, `RosebudCeilingCrawler`, `BreadwinnerCeilingCrawler` | `RecreationBabyJaneCeilingCrawler`, `FisheriesWadersCeilingCrawler`, `EngineeringRosebudCeilingCrawler`, `WelcomeRosebudCeilingCrawler` |
| `SpawnedAssassin` | `ToastyTeleport`, `LadySmithTeleport`, `DoctorTeleport`, `DuckyTeleport`, `WadersTeleport`, ... | `ArcadiaDoctorTeleport`, `ArcadiaLadySmithTeleport`, `MarketWadersTeleport`, `EngineeringDuckyTeleport`, `RecreationBabyJaneTeleport` |
| `SpawnedMagicAssassin` | `ToastyMagicTeleport`, `BabyJaneMagicTeleport`, `LadySmithMagicTeleport`, `DoctorMagicTeleport`, ... | `ScienceToastyMagicTeleport`, `GauntletDoctorMagicTeleport`, `ScienceRosebudMagicTeleport` |
| `SpawnedBouncer` | `Bouncer` | `MedicalBouncer`, `TheaterBouncer` |
| `SpawnedBouncerElite` | `BouncerElite` | `BouncerEliteEngineering`, `GauntletBouncer` |
| `SpawnedRosie` | `Rosie` | none |
| `SpawnedRosieElite` | `RosieElite` | none |
| `SpawnedGatherer` | `Gatherer` | `GathererMedical`, `GathererMarket`. See [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md). |

The file also holds named characters of the game's story (`Cohen`, `Atlas`, `DoctorSteinman`,
`Brenda`, `Charley`) and scripted one-offs. They are not covered in this guide. Open
`System\Spawning.ini` and read the `AIType=` line of a section to see which class it dresses.

The game's own maps list a small set, about ten names each. The first level's list holds
`ToastyMelee`, `LadySmithMelee`, `RosebudMelee`, `ToastyPISTOL`, `LadySmithPISTOL`,
`RosebudCeilingCrawler`, `Bouncer` and `Gatherer`, among others. The second level's list holds
`MedicalBabyJaneMelee`, `MedicalLadySmithMelee`, `MedicalDoctorMelee`, `MedicalLadySmithPISTOL`,
`MedicalDoctorPISTOL`, `MedicalDoctorGrenadier`, `DoctorSMG`, `Bouncer`, `GathererMedical` and
`Gatherer`, among others.

### The matching rule

When an AI spawns, the game picks its archetype like this:

1. The archetype's `AIType` must be the spawned class or a parent of it. A `SpawnedMeleeThug`
   matches every section whose `AIType` is `SpawnedMeleeThug`. It never matches a
   `SpawnedRangedAggressorPistol` section.
2. The archetype's name must be in the manager's `AIArchetypeNames`. When the spawner has
   `OverriddenAIArchetypeNames`, the name must be the override instead, and the list is not
   consulted.
3. One of the matching archetypes is picked at random. Several names for the same class give
   a random mix: with `ToastyMelee`, `LadySmithMelee` and `RosebudMelee` in the list, every
   `SpawnedMeleeThug` is one of the three.

No match means no archetype. The AI spawns without a mesh, invisible and without a weapon.
Check the archetype configuration if this occurs. Retail runtime logs are not available through a reliable method.
See [Testing a script](20-Scripting-Basics.md#testing-a-script).

The two lists must agree: the archetype names in the manager give the AI its looks, the
classes in the spawners give it its behaviour. Every class you spawn needs at least one
listed archetype whose `AIType` is that class.

### A custom archetype

An archetype of your own is a new section of `System\Spawning.ini`:

1. Copy a section whose `AIType` is the class you spawn, for example `[Bouncer]`.
2. Give the copy a unique name, for example `[MyBouncer]`. Keep `AIType`, `Mesh`,
   `CollisionHeight` and `VoiceTypes` from the template. Change the skins, the attachments
   and the health.
3. Add the name to the manager's `AIArchetypeNames`. The bake embeds every package that the
   section references.

The `ArchetypeNames=` list under `[ShockAI.SpawningManager]` at the top of the file is a list
for a drop-down that this editor does not have. You can add your name there; it changes
nothing.

**NOTE:** The game reads its own copy of the configuration, not the SDK folder's
`System\Spawning.ini`. A section that exists only in the SDK's file is not expected to
work in the game: the AI spawns without a mesh. Use the game's own archetype names. A custom
archetype is for an SDK that also delivers the configuration to the game, which this
guide does not cover.

## What the bake does

The bake writes `System\Editor.log`. See "Read the log after a bake" in
[18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md). For the spawning system it
writes these lines.

When the map has no spawning manager, the bake creates an empty one:

```
Bake: synthesized default ShockAI.SpawningManager
```

The empty manager has no spawn zones and no archetype names. When you see this line on a map
that must spawn AI, create the manager yourself.

For every name in `AIArchetypeNames`, the bake reads the section, loads and embeds every
package the section references, and adds the mesh package to the level's required packages:

```
Bake: archetype 'Bouncer' pulls package 'NewProtectorBouncer'
Bake: rooted archetype ref SkeletalMesh NewProtectorBouncer.ProtectorBouncerMESH
Bake: archetype 'Bouncer' adds RequiredPackages entry 'NewProtectorBouncer'
```

A name that has no section gives a warning. Compare the name with the section name letter by
letter:

```
Bake: WARNING: AIArchetypeNames entry 'Bouncr' has no [Bouncr] section in Spawning.ini. The archetype will not materialize and its AI will spawn without a mesh.
```

Two more lines mark a package that the bake cannot find, `Bake: archetype '...' references
package '...' which is not in paths`, and an object it cannot find, `Bake: archetype ref '...'
not found to root`. Both mean that a package of the content library is missing or renamed.

The bake registers every spawner in the manager, and in every spawn zone that the spawner
names. One line per registration:

```
Bake: registered ProtectorSpawner_0 in SpawningManager.ProtectorSpawners
Bake: registered ProtectorSpawner_0 in spawn zone 'MyZone' ProtectorSpawners
Bake: registered AggressorSpawner_0 in SpawningManager.AggressorSpawners
Bake: registered AggressorSpawner_0 in spawn zone 'MyZone' AggressorSpawners
```

The game only reads these lists. A spawner without a `Bake: registered` line spawns nothing.
The same lines exist for vents, corpses, cameras, turrets, bots and health stations.

Last, the bake reports the path network:

```
Bake: path network: 84 reachspecs, 84 usable by AI
```

The second number must not be 0. See [26-AI-Paths.md](26-AI-Paths.md).

What to check after a bake:

- No `Bake: WARNING: AIArchetypeNames` line.
- One `Bake: archetype '...' pulls package` line at least for every name you listed.
- One `Bake: registered ... in SpawningManager` line and one `... in spawn zone` line for
  every spawner.
- A `Bake: path network` line whose second number is not 0.

## Test

1. Save the map and run Build > Play Level (Ctrl+P).
2. The AI must appear at the spawner, facing the spawner's arrow.

| Symptom | Check |
| --- | --- |
| Nothing spawns. | The map has a spawning manager with a spawn zone, the spawner names that zone in `SpawnZones`, `InitialAITypes` has an entry, and `System\Editor.log` has the `Bake: registered` lines for the spawner. |
| The AI appears without a mesh, or is invisible. | The manager's `AIArchetypeNames` holds a name whose `AIType` is the spawned class. The spawner uses a `Spawned...` class, not a `ShockAI` base class. |
| The AI stands still and only attacks when the player is close. | The path network. See [26-AI-Paths.md](26-AI-Paths.md) and the `Bake: path network` line. |
| The corpse holds nothing. | `DefaultAILoot` on the manager. See [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md). |
| The AI does not respawn. | Repopulation is not tested. Use `InitialAITypes` for a test. |

## Limits

- `AIArchetypeNames`, `SpawnZones`, `OverriddenAIArchetypeNames` and the patrol names are
  typed by hand. There is no drop-down. The only check is the bake's warning on
  `AIArchetypeNames`.
- A name in `OverriddenAIArchetypeNames` must also be in `AIArchetypeNames`, or the bake does
  not embed its content. The archetype then joins the random mix of that class on the other
  spawners.
- Repopulation is not tested with this SDK.
- The path build makes no ceiling network. A `SpawnedCeilingCrawler` has nothing to crawl on.
- A custom archetype is not delivered to the game. Use the game's own archetype names.
- The `ShockAI` base classes cannot be dressed by any archetype.

## Related documents

- [08-Zones-and-Portals.md](08-Zones-and-Portals.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [26-AI-Paths.md](26-AI-Paths.md)
- [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)
- [28-Patrols.md](28-Patrols.md)
- [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md)
- [30-Corpses.md](30-Corpses.md)
- [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md)
- [31-Security.md](31-Security.md)
- [32-Hacking.md](32-Hacking.md)
