# Keyboard and Mouse Reference

This document lists the keyboard shortcuts and the mouse-button combinations of the BioShock editor.
Use it as a lookup table. The viewports draw a transform gizmo on the selection; the
mouse-button combinations below are the alternative way to move, rotate and scale actors.
The separate legacy `MODE ACTORSCALE` command limits Ctrl-drag scaling to brushes.

## Keyboard shortcuts in the main window

These shortcuts work when the main editor window has the focus.

### File

| Shortcut | Command |
| --- | --- |
| Ctrl+N | File > New |
| Ctrl+O | File > Open |
| Ctrl+L | File > Save |

### Edit

| Shortcut | Command |
| --- | --- |
| Ctrl+Z | Undo |
| Ctrl+Y | Redo |
| Ctrl+X | Cut |
| Ctrl+C | Copy |
| Ctrl+V | Paste |
| Ctrl+W | Duplicate |
| Delete | Delete |
| Ctrl+F | Find |
| Ctrl+H | Replace |
| Shift+A | Select All Actors |
| Shift+S | Select All Surfaces |
| Shift+Z | Select None |

### Brush

| Shortcut | Command |
| --- | --- |
| Ctrl+A | Brush > Add |
| Ctrl+S | Brush > Subtract |
| Ctrl+N | Brush > Intersect (also File > New; the menu shows both) |
| Ctrl+D | Brush > Deintersect |
| Ctrl+B | Brush > Open Brush |

**NOTE:** The accelerator table binds Ctrl+N to both File > New and Brush > Intersect.
Use the menu for one of them.

### Build

| Shortcut | Command |
| --- | --- |
| Ctrl+P | Build > Play Level (Play Map) |
| F9 | Build changed |

### Properties and windows

| Shortcut | Command |
| --- | --- |
| F4 | Actor Properties |
| F5 | Surface Properties |
| F6 | Level Properties |
| F7 | Preferences (main window); compile scripts (viewport) |
| F8 | Build Options window (viewport) |

### Surface selection

These shortcuts work with one or more surfaces selected.

| Shortcut | Command |
| --- | --- |
| Shift+G | Select Matching Groups |
| Shift+I | Select Matching Items |
| Shift+B | Select Matching Brush |
| Shift+T | Select Matching Texture |
| Shift+J | Select All Adjacents |
| Shift+C | Select Adjacent Coplanars |
| Shift+W | Select Adjacent Walls |
| Shift+F | Select Adjacent Floors and Ceilings |
| Shift+S | Select Adjacent Slants (menu); Select All Surfaces (accelerator) |
| Shift+Q | Reverse selection |
| Ctrl+M | Memorize Set |
| Shift+R | Recall Memory |
| Shift+O | Or With Memory |
| Shift+U | And With Memory |
| Shift+X | Xor With Memory |

**NOTE:** The menu shows Shift+M for Memorize Set and Shift+S for Adjacent Slants. The
accelerator table binds Ctrl+M and Shift+S to other commands. Use the menu for these two.

## Keyboard shortcuts inside a viewport

These keys work when the mouse pointer is over a viewport and the viewport has the focus.

### View modes

| Key | View mode |
| --- | --- |
| Alt+1 | Wireframe |
| Alt+2 | Zone/Portal |
| Alt+3 | Texture Usage |
| Alt+4 | BSP Cuts |
| Alt+5 | Dynamic Light |
| Alt+6 | Textured |
| Alt+0 | Lighting Only |
| Alt+7 | Top view (orthographic XY) |
| Alt+8 | Front view (orthographic XZ) |
| Alt+9 | Side view (orthographic YZ) |

Lit Untextured has no key. Select it from the viewport's View > Mode menu.

### Show and hide

| Key | Toggles |
| --- | --- |
| B | Brush wireframes |
| H | Actors |
| K | Backdrop (sky) |
| W | Static meshes |
| E | Event lines between actors |
| I | Actor information text |
| S | Selection highlight |
| T | Terrain |
| F | Distance fog |
| O | Volumes |
| Q | BSP |
| P | Realtime preview (animated materials play) |

### Editing in a viewport

