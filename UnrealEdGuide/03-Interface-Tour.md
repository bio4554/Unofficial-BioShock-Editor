# Interface Tour

This document names every part of the BioShock editor's main window and tells you what each part
is for. It is a tour, not a manual. Each section links to the document that gives the
details. Read it once, then use it as a map when you look for a control.

## Before you start

- The BioShock editor starts and opens a map. See [02-Setup.md](02-Setup.md).

## The main window

The main window has five areas, from top to bottom and left to right:

| Area | Position | Purpose |
|---|---|---|
| Menu bar | Top | All commands. See "The menu bar" below. |
| Toolbar | Under the menu bar | Buttons for the most used commands. |
| Button bar | Left edge | Editing modes, brush builders, CSG operations, visibility. |
| Viewports | Center | The views into the map. Four by default. |
| Bottom bar | Bottom edge | Command line, selection lock, grid controls, scale fields. |

The browsers and the Properties windows are separate windows. They open on top of the
main window.

## The menu bar

| Menu | Contents |
|---|---|
| File | New, Open, Save (Ctrl+L), Save As, Bake, Import and Export of `.t3d` map text, Import and Export of INT localization files. |
| Edit | Undo (Ctrl+Z), Redo (Ctrl+Y), Search for Actors, Cut, Copy, Paste, Duplicate (Ctrl+W), Delete, Select None (Shift+Z), Select All Actors (Shift+A), Select All Surfaces (Shift+S), Select Surfaces submenu. |
| View | Log, the browsers, Actor Properties (F4), Surface Properties (F5), Level Properties (F6), Advanced Options, the Viewports submenu, Background Image. |
| Brush | Brush Clip, Reset, Scale, Add (Ctrl+A), Subtract (Ctrl+S), Intersect (Ctrl+N), Deintersect (Ctrl+D), Add Mover, Add Antiportal, Add Special, Open Brush (Ctrl+B), Save Brush As, Import, Export, Batch Import. |
| Build | Play Level (Ctrl+P), Rebuild Geometry Only, Rebuild Lighting Only, Rebuild Changed Lighting Only, Rebuild AI Paths, Rebuild Changed AI Paths, Build All, Build Options. |
| Tools | 2D Shape Editor, Scale Lights, Check Map for Errors, Scale Map, Remove Existing Particles, Rotate Actors, Review Paths, Array, Particle Editor. |
| Help | What's New (the release notes of the installed version), Tip of the Day. |

**NOTE:** The Save shortcut is Ctrl+L, not Ctrl+S. Ctrl+S subtracts the builder brush from
the world. See [07-BSP-Geometry.md](07-BSP-Geometry.md).

## The toolbar

The toolbar sits under the menu bar. Hold the mouse over a button to read its tooltip.
From left to right:

| Button | Command |
|---|---|
| New Map, Open Map, Save Map | File > New, File > Open, File > Save. |
| Undo, Redo | Edit > Undo, Edit > Redo. |
| Search for Actors | Opens the search dialog. Type a part of a name and select an actor in the list. |
| Actor Class Browser, Group Browser, Music Browser, Sound Browser, Texture Browser, Mesh Browser, Prefab Browser, Static Mesh Browser, Animation Browser | Open one browser each. See "The browsers" below. |
| 2D Shape Editor | Opens the 2D Shape Editor. See [07-BSP-Geometry.md](07-BSP-Geometry.md). |
| UnrealScript Editor | Opens the script editor. Not used for level work. |
| Actor Properties, Surface Properties | Open the Properties windows. Same as F4 and F5. |
| Build Geometry, Build Lighting, Build Changed Lighting, Build Paths, Build Changed Paths, Build All, Build Options | The Build menu commands. See [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md). |
| Play Map! | Bakes the map into the game install and starts the game. See [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md). |
| Context Sensitive Help | Opens an old web help page. |

## The button bar

The button bar is the column of buttons on the left edge. It has five groups. Click a
group header to collapse or expand the group.

### Modes

One mode is active at a time. The mode changes what a mouse drag in a viewport does. Space
cycles the three gizmo modes, and Shift + Space switches the gizmo handles between world
space and the selected actor's own space.

