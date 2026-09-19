# Static Meshes

This document explains how to find static meshes in the content library, how to place
them, how their collision and lighting work in the game, and what you must know before you
import a mesh of your own. Read it before you decorate a map.

## Before you start

- The content library is built and the BioShock editor finds it. See [02-Setup.md](02-Setup.md).
- You can place and move actors. See [09-Actors-and-Properties.md](09-Actors-and-Properties.md).

## What a static mesh is

A static mesh is a mesh asset that lives in a package. A static mesh actor is an actor in
the map that draws one static mesh. Many actors can draw the same mesh. Almost every
visible object in a BioShock map is a static mesh: walls, pillars, furniture, signs,
machines and debris. BSP gives the rooms their shape and their zones. Static meshes give
them their look.

If you know Hammer, a static mesh actor is a prop_static. If you know Radiant, it is a
misc_model. If you know a newer Unreal Engine, it is the same concept.

## The Static Mesh browser

Open the browser with View > Show Static Mesh Browser.

| Control | Function |
| --- | --- |
| Package list | The packages that are loaded. Select one to see its meshes. |
| Group list | The groups inside the package. Select one, or All, to filter the mesh list. |
| Mesh list | The meshes in the selected package and group. Click one to preview it. |
| Preview pane | Shows the selected mesh. Left-drag rotates the view. Right-drag zooms. |
| Property pane | The properties of the selected mesh: the Materials array, the collision settings and NeverCollide. |

| Menu item | Function |
| --- | --- |
| File > Open... | Loads a package from the content library. |
| File > Save | Saves the selected package. See the caution under "Save a package". |
| File > Import... | Imports a mesh from an `.ase`, `.gltf`, `.glb` or `.lwo` file. |
| Edit > Load Entire Package | Loads every object in the package, not only the list. |
| Edit > Add to Level | Places the selected mesh in the map at the last click point. |
| Edit > Create from Selection... | Makes a static mesh from the selected brushes. |
| Edit > Prev Group, Next Group | Steps through the groups of the package. |
| Edit > Delete, Rename... | Removes or renames a mesh in the package. |
| View > Show Collision | Draws the game collision in the preview: green shapes stop the player and physics objects, orange shapes stop physics objects only (the player then hits the mesh triangles), and the BioShock editor collision model in the box colour. The text under the mesh name says how many shapes the mesh has and what the player hits. |
| View > Lit, View > Unlit | The preview's render mode. Unlit shows the materials at full brightness. |

The right-click menu on a mesh in the list has **Sections...**. It lists the material
sections of the mesh.

## Find meshes in the content library

The content library holds about 366 packages. The shared environment kit is in the `Gen_`
packages. District art has a prefix that names the level.

### The shared kit

| Package | Contents |
| --- | --- |
| Gen_Architecture | Tunnel sections, girders, planters, the load room door and lever. |
| Gen_Archways | Modular art deco archways and pillars. |
| Gen_Stairs | Stairs, railings, catwalks, support cylinders, posts. |
| Gen_Doors | Door art: frames, sliding doors, gates, bulkheads. |
| Gen_Windows | Window frames in every size, broken variants, shutters. |
| Gen_FloorsWallsAndCeilings | Tiling surface materials: marble, concrete, wood, tin ceiling. |
| Gen_Counter_System | Modular shop counter kit. |
| Gen_Elevator | Elevator car, doors, shaft and call buttons. |
| Gen_Decor | The largest prop set: tables, benches, buckets, phones, trash cans, moulding. |
| Gen_Office | Desks, chairs, cabinets, lamps. |
| Gen_Library | Bookcases, books, ladders, globes, rugs. |
| Gen_Kitchen | Pots, pans, plates, cabinets. |
| Gen_Containers | Crates, safes, boxes, briefcases. |
| Gen_Fine_Art | Paintings and framed canvases. |
| Gen_Signs | Neon signs, bulletin boards, arrows, typeface shaders. |
| Gen_Lights | Light fixtures: sconces, chandeliers, lamps, neon, and light shaft meshes. |
| Gen_Ambience | Animated wall machinery, vents, fans, blinking lights, TV screens. |
| Gen_BattleDebris | Sandbags, barricades, rubble, blood, damage decals. |
| Gen_Water | Water materials and water jet meshes. See [15-Water-and-Fluids.md](15-Water-and-Fluids.md). |
| Gen_Glass | Glass materials, window glass, breakable glass. |
| Gen_Seascape | Dead fish, crabs, sharks, seaweed. |

