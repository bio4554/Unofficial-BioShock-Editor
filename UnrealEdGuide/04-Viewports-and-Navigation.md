# Viewports and Navigation

This document tells you how to look at the map: the viewport types, the camera controls,
the view modes, the show flags and the grid. Read it before you edit geometry or place
actors. The BioShock editor draws a transform gizmo on the selection; the mouse-button
combinations in this document are the keyboard alternative.

## Before you start

- The BioShock editor is open with a map loaded.
- The Camera Movement mode is active on the button bar. This is the default mode.

## Viewport types

The default layout has four viewports.

| Viewport | Projection | Shows |
|---|---|---|
| Top | Orthographic, looking down the Z axis | X to the right, Y down the screen. |
| Front | Orthographic, looking along the Y axis | X to the right, Z up. |
| Side | Orthographic, looking along the X axis | Y to the right, Z up. |
| Perspective | 3D | The map as the game shows it, in the current view mode. |

The orthographic viewports draw wireframes. The perspective viewport draws the map with
the SDK's renderer, which runs the game's shaders.

Any viewport can change its type. Open the caption bar menu and pick Top, Front, Side or
one of the perspective view modes. The shortcuts are Alt+7 (Top), Alt+8 (Front), Alt+9
(Side) and Alt+1 (Perspective wireframe).

## The caption bar

Each viewport has a caption bar. The caption shows the viewport type or the view mode.
Click the caption bar to make the viewport active. The caption bar holds:

- A menu button on the left. It opens the viewport menu.
- The Realtime Preview button. When on, the viewport redraws continuously and animated
  materials move. The keyboard shortcut is P with the mouse over the viewport.
- One button for each viewport type and view mode.

The viewport menu has these sections:

| Section | Items |
|---|---|
| Mode | Top, Front, Side, Perspective, Texture Usage, BSP Cuts, Textured, Lighting, Lit Untextured, Dynamic Lighting, Zones/Portals. |
| View | Show flags. See "Show flags" below. |
| Actors | Full Actor View, Information, Icon View, Radii View, Hide Actors. |
| Window | 16-Bit Color, 32-Bit Color. Leave at 32-Bit. |
| Lock to Selected Actor? | The camera moves and turns with the selected actor. |
| Show Large Vertices? | Draws brush vertices larger in the orthographic viewports. |

## Camera controls in the perspective viewport

Hold the mouse button and move the mouse. The camera speed button on the button bar
scales all moves. The speed cycles through 1, 4 and 16.

| Mouse | Effect |
|---|---|
| Left drag | Move forward and back (mouse up and down). Turn left and right (mouse left and right). |
| Right drag | Look around. Pitch with mouse up and down. Yaw with mouse left and right. |
| Left + Right drag | Move up and down (mouse up and down). Strafe left and right (mouse left and right). |
| Alt + Right drag | Roll the camera. |

The perspective camera has no collision. It passes through walls.

## Camera controls in an orthographic viewport

| Mouse | Effect |
|---|---|
| Left drag | Pan the view. |
| Right drag | Pan the view at double speed. |
| Middle drag | Pan the view. |
| Left + Right drag | Zoom. Move the mouse up and down. |
| Mouse wheel | Zoom. |

## Selection with the mouse

The clicks below work in every viewport. They need the Camera Movement mode.

| Mouse | Effect |
|---|---|
| Left-click an actor | Select the actor. Deselect everything else. |
| Ctrl + Left-click an actor | Add the actor to the selection, or remove it. |
| Left-click a surface | Select the surface. Deselect everything else. |
| Ctrl + Left-click a surface | Add the surface to the selection, or remove it. |
| Ctrl + Shift + Left-click a surface | Select the brush that the surface belongs to. |
| Left-click empty space | Deselect everything. |
| Ctrl + Alt + Left drag in an orthographic viewport | Box selection. Actors inside the box are selected. |
| Double-click an actor | Select the actor and open Actor Properties. |
| Right-click an actor | Select the actor and open the actor context menu. |
| Right-click a surface | Select the surface and open the surface context menu. |
| Right-click empty space | Open the backdrop context menu. |

Selected BSP surfaces highlight in a blue-purple tint. Selected static mesh actors and
skeletal mesh actors highlight in green. Skeletal mesh actors are picked by clicking the
mesh itself.

Shift + Middle drag in an orthographic viewport measures a distance. The viewport draws
the line and its length in units.

## Moving actors with the mouse

The BioShock editor draws a transform gizmo on the selection: a set of colored handles on the
pivot of the selected actors (the small cross). The gizmo follows the edit mode on the button
bar on the left of the main window:

| Mode | Gizmo | Handles |
|---|---|---|
| Camera Movement | Move | An arrow per axis and an L-shaped grabber per plane. |
| Actor Rotate | Rotate | A ring per axis. |
| Actor Scaling | Scale | A cube-headed line per axis, a chamfer per plane, and a small cube on the pivot for uniform scale. |

The Space key cycles the three modes while the mouse is over a viewport. Shift + Space
switches the handles between world space and local space.

The X handle is red, the Y handle is green and the Z handle is blue. The handle under the
mouse turns yellow. Press the left button on a yellow handle and drag: the actors move along
that axis, in that plane, around that ring or by that scale factor, and the camera stays
put. Release the button to finish. One drag is one undo step (Ctrl+Z).

- The gizmo keeps the same size on screen at any distance and any zoom.
- An orthographic viewport shows the two axes in its plane and the grabber for that plane.
  In Actor Rotate mode it shows the ring around the axis that points at you.
- A move snaps to the Drag Grid when the grid is on. A rotation snaps to the Rotation Grid.
  Sub-grid motion accumulates, so the actors jump one grid step at a time. See "The grid"
  below. A move always snaps to the world grid, in local space as well.
- With several actors selected, the actors move together, and a rotation orbits them
  around the pivot. The pivot sits on the first actor you selected; after a box selection
  it sits at the center of the selection.
- Hold Shift during the drag to make the camera follow the actors.
- A click on a handle never selects the actor behind it.

Preferences (F7 in the main window) > Advanced > UseActorRotationGizmo turns the gizmo off
and on. It is on by default.

### World space and local space

The handles stand on the world axes to begin with: the X handle points along world X
wherever the actor is facing. Shift + Space stands them on the selected actor's own axes
instead, so the X handle points out of the actor's front, and a move, a rotation or a scale
follows the way the actor is turned. Shift + Space again goes back to world space.

A small label beside the pivot reads World or Local, so you can always see which one you
are in. The choice is remembered between sessions, and Preferences > Advanced lists it as
GizmoLocalSpace.

- In local space the axes come from the actor you selected first. With several actors
  selected they all move and turn together around the pivot, following that actor's axes.
- An orthographic viewport hides the handle that points at you. In local space that is
  whichever handle happens to line up with the view; when the actor is turned at an angle
  to the view, all three handles stay.
- Axis and plane scaling change an actor's DrawScale3D along its own axes, and scaling a
  brush moves its vertices around the pivot, so scaling already worked in the actor's own
  space. Local space lines the handles up with what the scale actually does.

### The keyboard alternative

Hold Ctrl and drag with a mouse button. The selected actors move instead of the camera. This
works with the gizmo off, and it also works when the pointer is not over a handle.

| Viewport | Mouse | Effect |
|---|---|---|
| Orthographic | Ctrl + Left drag | Move the actors in the plane of the view. |
| Orthographic | Ctrl + Right drag | Rotate the actors around the axis that points out of the view. |
| Perspective | Ctrl + Left drag | Move along the world X axis (mouse left and right). |
| Perspective | Ctrl + Right drag | Move along the world Y axis (mouse left and right). |
| Perspective | Ctrl + Left + Right drag | Move along the world Z axis (mouse up and down). |

Hold Shift together with Ctrl to make the camera follow the actors while they move.

Rotation and scaling have their own modes on the button bar.
The scale gizmo works on actors and brushes. Ctrl drags in the button bar's Actor Scaling mode also scale mesh actors.
The separate `MODE ACTORSCALE` command has legacy Ctrl drags that scale brushes only. See
[09-Actors-and-Properties.md](09-Actors-and-Properties.md) for moving, rotating and scaling,
including the Properties window fields and the Align commands.

The Drag Grid controls the step of a move. See "The grid" below.

## View modes

A perspective viewport draws in one view mode. Pick the mode from the caption bar
buttons, from the viewport menu, or with Alt and a number key while the mouse is over
the viewport.

| View mode | Shortcut | Shows | Use it to |
|---|---|---|---|
| Wireframe | Alt+1 | Brush and mesh edges, no fill. | Check geometry and overlapping brushes. |
| Zone/Portal | Alt+2 | Each zone in a flat color. Zone portals in a distinct color. | Check that zone portals seal the zones. See [08-Zones-and-Portals.md](08-Zones-and-Portals.md). |
| Texture Usage | Alt+3 | One flat color per material on BSP. | Find surfaces that share a material. |
| BSP Cuts | Alt+4 | One flat color per BSP node. | Find surfaces that the geometry build split many times. |
| Dynamic Light | Alt+5 | The game's look: textures, normal maps, specular, baked light and ambient. | Judge the final look. |
| Textured | Alt+6 | The textures with no lighting. | Find missing or wrong textures. |
| Lighting Only | Alt+0 | The baked lighting on white surfaces. No normal maps. No specular. | Judge light placement and intensity. |
| Lit Untextured | Menu only | The baked lighting on white surfaces with normal maps and specular. | Judge the lighting with the surface detail. |
| Depth Complexity | None | Not supported in the BioShock editor. | Do not use. |