| Button | Mode | Use |
|---|---|---|
| Camera Movement | The normal mode. | Move the camera. Select actors, and move them with the gizmo or with Ctrl. See [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md). |
| Vertex Editing | Vertex edit. | Move single brush vertices. See [07-BSP-Geometry.md](07-BSP-Geometry.md). |
| Actor Scaling | Scale. | Scale actors and brushes with the gizmo or Ctrl drags. This button selects `MODE ACTORSNAP`. |
| Actor Rotate | Rotate. | Rotate actors with the rotate gizmo, or with Ctrl and a mouse drag. |
| Texture Pan | Texture pan. | Slide the texture on the selected surfaces. |
| Texture Rotate | Texture rotate. | Rotate the texture on the selected surfaces. |
| Brush Clipping | Brush clip. | Cut brushes with a clip plane. |
| Freehand Polygon Drawing | Polygon. | Draw a brush polygon by placing markers. |
| Face Drag | Face drag. | Drag one face of a brush. |
| Terrain Editing | Terrain. | Not tested with BioShock content. |
| Matinee | Matinee. | Camera path tool inherited from the base engine. Not tested with BioShock content. |

### Clipping

Clip Selected Brushes, Split Selected Brushes, Flip Clipping Normal, Delete Clipping
Markers. These act on the clip markers you place in Brush Clipping mode. See
[07-BSP-Geometry.md](07-BSP-Geometry.md).

### Builders

Each builder button reshapes the builder brush. Left-click applies the last settings.
Right-click opens the settings dialog.

| Builder | Shape |
|---|---|
| Cube | A box. |
| Cylinder | A cylinder with a chosen number of sides. |
| Cone | A cone. |
| Sheet | A flat plane with no thickness. |
| Linear Staircase | A straight staircase. |
| Curved Staircase | A staircase that turns around a center. |
| Spiral Staircase | A staircase that turns around a column. |
| Tetrahedron (Sphere) | A sphere made from a subdivided tetrahedron. |
| BSP Based Terrain | A subdivided sheet for BSP terrain. |
| Volumetric (Torches, Chains, etc) | Crossed sheets for flames and chains. |

### CSG

Add, Subtract, Intersect, Deintersect, Add Special Brush, Add Static Mesh, Add Mover
Brush, Add Antiportal, Volume. These create world geometry or actors from the builder
brush. Right-click Add Mover Brush and Volume to pick a class. See
[07-BSP-Geometry.md](07-BSP-Geometry.md) and [08-Zones-and-Portals.md](08-Zones-and-Portals.md).

### Misc

Show Selected Actors Only, Hide Selected Actors, Show All Actors, Invert Selection, and
Change Camera Speed. The camera speed button cycles through 1, 4 and 16.

## The viewports

The center of the window holds the viewports. The default layout has four: three
orthographic views (Top, Front, Side) and one perspective view. Each viewport has a
caption bar with a menu. See [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md).

- View > Viewports > Configure opens the "Viewport Config" dialog. Pick one of the layouts.
- View > Viewports > Floating turns each viewport into a free window. View > Viewports >
  Fixed puts them back into the main window.
- View > Viewports > New Viewport opens one more floating viewport.
- The "Maximize Viewport" button in the bottom bar enlarges the active viewport.

## The bottom bar

From left to right:

| Control | Purpose |
|---|---|
| Command : | The console command line. Type a command and press Enter. See [02-Setup.md](02-Setup.md). |
| Show Full Log Window | Opens the log window. Same as View > Log. |
| Lock Selections? | When on, clicks in the viewports do not change the selection. |
| Toggle Vertex Snap | When on, dragged vertices snap to other vertices. |
| Toggle Drag Grid | When on, moved actors and brushes snap to the grid. |
| Drag Grid Size | The grid step in units. 1 to 4096. |
| Toggle Rotation Grid | When on, rotations snap to the rotation grid. |
| Maximize Viewport | Enlarges the active viewport to fill the viewport area. |
| DrawScale3D X, Y, Z | The non-uniform scale of the selected actors. Type a value and press Enter. |

## The browsers

View > Show Master Browser opens the Master Browser. It holds the browsers as tabs. Each
browser also opens on its own from the View menu or the toolbar. A browser's View >
Docked item moves it into or out of the Master Browser.