### Naming conventions

| Prefix | Meaning |
| --- | --- |
| `Gen_` | Shared art used in every level. |
| `Wel_`, `Bat_` | Welcome to Rapture, bathysphere. |
| `Int_` | Intro: lighthouse, plane crash, seascape. |
| `Med_` | Medical Pavilion. |
| `Res_` | Residential, Apollo Square. |
| `Rec_` | Fort Frolic. |
| `Fis_` | Neptune's Bounty. |
| `Hyd_`, `Mar_` | Arcadia, Farmer's Market. |
| `Eng_` | Hephaestus. |
| `Sci_` | Point Prometheus, Fontaine Futuristics. |
| `Gau_` | Proving Grounds. |
| `*_MESH`, `*Anim` | One animated prop with its skeletal mesh and its clips. |
| `MA_` | Interactive machines. |
| `WP_`, `PU_` | Weapons and world pickups. |

Open a package with File > Open... in the Static Mesh browser. The file dialog starts in
the content library folder and lists its `.pkg` packages. Select one.

## Place a static mesh

1. In the Static Mesh browser, select the package, the group and the mesh.
2. In a viewport, right-click on the surface where the mesh must stand.
3. Click **Add Static Mesh**.

The BioShock editor places a StaticMeshActor with the mesh at the click point. Move and rotate it as
described in [09-Actors-and-Properties.md](09-Actors-and-Properties.md).

To place another copy of a mesh that is already in the map, select the actor and press
Ctrl + W. To change the mesh of an actor, select the mesh in the browser, open the
Properties window and click the Use button next to StaticMesh in the Display category.

**Select > Matching Static Meshes** on the right-click menu selects every actor that uses
the same mesh.

## Static mesh actor properties

The defaults of StaticMeshActor fit a wall or a piece of furniture. The table lists the
properties you change most.

| Category | Property | Default | Meaning |
| --- | --- | --- | --- |
| Display | StaticMesh | none | The mesh to draw. |
| Display | DrawScale, DrawScale3D | 1 | Scale. Use DrawScale3D for a scale per axis. |
| Display | Skins | empty | Material overrides. Entry N replaces material slot N of the mesh. |
| Display | bStaticLighting | True | The lighting build lights the actor. |
| Display | bShadowCast, bCastStaticShadow | True | The actor blocks light in the lighting build. |
| Display | MaxLightsStatic | class default | Maximum number of static lights on this actor. |
| Display | SpecialLitChannel | 0 | Lighting channel. See [14-Lighting.md](14-Lighting.md). |
| Display | bUseDynamicLights | True | Dynamic lights in the game light the actor. |
| Display | bAcceptsProjectors | True | Decals and projected shadows draw on the actor. |
| Display | CullDistance | 0 | Distance at which the game hides the actor. |
| Collision | bCollideActors | True | The actor takes part in collision. |
| Collision | bBlockActors, bBlockPlayers | True | The actor stops actors and players. |
| Collision | bWorldGeometry | True | The actor counts as world for traces and paths. |
| Advanced | bStatic | True | The actor never moves. Read only. |

## Collision in the game

The game does not use the BioShock editor's collision data. At load time the game builds its own
collision from two sources:

- The BSP of the map.
- The Havok collision proxy that is stored with each static mesh in its package.

Every mesh in the content library has a collision proxy. A placed library mesh collides
in the game the way it did in the original levels.

Every mesh the BioShock editor makes gets a collision proxy of its own:

- An imported `.ase` or glTF file takes its proxy from the collision objects in the file.
  See "Collision objects in an .ase or glTF file" below.
- A mesh without collision objects gets a box that fits its bounds. This covers
  **Convert > To Static Mesh**, **Create from Selection...**, an `.ase`, glTF or `.lwo` file
  without collision objects, and a compile-time import.

