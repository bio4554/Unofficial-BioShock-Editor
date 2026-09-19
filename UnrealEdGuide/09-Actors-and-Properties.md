# Actors and Properties

This document explains what an actor is, how to place and select actors, how to move them
with the gizmo or with the Ctrl drags, and how to edit their properties. Read it before you place lights, static
meshes, or any other object in a map.

## Before you start

- You can open a map and move the camera. See [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md).
- You know the difference between a brush and an actor. See
  [05-Concepts-for-Hammer-Radiant-and-UE-Users.md](05-Concepts-for-Hammer-Radiant-and-UE-Users.md).

## What an actor is

An actor is any object that the BioShock editor places in the map at a location and a rotation.
Lights, player starts, path nodes, zone info markers, static mesh actors, movers, triggers
and volumes are all actors. Brushes are actors too, but the geometry build turns them into
BSP. Everything else stays an actor at run time.

If you know Hammer, an actor is an entity. If you know Radiant, an actor is an entity. If you
know a newer Unreal Engine, an actor is the same concept.

Every actor has a class. The class defines the properties that the actor has, and the
behaviour that the game runs. You choose a class in the Actor Class browser, then you place
one or more actors of that class.

## The Actor Class browser

Open the browser with View > Show Actor Class Browser. It shows the class tree of every
package that is loaded.

| Control | Function |
| --- | --- |
| Class tree | Lists classes by parent. Expand a node to see its child classes. Click a class to make it the current class. |
| Placeable classes Only? | Checked by default. Hides abstract classes and classes that the BioShock editor cannot place. Keep it checked. |
| File > Open Package | Loads a package from disk so that its classes appear in the tree. |
| View > Show Packages | Shows the list of loaded packages. |
| Class > New | Creates a script class. Not covered in this guide. |

### Where the placeable classes are

The classes come from the script packages in the SDK folder and from the content
library. The table lists the packages a mapper uses.

| Package | Location | Classes |
| --- | --- | --- |
| Engine | `System\Engine.u`, always loaded | Light, PlayerStart, PathNode, ZoneInfo, SkyZoneInfo, StaticMeshActor, Mover, Door, BlockingVolume, PhysicsVolume, FluidVolume, Emitter, Projector, Teleporter, Note, AntiPortalActor, CubemapProbe |
| ShockGame | `System\ShockGame.u`, always loaded | Game objects. Examples: VendingStation, HealthStation, ResearchStation, FuseBox, SecurityStation, MovableSpotlight, AudioBeacon, PhysicalStaticMeshContainer |
| VengeanceShared | `System\VengeanceShared.u`, always loaded | ReactiveActor and the animated mesh attachment classes |
| Scripting | `System\Scripting.u`, always loaded | Script, Trigger, TriggerRadius, ScriptableMover. Level scripting is not covered in this guide. |
| ShockDesignerClasses | `Content\ShockDesignerClasses.pkg` | Preconfigured game objects: weapon pickups, machines, doors, decorations |
| ShockAIClasses | `Content\ShockAIClasses.pkg` | Preconfigured enemy and AI classes. AI is not covered in this guide. |
| Doors, AnimatedDecorClasses | `Content\Doors.pkg`, `Content\AnimatedDecorClasses.pkg` | Door and animated decoration classes |

**NOTE:** The content library packages are not loaded when the BioShock editor starts. Use File >
Open Package in the Actor Class browser to load `Content\ShockDesignerClasses.pkg` before you
place its classes. A retail map loads the packages it uses when you open it.

**NOTE:** Prefabs are not supported in the BioShock editor. The Prefab browser and the Add Prefab
menu items do nothing useful.

## Place an actor

1. In the Actor Class browser, click the class. It becomes the current class.
2. In a viewport, right-click on a surface or on a static mesh at the place where you want
   the actor.
3. Click **Add <class> here**. The menu shows the name of the current class.

The BioShock editor places the actor on the clicked surface. If the actor does not fit at the click
point, the BioShock editor lifts it by the actor's collision height and writes a message in the log.
If the actor still does not fit, the BioShock editor places it at the click point without a fit test.

Two shortcuts place actors without the menu:

| Action | Result |
| --- | --- |
| Hold **A** and left-click on a surface | Places the current class at the click point. |
| Hold **L** and left-click on a surface | Places a Light at the click point. |

The right-click menu also has **Add Light here** and **Add Static Mesh**. Add Static Mesh
places the mesh that is selected in the Static Mesh browser. See [10-Static-Meshes.md](10-Static-Meshes.md).