| Tab | Purpose | Details |
|---|---|---|
| Actor Classes | The class tree. Select a class, then add it to the map with a right-click in a viewport. Open a package with File > Open Package. | [09-Actors-and-Properties.md](09-Actors-and-Properties.md) |
| Groups | Named groups of actors. Show, hide, select and rename groups. | [09-Actors-and-Properties.md](09-Actors-and-Properties.md) |
| Textures | Textures and materials by package and group. Select one to make it the current material. File > Open loads a package. | [13-Materials-and-Textures.md](13-Materials-and-Textures.md) |
| Static Meshes | Static meshes by package and group. Select one, then place it. File > Open loads a package. | [10-Static-Meshes.md](10-Static-Meshes.md) |
| Meshes | Skeletal meshes of the original Unreal Engine. View only. Use the Animation browser for BioShock meshes. | [11-Skeletal-Meshes-and-Animation.md](11-Skeletal-Meshes-and-Animation.md) |
| Animation Browser | Skeletal meshes and their animations. Select a mesh to list its clips; play, loop, scrub and step through them. File > Mesh import brings in a rigged glTF file (`.glb` or `.gltf`) as a BioShock skeletal mesh with its skeleton and clips. File > Animation import replaces the selected mesh's clips from a glTF file. View > Lit or Unlit picks a shaded preview or the plain textures. | [11-Skeletal-Meshes-and-Animation.md](11-Skeletal-Meshes-and-Animation.md) |
| Sounds | Sound packages. Play a sound. | |
| Music | Not used by BioShock. | |
| Prefabs | Not supported in the BioShock editor. Do not use. | |

The browsers show packages that are loaded. A map loads the packages it uses. To see a
package from the content library that the map does not use, open it in the browser with
File > Open. Select a file from `Content\` in the SDK folder.

**NOTE:** Every content library package is a `.pkg` file, whatever it holds. The Open and
Save dialogs of the Texture, Static Mesh, Sound and Music browsers filter for `*.pkg` and
save with that extension, so a package you save from any browser lands on the content
library search path. Any browser can load any `.pkg` package. The Actor Classes browser's
Open Package dialog lists `*.pkg` and `*.u`. Pick "Legacy Packages" or "All Files" in the
file type list to see `.u`, `.utx` or `.usx` files from an older SDK; rename such a
file to `.pkg` to put it on the search path.

## The Properties windows

There are three Properties windows. All three use the same property grid.

| Window | Shortcut | Shows |
|---|---|---|
| Actor Properties | F4, or double-click an actor | The properties of the selected actors. With more than one actor selected, the grid shows the shared properties. |
| Surface Properties | F5, or right-click a surface | The properties of the selected BSP surfaces: material alignment, pan, rotate, scale, flags, lightmap scale. |
| Level Properties | F6 | The LevelInfo actor of the map. |

### The property grid

- The grid lists categories. Click a category name to expand or collapse it. The
  categories are named after the property groups in the class, for example Lighting,
  Display, Collision.
- Click a value to edit it. Type a number and press Enter. Pick an enum value from the
  list. Check boxes are True or False.
- A color value opens with the "Color" button. The "Pick" button takes a color from the
  screen.
- An object reference value (a texture, a static mesh, a class) has three buttons:
  "..." opens the value, "Use" takes the object that is selected in the matching browser,
  and "Clear" empties the value. "Find" jumps to the object in the browser.
- An array value has "Add" and "Empty" on the array row, and "Insert" and "Delete" on
  each element row.
- Some values are objects that live inside the actor. They show a "New" button that
  creates the inner object. Expand the row to edit the inner object's own properties.
- "Reset To Defaults" at the bottom sets every property back to the class default.

Ranged properties show their limits as part of the value. The grid clamps a typed value
to the range. Example: `LightBrightness` accepts 0 to 5.

Level scripting actors (Script, Trigger) show each entry of their Actions list as a one-line
sentence, such as "Trigger effect event X with tag Y", the way the original tools did. Expand
a row to edit that action's fields directly. Some name properties, such as a corpse's PoseName,
show a drop-down list of the valid names. Writing level scripts is out of scope for this guide.

## The Log window

View > Log opens the log window. It shows the same text that the BioShock editor writes to
`System\Editor.log`. Keep it open while you build. Every warning from the geometry build,
the lighting build, the path build and the bake goes here.

## Related documents

- [02-Setup.md](02-Setup.md)
- [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md)
- [07-BSP-Geometry.md](07-BSP-Geometry.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [38-Keyboard-and-Mouse-Reference.md](38-Keyboard-and-Mouse-Reference.md)