The game keeps two copies of the collision. Physics objects, such as thrown props and
ragdolls, collide with the Havok proxy. The player, the AI and weapon traces collide with
the simple collision stored on the mesh: the same boxes, spheres, capsules and convex
shapes, or the mesh triangles when the mesh has none. The original meshes carry both, made
from the same collision objects. A mesh with a box fitted to its bounds therefore stops a
thrown prop at the box, and the player at the mesh triangles. A mesh whose file has
collision objects stops the player at those shapes, which is how the original props
behave. View > Show Collision in the Static Mesh browser draws what the game will use.

**CAUTION:** A mesh made with an SDK build before this one has no proxy and crashes the
game as soon as a map places it. Import the mesh again, or select it in the Static Mesh
browser and type `STATICMESH REBUILD` in the command box at the bottom of the editor
window. Either gives it a proxy. Save the package afterwards.

### Collision objects in an .ase or glTF file

The original game authored its collision in 3ds Max as extra objects in the same `.ase`,
named by shape. The BioShock editor reads the same names, in an `.ase` and in a glTF file.
Model the collision as simple closed objects around the mesh and name each one with one of
these prefixes:

| Prefix | Shape | What the editor takes from the object |
| --- | --- | --- |
| `MCDBX` or `HKCBX` | Box | The object's orientation and the size of its vertices along each axis. |
| `MCDSP` or `HKCSP` | Sphere | The centre of the vertices and the distance to the farthest one. |
| `HKCCP` | Capsule | The object's local Z axis is the length; the radius comes from the vertices. |
| `MCDCY` or `HKCCY` | Cylinder | Kept as a convex shape made of the object's faces. |
| `MCDCX` or `HKCCX` | Convex shape | The object's vertices and faces. The object must be convex. |

Any number of collision objects may share one file. A collision object is never rendered.
Prefixes are read without regard to case. An object named `MCD` with any other suffix is
ignored, as in the stock Unreal ASE importer. In a glTF file the prefix goes on the object
(the node name, which is what Blender writes for an object) or on the name of its mesh data. The collision objects also become the
BioShock editor collision that Snap to Floor and the path build use, so the editor and the
game agree.

Keep collision objects simple. A box or a few boxes serve most props. Use a convex shape
only where a box is clearly wrong, and keep it under a few dozen faces.


### Editor collision

The BioShock editor has its own collision data for meshes. The preview shows it with View > Show
Collision. **Save Brush As Collision** on the right-click menu of a static mesh actor
replaces the BioShock editor collision model of the mesh with the shape of the builder brush. The
game ignores this collision model. Use it only to make editor placement and Snap to Floor
work with a custom mesh.

**NOTE:** Static mesh collision in the BioShock editor viewports affects only editor tools such as
Snap to Floor, the actor fit test and Build Paths. It has no effect in the game.

### NeverCollide

Every mesh has a **NeverCollide** property in the property pane of the Static Mesh browser. When
it is True, an actor that uses the mesh never collides, in the game or in the BioShock editor,
whatever the Collision settings on that actor say. The original levels set it on the meshes that
are only a picture: light beams and light shafts, blood splats, glass dust, damage decals,
carpets, paper trash, water spews and cascades, muzzle and ice effects. Those meshes never stop
the player, the AI, Snap to Floor or the path build, and the game builds no Havok body for
them. Leave it False for everything that has a physical shape.

## Lighting on static meshes

The lighting build stores light per vertex on every static mesh actor. The game combines
that stored value with the light colours, the normal maps and the specular settings of the
material at run time. The rules that matter when you place meshes:

- One mesh actor takes up to MaxLightsStatic lights. The default is 3. The lighting build
  picks the strongest lights that reach the actor. Raise the value on an actor that needs
  more lights, or lower it on an actor that needs fewer.
- A mesh actor shows correct lighting only after a lighting build. After you move a mesh
  or a light, the viewport shows stale lighting until the next lighting build.
- LightMapScale has no effect on static meshes. It affects BSP surfaces.
- Light beam meshes and meshes with cutout materials do not block light.

See [14-Lighting.md](14-Lighting.md) for the complete lighting model.

