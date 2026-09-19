# Corpses

This document tells you how to place a corpse that a Little Sister can gather ADAM from: the
corpse classes, the placement height, the pose, the spawn zone registration through the
ZoneInfo, what the bake writes to the log, and the optional loot for the player. Read it
after [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md).

**CAUTION:** None of the game's gameplay systems has been extensively tested on custom maps.
That includes the systems that the SDK's authors have run on maps of their own. Expect
behaviour that differs from the game's own maps, and test every setup in the game.

## Before you start

- You can place actors and edit their properties in the Properties window. See
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- You know what a BSP zone and a ZoneInfo are. See
  [08-Zones-and-Portals.md](08-Zones-and-Portals.md).
- The map has a path network. See [26-AI-Paths.md](26-AI-Paths.md).
- The map has a spawning manager, a spawn zone, a `ProtectorSpawner` and a vent. See
  [27-Spawning-AI.md](27-Spawning-AI.md) and
  [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md).

## How a corpse works

A gatherable corpse is a `Booty` actor: a ragdoll with a skeletal mesh, and a `Container`
that the player can search. When a Little Sister is out of her vent, she looks through the
corpses that are registered in her Big Daddy's spawn zones, walks to one, extracts ADAM and
returns to the vent.

A corpse has no `SpawnZones` property of its own. It is registered by the BSP zone that
contains it: the ZoneInfo of that BSP zone carries a `SpawnZones` list, and the corpse joins
every spawn zone that the list names. The bake writes the initial registration. The game
keeps it up to date when a ragdoll is dragged into another zone.

**NOTE:** Two kinds of zone meet here. A BSP zone is a region of the built geometry, closed by
zone portals, with one ZoneInfo actor. See [08-Zones-and-Portals.md](08-Zones-and-Portals.md).
A spawn zone is a logical zone of the spawning manager, a `SpawnZoneInfo` with a name. A
spawn zone can cover many BSP zones, and a BSP zone can belong to several spawn zones. The
`SpawnZones` list on a ZoneInfo is the link between the two.

## Place a corpse

1. In the Actor Class browser, use File > Open Package to load `Content\ShockAIClasses.pkg`
   from the content library.
2. Expand Actor > ReactiveActor > ReactiveAnimatedMesh > Booty and select a class:

| Class | Corpse |
| --- | --- |
| `AggToastyBooty`, `AggBabyJaneBooty`, `AggDoctorBooty` | Splicer corpses. The mesh is preset. |
| `CorpseMaleBooty`, `CorpseFemaleBooty` | Civilian corpses. The mesh is preset. |
| `ProRosieBooty` | A Rosie corpse. The mesh is preset. |
| `PlacedBooty` | A generic corpse. Set `Mesh` yourself. |

3. Right-click the floor and click **Add <class> here**.
4. Check the height. See below.

### Placement height

A corpse is a ragdoll. It must start resting on the floor. The BioShock editor draws the
corpse in its pose, lying as it lies in the game, so you can see the fit. Look at the corpse
in a side view and move it up or down until the body rests on the floor surface: not sunk
into it, not above it. When you change the pose, check the height again.

| Height | Result |
| --- | --- |
| The body rests on the floor | Correct. The corpse can be gathered. |
| Half buried in the floor | The corpse never renders in the game. |
| Floating above the floor | The Little Sister cannot gather it. |

As a check by numbers: the collision height of the corpse classes is 20 units, so the centre
of the actor belongs about 20 units above the floor surface. On the same floor as your
PathNodes, that is about 80 units below the centre of a PathNode.

When the click point does not fit the corpse, the BioShock editor lifts it by the collision
height and writes a line to `System\Editor.log`:

```
Add actor: AggToastyBooty did not fit at the click point; placed 20 units higher, resting on the surface
```

When the lifted position does not fit either, the BioShock editor places the corpse at the click
point without a fit test, and the log line ends with `placed without collision adjustment`.
That corpse is inside the geometry. Move it up until it rests on the floor.

## Pose the corpse

