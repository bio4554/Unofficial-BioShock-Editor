# Little Sisters and Gatherer Vents

This document tells you how to put a Little Sister in a map: where she comes from, how to
place and set up a vent, what the spawning manager needs, what the path build and the bake
do with the vent, and how to set the ADAM that the player gets from her. Read it after
[27-Spawning-AI.md](27-Spawning-AI.md), which covers the spawning manager, the spawn zones
and the spawner for the Big Daddy.

**CAUTION:** None of the game's gameplay systems has been extensively tested on custom maps.
That includes the systems that the SDK's authors have run on maps of their own. Expect
behaviour that differs from the game's own maps, and test every setup in the game.

## Before you start

- You can place actors and edit their properties in the Properties window. See
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- The map has a path network. See [26-AI-Paths.md](26-AI-Paths.md).
- The map has a spawning manager, a spawn zone and a `ProtectorSpawner` that spawns a Big
  Daddy in that zone. See [27-Spawning-AI.md](27-Spawning-AI.md).
- You can bake the map and start it in the game with Play Map. See
  [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).

## Where the Little Sister comes from

The Little Sister is never placed in a map, and no spawner creates her. She comes out of a
vent. The vent is the actor you place: a `PlacedGathererVent`. Three things bring her out of
it:

1. The ecology. An idle Big Daddy picks a vent in its spawn zones, walks to it and knocks. The
   vent creates the Little Sister and the two become a pair. She looks for corpses to gather
   ADAM from; when there is nothing to gather, the Big Daddy puts her back and the cycle
   repeats. See [30-Corpses.md](30-Corpses.md) for the corpses.
2. A script. `ActionSpawnLinkedGathererAndProtector` creates a Big Daddy and the Little
   Sister of a vent as a linked pair. It names the vent in `AssociatedGathererVent` and the
   spawn points in `ProtectorSpawnLocationLabel` and `GathererSpawnLocationLabel`.
   `ActionSpawnPlayerEscortedGatherer` creates a Little Sister that the player escorts, out
   of the vent named in `GathererVentLabel`. See
   [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md).
3. The player. When a vent's `bPlayerCanSpawn` is True, the player calls a Little Sister out by
   using the vent or by hitting it with the wrench. The game's own maps use this for one
   mission only. It is not part of the normal ecology and this document does not cover it.

The Little Sister that a vent creates is the `Gatherer` archetype of the spawning system.
Her class is `ShockAIClasses.SpawnedGatherer`. Like every archetype, the values come from the
game's configuration; the map only names the archetype.

**NOTE:** "Spawn zone" in this document means a logical zone of the spawning manager, a
`SpawnZoneInfo` with a name. It is not a BSP zone of
[08-Zones-and-Portals.md](08-Zones-and-Portals.md).

## Place the vent

1. In the Actor Class browser, use File > Open Package to load `Content\ShockAIClasses.pkg`
   from the content library. Load `Content\GathererVentMesh.pkg` too; it holds the vent's mesh.
2. Expand Actor > StaticMeshActor > GathererVent and select `PlacedGathererVent`.
3. Right-click the floor at the foot of a wall and click **Add PlacedGathererVent here**.
4. Move and rotate the vent so that it sits flush against the wall, with its front open to the
   room. The game's own maps place every vent this way.
5. Press F4 and set the properties in the table below.

| Property | Category | Meaning |
| --- | --- | --- |
| `SpawnZones` | GathererVent | The names of the spawn zones this vent serves. Add the `SpawnZoneName` of your zone. The bake registers the vent in each zone named here. |
| `Label` | Scripting | The vent's name for scripts. Every action that names a vent needs it. |
| `bDontSpawnGatherers` | GathererVent | Leave it False. True means the vent never creates a Little Sister. |
| `bProtectorsShouldntUse` | GathererVent | Leave it False. True means no Big Daddy picks this vent. |
| `bPlayerCanSpawn` | GathererVent | Leave it False. See "Where the Little Sister comes from". |

Do not set `GathererClass`. It comes from the game's configuration and is already
`ShockAIClasses.SpawnedGatherer`.

The class's default `StaticMesh` is `GathererVentMesh.GathererVentMesh`. Every vent in the game
uses this one mesh. Do not change it: the path build computes the vent's positions from it.

### Floor space in front of the vent

The vent's front needs open floor. The Big Daddy stands about 110 units in front of the vent
when knocking. The Little Sister walks out to about 220 units in front of it. Keep that
space free of meshes, steps and other actors, and keep it inside the path network.

## Set up the spawning manager

Open Level Properties with F6 and expand Spawning > `SpawningManager`.

1. `AIArchetypeNames`: add `Gatherer`. This name makes the bake embed the Little Sister's
   content: her mesh and animations, and the ADAM extractor she carries. Without it she does
   not spawn correctly.
2. `SpawningZones`: the map needs a `SpawnZoneInfo` whose `SpawnZoneName` is one of the names
   in the vent's `SpawnZones`.
3. A `ProtectorSpawner` in that same zone, with a Big Daddy in its `InitialAITypes`. The
   Little Sister only roams with an escort. Without a Big Daddy in the zone, the ecology
   never brings her out.