| Key | Command |
| --- | --- |
| Space | Cycle the gizmo mode: Camera Movement, Actor Rotate, Actor Scaling |
| Shift+Space | Switch the gizmo handles between world space and local space |
| Delete | Delete the selected actors |
| Ctrl+Z, Ctrl+Y | Undo, Redo |
| Ctrl+X, Ctrl+C, Ctrl+V | Cut, Copy, Paste |
| Ctrl+W | Duplicate |
| Ctrl+A | Brush > Add |
| Ctrl+S | Brush > Subtract |
| Ctrl+I | Brush > Intersect |
| Ctrl+D | Brush > Deintersect |
| Ctrl+L | Save |
| Ctrl+E | Save As |
| Ctrl+O | Open |
| Ctrl+P | Play Map |
| Shift+A | Select all actors |
| Shift+N | Select none |
| Shift+D | Duplicate |
| Shift+S | Select all surfaces |
| Shift+B, G, I, T | Select surfaces with the matching brush, group, item, texture |
| Shift+J, C, W, F, Y | Select adjacent surfaces: all, coplanar, walls, floors, slants |
| Shift+Q | Reverse the surface selection |
| Shift+M, R, O, U, X | Surface selection memory: set, recall, or, and, xor |

**NOTE:** Inside a viewport, Ctrl+I is Intersect and Shift+Y is Adjacent Slants. The main
window uses Ctrl+N and Shift+S for the same commands.

## Edit modes

The Modes group of the button bar on the left of the main window selects the edit mode. The mode changes what a
mouse drag does.

| Mode | Purpose |
| --- | --- |
| Camera Movement | Move the camera. Move actors with the move gizmo or with Ctrl. This is the default mode. |
| Vertex Editing | Move brush vertices. |
| Actor Scaling | Scale actors and brushes with the gizmo or Ctrl drags. This button selects `MODE ACTORSNAP`. |
| Actor Rotate | Rotate the selected actors with the rotate gizmo, or with Ctrl and a mouse drag. |
| Texture Pan | Pan the texture of the selected surfaces with a mouse drag. |
| Texture Rotate | Rotate the texture of the selected surfaces with a mouse drag. |
| Brush Clipping | Place clip markers and clip the brush. |
| Freehand Polygon Drawing | Draw a polygon that becomes the builder brush. |
| Face Drag | Drag a brush face. |

## Mouse: camera movement in the perspective view

These combinations work in Camera Movement mode with no key held.

| Buttons | Motion |
| --- | --- |
| Left drag | Move forward and backward. Turn left and right. |
| Right drag | Look around: pitch and yaw. |
| Left + Right drag | Move up and down. Strafe left and right. |
| Alt + Right drag | Roll the camera. |

The mouse wheel does not zoom the perspective view.

## Mouse: camera movement in the orthographic views

| Buttons | Motion |
| --- | --- |
| Left drag | Pan the view. |
| Right drag | Pan the view. |
| Middle drag | Pan the view. |
| Left + Right drag | Zoom in and out. |
| Mouse wheel | Zoom in and out. |

## Mouse: select

| Action | Result |
| --- | --- |
| Left click on an actor or surface | Select it. Clear the previous selection. |
| Ctrl + Left click | Add to or remove from the selection. |
| Ctrl + Alt + Left drag (orthographic view) | Box select. |
| Shift + Middle drag (orthographic view) | Measure a distance. The viewport shows the length. |
| Right click | Open the context menu for the actor, the surface or the backdrop. |

## Mouse: transform gizmo

The gizmo sits on the pivot of the selected actors and follows the edit mode. The X handle is
red, the Y handle green, the Z handle blue; the handle under the pointer is yellow. The
handles stand on the world axes, or on the first selected actor's own axes in local space; a
label beside the pivot reads World or Local.

| Action | Result |
| --- | --- |
| Left drag an axis arrow (Camera Movement mode) | Move along that axis. |
| Left drag a plane grabber (Camera Movement mode) | Move in that plane. |
| Left drag a ring (Actor Rotate mode) | Rotate around that axis. |
| Left drag an axis cube (Actor Scaling mode) | Scale along that axis. |
| Left drag a plane chamfer (Actor Scaling mode) | Scale along the two axes of that plane. |
| Left drag the center cube (Actor Scaling mode) | Scale uniformly. |
| Shift while dragging | The camera follows the actors. |
| Space | Cycle Camera Movement, Actor Rotate, Actor Scaling. |
| Shift+Space | Switch between world space and local space. |