The corpse classes come with a pose already set in `StartingPose`: the pose that most corpses
of the game's own maps use. A placed corpse needs no pose work. To give it another pose from
the `DeathPoses` animation, as the game's own maps do for variety:

1. Select the corpse and press F4.
2. Expand the ReactiveAnimatedMesh category and `StartingPose`.
3. Leave `AnimationName` at `DeathPoses`.
4. Click `PoseName` and pick a pose from the drop-down. The list holds the pose markers of
   the skeleton's `DeathPoses` animation. The splicer skeleton has 43, for example
   `SitUp_Straight`, `RightSide_Fetal`, `OnStomach_ArmsOverHead`, `hanging` and
   `SittingOnBench`.
5. Leave `PoseRagdollState` at `POSE_RAGDOLL_INACTIVE`. The game's own maps use this value on
   every placed corpse.

The BioShock editor shows the pose at once, in lit, wireframe and orthographic views. A
`PoseName` that the animation does not hold writes a `BioPose:` warning to `System\Editor.log`,
and the corpse shows the first frame of the animation instead.

## Register the corpse in a spawn zone

The Little Sister only sees corpses that are registered in her Big Daddy's spawn zones. The
registration comes from the ZoneInfo of the BSP zone that contains the corpse.

1. Find the ZoneInfo actor of the BSP zone that contains the corpse. A geometry build must
   have run since you placed the ZoneInfo, so that it is assigned to its zone.
2. Select that ZoneInfo and press F4.
3. Expand the Spawning category. Click the `SpawnZones` row and click **Add**.
4. Type the `SpawnZoneName` that your `ProtectorSpawner` uses, for example `MyZone`. Add one
   entry per spawn zone that the corpse belongs to.

**CAUTION:** Set `SpawnZones` on the ZoneInfo whose BSP zone contains the corpse. The
LevelInfo is a ZoneInfo too, but it covers only the BSP zones that have no ZoneInfo actor. A
list on the LevelInfo does nothing for a corpse in a zone that has its own ZoneInfo. The
mistake is silent in the game: the Little Sister stays with her Big Daddy. Read the bake log.

## What the bake writes

The bake registers every corpse in the `Booty` list of each spawn zone that the corpse's
ZoneInfo names. It writes one line per registration:

```
Bake: registered AggToastyBooty_0 in spawn zone 'MyZone' Booty (via zone ZoneInfo_0)
```

`via zone` names the ZoneInfo that supplied the spawn zone. When that is not the ZoneInfo you
edited, the corpse is in another BSP zone than you expected. Move the corpse or edit the
right ZoneInfo, and bake again.

A corpse in a BSP zone whose ZoneInfo names no spawn zone gets a warning instead:

```
Bake: WARNING: AggToastyBooty_0 is in the zone of LevelInfo0, whose SpawnZones list is empty. Set SpawnZones on that ZoneInfo (not on LevelInfo), or Little Sisters will never gather it.
```

The path build does nothing for a corpse. Keep the corpse close to the path network, so that
the Little Sister can walk to it.

## Optional: loot for the player

The `Container` property of the corpse, in the Booty category, holds what the player finds
when searching the corpse. It is not needed for gathering. Click the `Container` row, pick
`Container` in its `New` row and click **New**, then fill its `LootSlots` as described in
"Loot specifications" in [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md). An empty
`Container` gives the player nothing.

## Limits

- A corpse can be gathered one time. The ADAM that the player then gets from the Little
  Sister comes from the spawning manager's `DefaultAILoot`, not from the corpse. See
  [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md).
- Attached models of a corpse, the `AnimatedMeshAttachments`, are not posed in the BioShock
  editor.
- A corpse with `PoseRagdollState` = `POSE_RAGDOLL_ACTIVE` shows its pose marker in the
  BioShock editor. In the game the ragdoll settles under physics, so the game's result differs
  from the editor's view.

## Related documents

- [08-Zones-and-Portals.md](08-Zones-and-Portals.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [26-AI-Paths.md](26-AI-Paths.md)
- [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)
- [27-Spawning-AI.md](27-Spawning-AI.md)
- [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md)
- [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md)
