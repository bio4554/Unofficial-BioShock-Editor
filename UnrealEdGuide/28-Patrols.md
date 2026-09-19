# Patrols

This document tells you how to make a splicer walk a route: where to place the patrol
points, how to define the patrol on the spawning manager, and how to give the patrol to a
spawner. Read it after [27-Spawning-AI.md](27-Spawning-AI.md), which covers the spawning
manager and the spawners that this document uses.

**CAUTION:** None of the game's gameplay systems has been extensively tested on custom maps.
That includes the systems that the SDK's authors have run on maps of their own. Expect
behaviour that differs from the game's own maps, and test every setup in the game.

## Before you start

- You can place actors and edit their properties in the Properties window. See
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- The map has a path network and you know how to build and check it. See
  [26-AI-Paths.md](26-AI-Paths.md).
- The map has a spawning manager, a spawn zone and an `AggressorSpawner` that spawns a
  splicer. See [27-Spawning-AI.md](27-Spawning-AI.md).
- You can bake the map and start it in the game with Play Map. See
  [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).

## How a patrol works

A patrol is a named route. The route belongs to the map's spawning manager. It is a
`PatrolList` object with a `PatrolName` and an ordered list, `PatrolEntries`. Each entry points
at one `PatrolPoint` actor in the map. A spawner gives a patrol to the splicers it spawns by
name. The splicer walks the entries in order over the path network, from the first to the
last, then walks back. Back and forth is the default behaviour.

The splicer moves over the normal path network. The patrol points must be linked by the path
build like any other node. A patrol point that has no links is a stop the splicer cannot
reach.

## Place patrol points

1. In the Actor Class browser, select NavigationPoint > PathNode > PatrolPoint. The class is
   in the `ShockAI` package, which is always loaded.
2. Right-click the floor at a stop of the route and click **Add PatrolPoint here**.
3. Rotate the actor so that its arrow points where the splicer looks while it waits at the
   stop. In the Top viewport, hold Ctrl and right-drag to turn the actor. Only the yaw
   matters.
4. Repeat steps 2 and 3 for each stop of the route.
5. Run Build > Rebuild AI Paths.
6. Show the paths in a viewport and confirm that every patrol point has links to its
   neighbours. See "Check the paths" in [26-AI-Paths.md](26-AI-Paths.md).

**NOTE:** `PatrolPoint` extends `PathNode`. A patrol point is a full node of the path network.
You can replace some of the PathNodes on the route with PatrolPoints; they do both jobs. Keep
the node coverage generous anyway. A splicer that chases the player, and a splicer that
spawns later, still move over the whole network.

## Define the patrol on the spawning manager

1. Press F6 to open Level Properties.
2. Expand Spawning > `SpawningManager` > `Patrols`.
3. Click the `Patrols` row and click the **Add** button. A new entry appears. Click its `New`
   row, pick `PatrolList` and click the **New** button.
4. Set `PatrolName`, for example `TestRoute`.
5. Click the `PatrolEntries` row and click **Add** once per stop, in walk order. Each entry is
   a `PatrolEntry`.
6. In each entry, set `PatrolPoint` to the patrol point of that stop. Click the row and click
   the **Find** button that appears at the right end of the row, then click the patrol point
   in a viewport. The row fills with the actor's name.
7. Set the other fields of the entry as needed. The table below lists them.

| Property | Meaning |
| --- | --- |
| `PatrolPoint` | The patrol point of this stop. Use the **Find** button. |
| `IdleTime` | A range, `Min` and `Max`, in seconds. How long the splicer waits at the stop. 0 means the splicer keeps walking. |
| `IdleChance` | The chance, in percent, that the splicer waits at the stop at all. |
| `AnimationToLoop` | The name of an animation the splicer loops while it waits. Leave it `None` to start. |
| `AnimationToPlayOnce` | The name of an animation the splicer plays one time when it arrives. Leave it `None` to start. |
| `AnimationCategoryToLoop` | The name of an animation category to loop instead of one animation. Leave it `None` to start. |
| `AnimationCategoryToPlayOnce` | The name of an animation category to play one time. Leave it `None` to start. |
| `bDoNotRotateToFacePatrolPointRotation` | True: the splicer does not turn to the patrol point's direction at this stop. |
| `IdleForever` | True: the splicer stays at this stop until something else moves it, for example combat or a script. |
| `bShouldRun` | True: the splicer runs on the leg that leads to this stop. |
| `bShouldBeAggressive` | True: the splicer moves aggressively on the leg that leads to this stop. |

The `Patrols` list can hold several patrols. Give each one its own `PatrolName`.

## Give the patrol to a spawner

1. Select the `AggressorSpawner` and press F4.
2. Set `InitialPatrol` to the `PatrolName`, for example `TestRoute`. Type the name; the field is
   a name, not a drop-down.
3. Save the map and run Build > Play Level (Ctrl+P).

`InitialPatrol` is given to the splicers that the spawner creates at level start. The splicer
walks the route from end to end and back.

The spawner has two more patrol fields: `RepopulationPatrol` for the splicers that the
spawner creates later, when a zone repopulates, and `GlobalPatrol` as the catch-all. They use
the same name matching as `InitialPatrol`. They are not tested with this SDK.

A script can change the patrol of a splicer while the game runs, with the action
`ActionSetAIPatrol`: it takes the splicer's Label in `AggressorLabel` and the `PatrolName`. See
[22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md). This action is not tested
with this SDK.

## What to check in the log

The bake writes `System\Editor.log`. See "Read the log after a bake" in
[18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md). For a patrol, the important
line reports the path network:

```
Bake: path network: 84 reachspecs, 84 usable by AI
```

The second number must not be 0. When the splicer spawns but never walks, read this line first.
A path network that was built by an older editor has no links that the AI can use, and the
bake writes a `Bake: WARNING` line instead. Run Build > Rebuild AI Paths and bake again.

The bake does nothing else for a patrol. The spawning manager, with the patrol lists inside
it, is written to the baked map, and the patrol points are part of the path network.

## Related documents

- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [26-AI-Paths.md](26-AI-Paths.md)
- [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)
- [27-Spawning-AI.md](27-Spawning-AI.md)