## Convert a mesh to a brush

**Convert > To Brush** on the right-click menu of a static mesh actor makes the builder
brush take the shape of the mesh. Use it to cut a hole that fits a door frame, or to trace
reference geometry. The result is often a brush with many polygons. Do not add such a
brush to the BSP.

## Import a custom static mesh

1. Open the Static Mesh browser.
2. Click File > Import...
3. Select an `.ase`, `.gltf`, `.glb` or `.lwo` file. A `.gltf` needs its `.bin` file beside it.
4. Enter a package name, a group name and a mesh name. Use a new package name for your own
   content.
5. In the property pane, expand Materials. Set the Material of each slot with the Use
   button and a material selected in the Texture browser.

The mesh appears in the browser and can be placed like a library mesh. Test it in the
BioShock editor.

The imported mesh has game collision from the collision objects in the file, or a box
fitted to its bounds when the file has none. See "Collision in the game" above. The
log window reports which one the mesh got.

### glTF files

glTF 2.0 is the format most current modelling tools export directly (in Blender: File >
Export > glTF 2.0). Export the mesh and its collision objects, as a `.glb` or as a `.gltf`
with a separate or embedded `.bin`, without animation. The editor reads:

- Every object with a mesh, merged into one static mesh with its scene transforms applied.
  Objects whose name starts with a collision prefix become collision instead (see
  "Collision objects in an .ase or glTF file" above).
- The normals as exported, so the mesh is as smooth or as flat as it was in the tool. A
  file without normals imports flat shaded.
- Up to eight sets of texture coordinates and the vertex colours.
- The materials. Each glTF material becomes one material slot. The editor looks for a
  loaded material with the glTF material's name, then with the name of its base colour
  texture, then with that texture's file name without path and extension, so open the
  package that holds your materials first. A slot that finds nothing gets the current
  material of the Texture browser; set it in the property pane afterwards.

Orientation and size: the mesh arrives with the same orientation an `.ase` export of the
same scene gives, so a model that faces the tool's front view faces the same way in the
editor whichever file you export. One glTF unit is one Unreal unit. A tool that exports in
metres writes the numbers you modelled with, so a 128 unit cube in Blender is a 128 unit
cube in the editor. To scale a file on import, use the command box at the bottom of the
editor window instead of the menu:

```
STATICMESH IMPORT FILE="C:\Models\Crate.glb" NAME=Crate PACKAGE=MyMeshes GROUP=Props SCALE=52.5
```

Not imported: skeletons, skin weights, animation, morph targets, cameras and lights. A rigged
model goes through the Animation browser instead; see
[11-Skeletal-Meshes-and-Animation.md](11-Skeletal-Meshes-and-Animation.md). A file
compressed with Draco is refused; export without mesh compression.

**NOTE:** A mesh can also be imported at compile time, from a script package, so that it is
built again from its `.ase` or glTF file every time the package is compiled. See
[34-Importing-Assets-with-UCC.md](34-Importing-Assets-with-UCC.md).

## Save a package

File > Save in the Static Mesh browser writes the selected package to disk. The Save dialog
defaults to the `.pkg` extension and to the content library folder, so the package lands
on the content library search path. Type a name of your own, for example `MyProps`.
The map does not need your package file after a bake. The bake embeds every mesh, texture
and material that the map references into the baked map. Players need only the baked map
and its bulk content files.

**CAUTION:** Save custom content to a new package with a name of your own. Do not save a
library package such as `Gen_Decor.pkg` after you changed it. Do not write any package into
the game's `Builds\Release` folder. The game loads its own `.U` files from there, and a
replaced file can stop the game from loading.

## Related documents

- [02-Setup.md](02-Setup.md)
- [07-BSP-Geometry.md](07-BSP-Geometry.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [13-Materials-and-Textures.md](13-Materials-and-Textures.md)
- [14-Lighting.md](14-Lighting.md)
- [15-Water-and-Fluids.md](15-Water-and-Fluids.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [36-Troubleshooting.md](36-Troubleshooting.md)
- [34-Importing-Assets-with-UCC.md](34-Importing-Assets-with-UCC.md)