Notes on the view modes:

- Water, glass and light beams keep their real materials in Lighting Only and in Lit
  Untextured.
- The sky keeps its textures in every mode.
- A surface whose cutout mask lives in the texture keeps its texture in the untextured
  modes, so the cutout stays visible.
- The static lighting in Dynamic Light, Lighting Only and Lit Untextured comes from the
  last lighting build. A moved light does not change the view until you run the lighting
  build again. See [14-Lighting.md](14-Lighting.md).
- The Zone/Portal, Texture Usage and BSP Cuts modes color the BSP. Placed static meshes
  keep their textures in these modes.

The first time a viewport draws a material, the BioShock editor compiles the material's shaders.
The viewport stutters for a moment. This happens once per material per editor session.

## Show flags

The View section of the viewport menu turns categories of things on and off. Some flags
have a single-key shortcut. The key works while the mouse is over the viewport.

| Flag | Key | Shows or hides |
|---|---|---|
| Show Active Brush | B | The builder brush. |
| Show Static Meshes | W | Static mesh actors. |
| Show Moving Brushes | | Movers. |
| Show Volumes | O | Volume actors. |
| Show Backdrop | K | The sky and backdrop surfaces. |
| Show Coordinates | | The coordinate axes. |
| Show Paths | | The AI path network lines. See [26-AI-Paths.md](26-AI-Paths.md). |
| Show Event Lines | E | Lines from an actor to the actors it triggers. |
| Show Selection Highlight | S | The selection tint on surfaces and meshes. |
| Show Terrain | T | Terrain. Not used by BioShock. |
| Show Distance Fog | F | The zone's distance fog. |
| Matinee Rotations, Matinee Paths | | Matinee overlays. Not used by BioShock. |
| Show Fluid Surfaces | | Fluid surface actors. |
| Show Karma Primitives | | Karma physics shapes. Not used by BioShock. |
| Show Collision | | Collision shapes. |
| Post Processing | | The game's bloom and streaks (from the level's GlowSettings) in lit perspective views. On by default. |

Two more keys work in the same way: H hides and shows all actors, and Q hides and shows
the BSP.

The Actors section of the viewport menu picks how actors draw: Full Actor View draws the
meshes, Icon View draws only the sprites, Radii View draws collision and sound radii,
Information adds the actor names, and Hide Actors hides them all. The I key toggles
Information. A selected light always draws its lit shape, whatever the view mode; see
[14-Lighting.md](14-Lighting.md).

## The grid

The orthographic viewports draw a grid. Actors and brushes snap to it when the Drag Grid
is on.

- The Toggle Drag Grid button in the bottom bar turns snapping on and off.
- The Drag Grid Size list in the bottom bar sets the step, from 1 to 4096 units.
- The Grid submenu of the backdrop context menu sets the same step.
- The Toggle Rotation Grid button snaps rotations to fixed steps. Rotation values use
  65536 per full turn, so 16384 is 90 degrees.
- The Toggle Vertex Snap button snaps dragged vertices to other vertices.

Work with a large grid step for room-sized brushes and a small step for trim. A brush
that sits off the grid causes BSP errors. See [07-BSP-Geometry.md](07-BSP-Geometry.md).

## Camera speed and position

- Change Camera Speed on the button bar cycles the speed through 1, 4 and 16. The
  console command `MODE SPEED=4` sets it directly.
- Align Cameras on the actor context menu moves every viewport camera to the selected
  actor.
- Lock to Selected Actor? on the viewport menu ties the camera to the selected actor.
  When you move the camera, the actor moves with it. Turn it off when you are done.
- The Set submenu on the actor context menu has Location/Rotation from Camera. It moves
  the selected actor to the perspective camera. This is the way to place a PlayerStart
  where you stand.

## Background images

View > Background Image loads a picture behind an orthographic viewport. Use it to trace a
floor plan. Open loads a file. Center, Tile and Stretch set how the picture fills the
view. Clear removes it.

## Related documents

- [03-Interface-Tour.md](03-Interface-Tour.md)
- [07-BSP-Geometry.md](07-BSP-Geometry.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [14-Lighting.md](14-Lighting.md)
- [38-Keyboard-and-Mouse-Reference.md](38-Keyboard-and-Mouse-Reference.md)
