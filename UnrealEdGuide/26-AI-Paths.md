# AI Paths

This document explains the path network that the game's AI uses to move, how to place path
nodes, and how to build and check the network. Read it before you bake a map that contains
enemies. For the enemies themselves, see [27-Spawning-AI.md](27-Spawning-AI.md).

## Before you start

- The map has floors and a PlayerStart. See [07-BSP-Geometry.md](07-BSP-Geometry.md) and
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- The geometry build has run. Paths are traced against the built geometry.

## What the path network is

The game's AI does not path-find over raw geometry. It walks a network of navigation
points. A navigation point is an actor. PathNode is the basic one. The path build traces
between every pair of navigation points that are near each other and stores a link for
each pair a creature can walk. AI moves from link to link.

If you know Radiant, the network is like a set of info_waypoint entities that the BioShock editor
links for you. If you know a newer Unreal Engine, there is no nav mesh. You place the nodes
by hand.

## Place path nodes

1. In the Actor Class browser, select NavigationPoint > PathNode.
2. Right-click the floor where AI walks and click **Add PathNode here**.
3. Repeat along every route the AI must take: corridors, doorways, around furniture, and
   in every room.

Rules for placement:

| Rule | Reason |
| --- | --- |
| Place nodes on the floor, not in the air. | The build drops each node to the floor and traces walks between nodes. |
| Keep nodes within 1200 units of a neighbour. | The build never links two nodes that are farther apart. |
| Place a node on each side of every doorway. | The build must see a clear walk through the opening. |
| Place nodes where a creature can stand. | A node inside a wall or a mesh gets no links. |
| Give wide creatures room. | A link is usable by a creature only if its size class fits along the whole link. |

The build tests each link against three size classes. A link stores which classes fit.

| Size class | Radius | Half-height |
| --- | --- | --- |
| Small | 54 units | 64 units |
| Tall | 50 units | 86 units |
| Wide | 70 units | 86 units |

A corridor that is narrower than 140 units has no links for wide creatures. A door opening
that is lower than 172 units has no links for tall or wide creatures.
The full cylinder height is twice the half-height: 128 units for Small and 172 units for Tall and Wide.

### Navigation point properties

| Property | Meaning |
| --- | --- |
| bBlocked | The node is not usable. Set it True to close a route without deleting nodes. |
| bOneWayPath | Links leave this node only in the direction it faces. |
| ExtraCost | Extra cost for routes through this node. Higher values make AI prefer other routes. |
| ForcedPaths | Names of up to 4 nodes that must always link to this node. |
| ProscribedPaths | Names of up to 4 nodes that must never link to this node. |

## Place flying path nodes

Security bots fly. They move over a second set of nodes, the FlyingPathNodes, that you place
in the air. The path build links them to each other and to the ground nodes below them.
Cameras and turrets do not move and need no flying nodes. A map without security bots needs
none either. See [31-Security.md](31-Security.md) for the bots.

1. In the Actor Class browser, select NavigationPoint > PathNode > FlyingPathNode.
2. Right-click the floor below the place where the node belongs and click **Add FlyingPathNode
   here**. Then move the node up into the air.
3. Repeat over the space where the bots fly.

Rules for placement, taken from the game's own maps and from the build:

| Rule | Reason |
| --- | --- |
| Place a flying node 200 to 250 units above each ground node in open areas. | The game's maps carry one flying node above most ground nodes, at that height. A ground node links to a flying node only when it is inside the flying node's collision radius. Keep the flying node above the ground node, not beside it. |
| Keep flying nodes 150 units or less apart, or raise their `CollisionRadius`. | Two flying nodes link only when the distance between them is less than the sum of their collision radii. The default radius is 80 units. With `CollisionRadius` set to 256, nodes link at up to about 500 units. |
| Keep a clear line between neighbours. | The build links two flying nodes only when nothing blocks the line between their centres. |
| Keep the height difference between neighbours under 170 units. | The build links two flying nodes only when their collision cylinders overlap in height. The default height is 100 units. |
| Add nodes in high rooms and shafts. | The game's maps place nodes 400 to 650 units up in tall halls. |
| Add nodes 3000 to 6000 units away from the places where the player fights, out of sight. | The alarm creates its bots at a navigation point that the player cannot see, within that distance. |

`CollisionRadius` and `CollisionHeight` are in the Collision category of the node's Properties
window.

## Build the paths

Click Build > Rebuild AI Paths. The BioShock editor removes the old links, traces every pair of nodes
and stores the new links. The build also writes the movement data and the size-class data
that the game reads for every link.

Build > Rebuild Changed AI Paths rebuilds only around nodes that moved. Use the full build
before a bake.

Build > Build All runs the path build after the geometry build and the lighting build.

**NOTE:** A map whose paths were built with an older editor carries links without the
size-class data. The game's AI cannot use those links and stands still. The bake writes
this warning to the log:

```
Bake: WARNING: ... reachspecs, none with size-class bits: the path network was built by an older editor. Run Build > Rebuild AI Paths ...
```

Run Build > Rebuild AI Paths and bake again.

## Check the paths

### Show the paths in a viewport

Right-click the caption bar of a viewport, open the **View** submenu and click **Show
Paths**. Links draw as lines between the nodes. A node without lines has no link. Move it
or add a node between it and its neighbour, then build again.

### Review the paths

Tools > Review Paths checks the network and lists problems in the map check window.
Examples of the messages:

| Message | Meaning |
| --- | --- |
| No navigation point list. Paths define needed. | The path build has never run. |
| Only N PlayerStarts in this level | The map has fewer starts than the review expects. |
| No navigation point associated with this mover! | A mover that AI must cross has no lift or door navigation point. |

### Movers and doors

A mover in the AI's route needs a navigation point that describes it. The Door actor in the
Engine package marks a mover that opens for AI. Set bAutoDoor on the mover to let the build
create the Door navigation point for it. Lift navigation points for elevators are not
covered in this guide.

## Limits

- The build does not place flying nodes for you. Place every flying node by hand. The build
  creates no network for ceiling crawlers.
- PlayerPathNode is a navigation point for the player's own guidance system. It is not part
  of the AI network.
- The build does not place nodes for you. Every node is placed by hand.

## Related documents

- [07-BSP-Geometry.md](07-BSP-Geometry.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [36-Troubleshooting.md](36-Troubleshooting.md)
- [27-Spawning-AI.md](27-Spawning-AI.md)
- [28-Patrols.md](28-Patrols.md)
- [31-Security.md](31-Security.md)