In an orthographic viewport, right-click on empty space also shows the Add menu. The actor
is placed on the view plane at the click point.

## Select actors

| Action | Result |
| --- | --- |
| Left-click an actor | Selects the actor. Clears the previous selection. |
| Ctrl + left-click an actor | Adds the actor to the selection, or removes it if it is selected. |
| Ctrl + Alt + left-drag in an orthographic viewport | Draws a box. Actors inside the box are selected. |
| Left-click on empty space | Clears the selection. |
| Double-click an actor | Selects the actor and opens the Properties window. |
| Shift + Ctrl + left-click a BSP surface | Selects the brush that made the surface. |
| Shift + Z | Clears the selection. |
| Shift + A | Selects every actor. |

Selected static mesh actors and skeletal mesh actors highlight green in the 3D viewport.
Selected brushes show their wireframe in the selection colour. Selected sprite actors turn
green.

The right-click menu on an actor has a **Select** submenu:

| Item | Result |
| --- | --- |
| All <class> Actors | Selects every actor of the clicked actor's class. |
| All Actors in Matching Zone | Selects every actor in the same zone. |
| Matching Static Meshes | Selects every actor that uses the same static mesh. |
| All Actors, None | Selects everything, or nothing. |
| Adds, Subtracts, Semisolids, Nonsolids | Selects brushes by CSG type. |

### Search for actors

Edit > Search for Actors opens a dialog with a list of every actor in the map. Type in the
Name, Event or Tag fields to filter the list. Click an actor in the list to select it. The
viewports move to the actor.

## Move, rotate and scale actors

The BioShock editor draws a transform gizmo on the selection. Drag its handles to move, rotate
or scale the selected actors; see "Moving actors with the mouse" in
[04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md). The Ctrl drags below are
the keyboard alternative: hold Ctrl and drag with the mouse buttons. The camera does not move
while Ctrl is held. Hold Shift instead of Ctrl to move the actor and the camera together.

### Move actors in a perspective viewport

| Mouse action | Result |
| --- | --- |
| Ctrl + left-drag left or right | Moves along the world X axis. |
| Ctrl + right-drag left or right | Moves along the world Y axis. |
| Ctrl + left-drag + right-drag up or down | Moves along the world Z axis. |

The perspective viewport moves actors along the world axes, not along the screen axes.
Use an orthographic viewport for most placement work.

### Move and rotate actors in an orthographic viewport

| Mouse action | Result |
| --- | --- |
| Ctrl + left-drag | Moves the actor in the plane of the view. |
| Ctrl + right-drag left or right | Rotates the actor around the axis that points out of the view. |
| Ctrl + left-drag + right-drag up or down | Zooms the view. |

In the Top view, Ctrl + right-drag changes the yaw. In the Front view it changes the pitch.
In the Side view it changes the roll.

### Rotate actors with the rotate tool

The button bar on the left of the main window has an Actor Rotate mode button. In rotate
mode the Ctrl drags rotate instead of move.

| Mouse action | Result |
| --- | --- |
| Ctrl + left-drag | Changes the pitch. |
| Ctrl + right-drag | Changes the yaw. |
| Ctrl + left-drag + right-drag | Changes the roll. |

Rotation snaps to the rotation grid when the rotation grid is on. Turn it on and off with
the Toggle Rotation Grid button in the bottom bar.

### Scale actors and brushes with the gizmo

Select Actor Scaling mode on the button bar. Drag a gizmo handle with the left mouse button.
The axis handles scale along one axis. The plane handles scale along two axes.
The center handle scales uniformly.

For actors other than brushes, uniform scaling changes DrawScale. Axis and plane scaling change DrawScale3D.
For brushes, the gizmo scales the vertices around the pivot.
You can also change DrawScale or DrawScale3D in the Properties window to scale a static mesh actor.

### Scale with Ctrl drags

The Actor Scaling button selects `MODE ACTORSNAP`. Its Ctrl drags scale brushes and mesh actors.
Ctrl + left-drag scales along X. Ctrl + right-drag scales along Y.
Ctrl + both mouse buttons scales along Z. The scale applies when you release the buttons.

The separate console command `MODE ACTORSCALE` selects the older scale mode. Its Ctrl drags change brushes only.
In that mode, hold Alt with Ctrl to scale brush locations with the brush size.
The gizmo can scale actors in either mode. The brush-only limit applies to legacy Ctrl drags in `MODE ACTORSCALE`.

### Menu commands that change the transform

Right-click an actor to see these items.