See [27-Spawning-AI.md](27-Spawning-AI.md) for the spawner and the zone.

**NOTE:** `MaxLootableGatherers` on the spawning manager limits how many Little Sisters can
exist at one time. The default is 3.

## What the path build does with the vent

The game does not compute the vent's positions at run time. The path build computes them
for every placed vent, from the vent's position and rotation:

- The points around the vent that the AI uses: where the Little Sister appears, where a
  Big Daddy stands to take her out and to put her back, and others.
- A `PathNode` in front of the vent. The path build creates this node, marks it as generated
  and links it to the vent. It joins the path network like any other node. The Big Daddy
  walks to this node. A vent without it is skipped by every Big Daddy, without a message
  in the game.

The path build writes these lines to `System\Editor.log`:

```
Paths: vent PlacedGathererVent_0: sockets cooked, FrontPathNode PathNode_12 at (640 -128 101)
Paths: cooked 1 gatherer vent(s)
```

Rules that follow from this:

- When you move or rotate a vent, run Build > Rebuild AI Paths again. The computed positions
  belong to the old placement until you do.
- Run the path build with every vent in its final position, as the last build step before the
  bake. A path node that you add, move or delete after the path build makes the bake write a
  `Bake: WARNING` line, and the game asks for a path rebuild when the map starts.
- A second path build reuses the generated node and moves it to the new position. Do not
  delete or move the generated node yourself.

## What the bake does with the vent

The bake registers the vent in the spawning manager's list of vents and in the vent list of
every spawn zone named in `SpawnZones`. It writes one line per registration:

```
Bake: registered PlacedGathererVent_0 in spawn zone 'MyZone' GathererVents
```

A vent that is registered in no zone is not found by the ecology. When the line is missing,
compare the vent's `SpawnZones` with the zone's `SpawnZoneName` letter by letter.

## The ADAM that the player gets

The ADAM that the player receives for harvesting or rescuing the Little Sister is not a
property of the vent, of the archetype or of the spawner. It comes from the loot container
that the spawning manager gives her when she spawns.

**CAUTION:** Without the entry below, a rescued or harvested Little Sister pays 0 ADAM. The
rescue choice still appears in the game; it just gives nothing.

1. Open Level Properties with F6 and expand Spawning > `SpawningManager` > `DefaultAILoot`.
2. Click the `DefaultAILoot` row and click **Add**. Click the new entry's `New` row, pick
   `AIContainerInfo` and click **New**.
3. Set `AIType` to `Class'ShockAIClasses.SpawnedGatherer'`. The entry is matched to the
   spawned AI by class name. `ShockAIClasses.pkg` must be loaded for the class to appear in
   the drop-down.
4. Expand `LootContainer`, click its `New` row, pick `Container` and click **New**.
5. Expand `LootSlots`, then `[0]`, then `LootSpec`. Click its `New` row and pick one of these:
   - `LootTableSpecification`. Set `TableName` to a gatherer table of the game's loot tables,
     for example `Deck4GathererA`. The game's own maps use this form. The `[Deck<N>Gatherer<A|B>]`
     tables of `System\LootTables.ini` hold one entry of 160 ADAM each in the `A` tables.
   - `LootItemSpecification`. Set `ItemClass` to `Class'ShockGame.ADAM'` and `MinStackSize` and
     `MaxStackSize` to the amount, for example 160 and 160. This form needs no table.
6. Leave the other two slots and the container's flags at their defaults.

Harvest pays the full stack. Rescue pays half of it: the game's `SeaSlugAdamPercentage` is
0.5. With a stack of 160, rescue pays 80. See "Loot specifications" in
[24-Pickups-and-Loot.md](24-Pickups-and-Loot.md) for the two specification classes and the
table format.

## Gathering

A Little Sister gathers ADAM from corpses. A corpse is a `Booty` actor, registered in a spawn
zone through the ZoneInfo of the BSP zone that contains it. Without corpses she comes out,
finds nothing, and the Big Daddy puts her back. See [30-Corpses.md](30-Corpses.md).

## Content packages involved

| Package | Contents |
| --- | --- |
| `ShockAIClasses.pkg` | `PlacedGathererVent`, `SpawnedGatherer`, the Booty classes and the other AI classes. |
| `GathererVentMesh.pkg` | The vent's mesh and its shader. |
| `GathererGirl.pkg` | The Little Sister: her mesh, animations and materials. The bake embeds it through the `Gatherer` archetype name. |
| `WP_AI_GathererGun.pkg` | The ADAM extractor she carries. |
| `vo_gatherer_audio.pkg`, `Gatherer_audio.pkg` | Her voice and sounds. The bake pulls them in through the effects database; nothing to do. |

You load the first two in the Actor Class browser. The bake handles the rest.

## Related documents

- [08-Zones-and-Portals.md](08-Zones-and-Portals.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [26-AI-Paths.md](26-AI-Paths.md)
- [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)
- [27-Spawning-AI.md](27-Spawning-AI.md)
- [30-Corpses.md](30-Corpses.md)
- [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md)