Moves snap to the Drag Grid and rotations to the Rotation Grid when those are on. One drag
is one undo step. Preferences > Advanced > UseActorRotationGizmo turns the gizmo off, and
GizmoLocalSpace on the same page holds the world / local choice.

## Mouse: move actors

Hold Ctrl in Camera Movement mode. The selected actors move instead of the camera. Movement
snaps to the grid. These drags also work with the gizmo off.

### Perspective view

| Buttons | Motion |
| --- | --- |
| Ctrl + Left drag | Move along the world X axis. |
| Ctrl + Right drag | Move along the world Y axis. |
| Ctrl + Left + Right drag | Move along the world Z axis (up and down). |

### Orthographic views

| Buttons | Motion |
| --- | --- |
| Ctrl + Left drag | Move in the plane of the view. |
| Ctrl + Right drag | Rotate about the axis that points at you. |
| Ctrl + Left + Right drag | Zoom the view. |

Hold Shift instead of Ctrl to move the actors and the camera together.

## Mouse: rotate actors

Select Actor Rotate mode. Hold Ctrl.

| Buttons | Motion |
| --- | --- |
| Ctrl + Left drag | Pitch. |
| Ctrl + Right drag | Yaw. |
| Ctrl + Left + Right drag | Roll. |

In the orthographic views in Camera Movement mode, Ctrl + Right drag also rotates about the
view axis.

## Mouse: scale actors

Select Actor Scaling mode on the button bar. Hold Ctrl.
This mode uses `MODE ACTORSNAP` and scales brushes and mesh actors when you release the buttons.

| Buttons | Motion |
| --- | --- |
| Ctrl + Left drag | Scale along the X axis. |
| Ctrl + Right drag | Scale along the Y axis. |
| Ctrl + Left + Right drag | Scale along the Z axis. |

The separate console command `MODE ACTORSCALE` selects the legacy brush scale mode.
Its Ctrl drags scale brushes only. Ctrl + Alt drags also scale the brush locations in that mode.
The gizmo scales actors and brushes in both modes.

## Mouse: vertex editing

Select Vertex Editing mode and select a brush.

| Action | Result |
| --- | --- |
| Left click on a vertex | Select the vertex. |
| Ctrl + Left click on a vertex | Add the vertex to the selection. |
| Ctrl + Left drag, Ctrl + Right drag, Ctrl + both | Move the selected vertices. The buttons pick the axes as for actors. |
| Right click on a selected vertex | Snap the selected vertices to the grid. |

In Camera Movement mode, Alt + Left drag on a vertex grabs that vertex and moves it. Release
Alt or the button to release the vertex.

## Mouse: texture alignment

Select Texture Pan mode or Texture Rotate mode. Select one or more surfaces. Hold Ctrl.

| Mode | Buttons | Result |
| --- | --- | --- |
| Texture Pan | Ctrl + Left drag | Pan the texture in U. |
| Texture Pan | Ctrl + Right drag | Pan the texture in V. |
| Texture Pan | Ctrl + Left + Right drag | Scale the texture. |
| Texture Pan | Add Shift | Pan in fine steps. |
| Texture Rotate | Ctrl + Left drag | Rotate the texture. |

## Mouse: apply a texture to a surface

Select a texture in the Texture browser first.

| Action | Result |
| --- | --- |
| Alt + Left click on a surface | Apply the current texture to that surface. |
| Ctrl + Alt + Left click on a surface | Apply the current texture and copy the alignment of the last surface. |
| Shift + Left click on a surface | Apply the current texture to every selected surface. |
| Ctrl + Shift + Left click on a surface | Select the brush that owns the surface. |

## Related documents

- [03-Interface-Tour.md](03-Interface-Tour.md)
- [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md)
- [07-BSP-Geometry.md](07-BSP-Geometry.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