| Item | Result |
| --- | --- |
| Reset > Move To Origin | Moves the actor to 0,0,0. |
| Reset > Rotation, Scaling, All | Returns the rotation, the scale, or both to the default. |
| Set > Location/Rotation from Camera | Moves the actor to the camera position and rotation. |
| Transform > Mirror About X, Y, Z | Mirrors a brush about the axis. |
| Transform > Scale... | Scales a brush by a number. |
| Transform > Transform Permanently | Bakes the rotation and scale of a brush into its polygons. |
| Align > Snap to Floor | Drops the actor to the surface below it. |
| Align > Align to Floor | Drops the actor and rotates it to the surface normal. |
| Duplicate (Ctrl + W) | Makes a copy at an offset. |
| Delete (Del) | Removes the selected actors. |
| Edit > Paste > To Original Location, Here, At World Origin | Pastes copied actors. |

### Grid snapping

The right-click menu has a **Grid** submenu with sizes from 1 to 4096 units. Actor
movement snaps to this grid when the drag grid is on. The Toggle Drag Grid button is in
the bottom bar. Most actors have bEdShouldSnap set, so their location stays on the grid.

Place the pivot with **Pivot > Place Pivot Here** or **Place Pivot Snapped Here**. Rotation
and scale work around the pivot.

## The Properties window

Open the Properties window with F4, with a double-click on an actor, or with the
**Properties** item on the right-click menu. The window shows the properties of every
selected actor. If several actors are selected, the window edits all of them at once.

Properties are grouped in categories. Click the plus sign to expand a category. Click a
value to edit it. Bool properties show True and False. Enum properties show a drop-down
list. Object properties show a text field and a Use button. The Use button takes the
current selection from the matching browser, for example the Texture browser or the
Static Mesh browser.

### Ranged properties

Some numeric properties in this engine carry a minimum and a maximum. The property clamps
any value you type to that range. LightBrightness is an example. Its range is 0 to 5. If
you type 7, the value becomes 5. CollisionRadius has a range of 0 to 10000.

### Categories that every actor has

The table lists the properties a mapper uses. Other properties exist. Leave them at the
default unless a document tells you to change them.

| Category | Property | Meaning |
| --- | --- | --- |
| Display | DrawType | What the actor draws: DT_Sprite, DT_StaticMesh, DT_Mesh, DT_None. |
| Display | StaticMesh | The static mesh, when DrawType is DT_StaticMesh. |
| Display | Skins | Material overrides for the mesh. One entry per material slot. |
| Display | DrawScale, DrawScale3D | Uniform scale, or a scale per axis. |
| Display | bStaticLighting | The lighting build lights the actor. On for static mesh actors. |
| Display | bUnlit | Lights do not affect the actor. |
| Display | bShadowCast, bCastStaticShadow | The actor blocks light in the lighting build. |
| Display | MaxLightsStatic | Maximum number of static lights that light the actor. |
| Display | SpecialLitChannel | Lighting channel. A light and a receiver must match. See [14-Lighting.md](14-Lighting.md). |
| Display | LightMapScale | Lightmap texel size class of the actor. |
| Display | bAcceptsProjectors | Projectors and decals draw on the actor. |
| Display | CullDistance | Distance at which the game stops drawing the actor. 0 means no limit. |
| Collision | bCollideActors | The actor takes part in collision at all. |
| Collision | bBlockActors, bBlockPlayers | The actor stops other actors, or players. |
| Collision | CollisionRadius, CollisionHeight | The collision cylinder. Height is the half height. |
| Movement | Location, Rotation | Position in units and rotation in 65536 per turn. |
| Movement | Physics | PHYS_None for placed objects. |
| Events | Tag | The name other actors use to find this actor. |
| Events | Event | The name this actor sends when it fires. |
| Lighting | LightType, LightBrightness, LightColor, LightRadius, ... | See [14-Lighting.md](14-Lighting.md). |
| Sound | AmbientSound, SoundRadius, SoundVolume | A looping sound at the actor. |
| Advanced | bHidden | The game does not draw the actor. |
| Advanced | bHiddenEd | The BioShock editor does not draw the actor. |
| Advanced | bMovable | The actor can move at run time. |
| Advanced | bEdShouldSnap | The actor snaps to the grid. |
| Object | Group | The group names of the actor, separated by commas. |
| Object | Name | The unique name of the actor. Read only. |
| Object | InitialState | The state the actor starts in. |

Some properties show but do not accept edits. bStatic and bNoDelete are examples. The
class sets them.

## Groups

The Group browser lists the groups in the map. Open it with View > Show Group Browser.

| Menu item | Result |
| --- | --- |
| New Group | Creates a group and puts the selected actors in it. |
| Rename Group, Delete Group | Changes or removes the selected group. |
| Add Selected Actors to Group | Puts the selected actors in the selected group. |
| Delete Selected Actors from Group | Removes them from the group. |
| Select Actors, Deselect Actors | Selects or deselects every actor in the group. |

Each group has a check box. Clear the check box to hide the group in the viewports. Hidden
actors do not take part in selection. The map remembers which groups were visible when you
saved it.

An actor can be in several groups. The Group property in the Object category holds the
list.

## Level Properties

Open Level Properties with F6 or with **Level Properties** on the right-click menu of empty
space. The window shows the properties of the LevelInfo actor. The map has exactly one.

| Category | Property | Meaning |
| --- | --- | --- |
| LevelSummary | Title, Author, Description | Text that describes the map. |
| Audio | Song, MusicVolumeOverride | Streamed music for the map. |
| Headlights | bUseHeadlights | Player headlight setting. |

Do not change DefaultGameType. The game sets the game type itself.

**NOTE:** KillZ, the height below which actors are destroyed, is a property of ZoneInfo,
not of LevelInfo. See [08-Zones-and-Portals.md](08-Zones-and-Portals.md).

## Movers

A mover is a brush that moves between keyframes. Elevators, platforms and simple doors are
movers.

### Create a mover

1. Shape the builder brush to the mover shape. See [07-BSP-Geometry.md](07-BSP-Geometry.md).
2. Click Brush > Add Mover. The BioShock editor makes a Mover actor from the builder brush.
3. Right-click the mover and click **Mover > Key 1**.
4. Move the mover to the position it must reach.
5. Right-click the mover and click **Mover > Key 0 (Base)**. The mover returns to the start
   position. The BioShock editor stores both positions.

The menu offers keys 0 to 7. Key 0 is the rest position. NumKeys in the Properties window
shows how many keys the mover uses.

### Mover properties

| Property | Meaning |
| --- | --- |
| MoveTime | Seconds to move from one key to the next. |
| StayOpenTime | Seconds the mover waits at the last key before it returns. |
| DelayTime | Seconds the mover waits after a trigger before it moves. |
| bTriggerOnceOnly | The mover moves one time, then stops responding. |
| MoverEncroachType | What the mover does when an actor blocks it: stop, return, crush or ignore. |
| MoverGlideType | Linear motion, or ease in and out. |
| bUseShortestRotation | Rotates by the shortest path between keys. |
| OpeningSound, ClosingSound, ... | Sounds in the MoverSounds category. |
| OpeningEvent, ClosedEvent, ... | Events in the MoverEvents category. |
| InitialState | How the mover starts: TriggerOpenTimed, BumpOpenTimed, StandOpenTimed, ConstantLoop and others. |

**NOTE:** The doors in the game use the game's own door classes, which the content library
provides. Their logic and their wiring to the game's power system are not covered in this
guide.

## PlayerStart

Play Map spawns the player at a PlayerStart actor. A new map needs at least one.

1. Select PlayerStart in the Actor Class browser. It is under NavigationPoint >
   SmallNavigationPoint.
2. Right-click the floor and click **Add PlayerStart here**.
3. Rotate the actor to face the direction the player must look. The yaw is what matters.

| Property | Meaning |
| --- | --- |
| bEnabled | The start is active. Keep it True. |
| bPrimaryStart | The game prefers this start. |
| bSinglePlayerStart | The game uses the first start with this flag for single player. |

TeamNumber and bCoopStart have no meaning in BioShock. Leave them at the default.

**CAUTION:** A PlayerStart inside a wall or below the floor makes the player spawn stuck.
Place it 16 units or more above the floor and run the geometry build before you play.

## Related documents

- [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md)
- [05-Concepts-for-Hammer-Radiant-and-UE-Users.md](05-Concepts-for-Hammer-Radiant-and-UE-Users.md)
- [07-BSP-Geometry.md](07-BSP-Geometry.md)
- [08-Zones-and-Portals.md](08-Zones-and-Portals.md)
- [10-Static-Meshes.md](10-Static-Meshes.md)
- [14-Lighting.md](14-Lighting.md)
- [36-Troubleshooting.md](36-Troubleshooting.md)
- [38-Keyboard-and-Mouse-Reference.md](38-Keyboard-and-Mouse-Reference.md)
