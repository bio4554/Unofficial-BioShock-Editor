# Concepts for Hammer, Radiant and Unreal Engine 4/5 Users

This document maps the concepts of the BioShock editor to the concepts of Hammer (Source), Radiant
(Quake, Doom 3) and Unreal Engine 4/5. Read it before you build your first map. It tells you
which habits transfer and which habits you must change.

## Before you start

- Read [01-Introduction.md](01-Introduction.md) for what the SDK and the BioShock editor are.
- Read [03-Interface-Tour.md](03-Interface-Tour.md) for the names of the windows.

## The world is solid until you subtract

In Hammer and Radiant the world starts empty. You add solid blocks and the space between
them is the play area. Sealing the map against the void is your responsibility.

In the BioShock editor the world starts as solid rock. You **subtract** a room from the rock with a
brush. The inside of the subtracted shape is the play area. Then you **add** brushes inside
that room for pillars, walls and stairs. There is no void to seal. A map with one
subtracted cube is a complete, sealed map.

Unreal Engine 4/5 has no BSP world in this sense. Its geometry brushes are a minor tool.
In the BioShock editor, BSP is the shell of every level. The shell is simple. Detail comes from
static meshes placed inside the shell.

Consequences:

- Do not build the outer walls of a room from solid blocks. Subtract the room instead.
- Additive brushes that touch the solid rock disappear into it. This is normal.
- A leak is impossible. A hole in a room shows solid rock, not the void.

## One builder brush shapes every operation

Hammer has a block tool that creates a new solid on each drag. Radiant creates a new brush
on each drag.

The BioShock editor has one **builder brush**. It is the red wireframe shape. It is not part of the
map. You set its shape with a brush builder (cube, cylinder, cone, stairs, sheet), move it
into position, and then apply a CSG operation:

| Operation | Menu | Result |
|---|---|---|
| Add | Brush > Add (Ctrl+A) | A solid brush with the builder's shape |
| Subtract | Brush > Subtract (Ctrl+S) | A hollow space with the builder's shape |
| Intersect | Brush > Intersect (Ctrl+N) | The builder brush becomes the part of itself that is inside solid space |
| Deintersect | Brush > Deintersect (Ctrl+D) | The builder brush becomes the part of itself that is in empty space |

The builder brush stays where it is after the operation. You can apply it again. See
[07-BSP-Geometry.md](07-BSP-Geometry.md).

## Brush order matters

Hammer and Radiant treat all brushes as one set. Order does not matter.

The BioShock editor applies the brushes in list order. A subtract that comes after an add cuts the
add. An add that comes after a subtract fills the subtract. Each brush keeps its place in
the list. Two commands change that order:

- Right-click a brush > Order > To First
- Right-click a brush > Order > To Last

After you change brush order, run a geometry build to see the result. See
[07-BSP-Geometry.md](07-BSP-Geometry.md).

## No compile tools, but four build steps

Hammer runs vbsp, vvis and vrad. Radiant runs q3map2 or the Doom 3 compiler. Unreal Engine
4/5 builds lighting and navigation from the Build menu.

The BioShock editor has the same kind of steps in its Build menu:

| Build step | Menu | Hammer equivalent | What it does |
|---|---|---|---|
| Geometry build | Build > Rebuild Geometry Only | vbsp | Cuts the brushes into the BSP tree, finds the zones, and computes the leaf data |
| Lighting build | Build > Rebuild Lighting Only | vrad | Runs BioShock's static lighting bake |
| Path build | Build > Rebuild AI Paths | Navigation mesh | Links the PathNodes into the network the game's AI uses |
| Build All | Build > Build All | All of the above | Geometry, lighting and paths in one run |

There is no visibility step that you run. The geometry build computes the zone and portal
data the game uses for visibility. See [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).

## Save is not the same as bake

Hammer saves a `.vmf` and compiles a `.bsp`. Unreal Engine 4/5 saves `.umap` files and
cooks a packaged build.

The BioShock editor has two output commands:

| Command | Output | Use |
|---|---|---|
| File > Save (Ctrl+L) | A saved map, `.unr` | The editing format. It stores your geometry and actors and refers to the content library for the rest. Open it again in the BioShock editor. |
| File > Bake | A baked map, `.bsm` | The playable format. It embeds every content object the map uses and adds the data that the game needs. Copy it into the game's `Content\Maps` folder. |

A saved map is a `.unr` file and a baked map is a `.bsm` file. Keep your working files as saved maps. Bake when you want to
play. The game does not load a saved map. See
[18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).

## Zones and zone portals

Source divides the world into visleafs with hint brushes and areaportals. Radiant uses
portals that the compiler finds, and areaportal brushes. Doom 3 uses visportals.

The BioShock editor divides the world into **zones**. A zone is a sealed pocket of empty space. The
map is one zone until you seal parts of it off with **zone portals**. A zone portal is a
flat sheet brush with the Portal flag that closes an opening between two rooms.

Zones do two jobs:

- Visibility: the game renders only the zones that are visible through the portals.
- Environment: each zone has a ZoneInfo actor that sets the zone's ambient light, fog,
  reverb and other per-zone values.

A zone portal is closer to a Doom 3 visportal than to a Source areaportal. It is a
requirement for good performance and for per-room lighting settings. See
[08-Zones-and-Portals.md](08-Zones-and-Portals.md).

## Actors are entities

A Hammer entity, a Radiant entity and an Unreal Engine 4/5 actor are all the same idea as
an **actor** in the BioShock editor. An actor is an object placed in the level with a class and a
set of properties.

Differences from Hammer:

- The Properties window (F4) shows the properties in categories. There is no SmartEdit
  toggle. Every property is a typed field.
- An actor has a `Tag` and an `Event` name. These are the equivalent of targetname and
  target. The game's own trigger and script actors are not covered in this guide.
- Lights, player starts, zone infos, path nodes and static meshes are all actors.

Differences from Unreal Engine 4/5:

- There is no details panel with drag-and-drop components. An actor is one object.
- The transform gizmo moves, rotates and scales selected actors and brushes, in world space
  or in the selected actor's own space (Shift + Space switches between the two).
  Ctrl-drag controls are also available. The Actor Scaling button scales actors and brushes.
  The separate legacy `MODE ACTORSCALE` command has brush-only Ctrl drags.
  See [09-Actors-and-Properties.md](09-Actors-and-Properties.md).

## Packages, groups and object paths

Source stores content in `.vpk` files and loose files. The `.vmf` refers to a material by
its file path. Unreal Engine 4/5 stores one object per `.uasset` file.

The BioShock editor stores content in **packages**. A package is one `.pkg` file that holds many
objects. Objects sit in **groups** inside the package. Every object has a full path:

```
Package.Group.Object
Gen_Decor.Furniture.Bench_01
```

Two kinds of package matter to a mapper:

| Kind | Where | Contents |
|---|---|---|
| Content library | `<SDK folder>\Content\*.pkg` | Textures, materials, static meshes, sounds and effects, decooked from your own game install |
| Script packages | `<SDK folder>\System\*.u` | The game's classes (`ShockGame.u`, `Engine.u`, ...) |

A map refers to a library object by its path. A saved map does not copy the object. A baked
map does. See [10-Static-Meshes.md](10-Static-Meshes.md) and
[13-Materials-and-Textures.md](13-Materials-and-Textures.md).

## Textures, materials and shaders

In Source a `.vmt` material names a `.vtf` texture and a shader. In Unreal Engine 4/5 a
material is a node graph and a texture is a separate asset.

The BioShock editor has three layers:

| Layer | Class | Meaning |
|---|---|---|
| Texture | `Texture` | One image with mipmaps |
| Material | `Material` and its subclasses | Anything you can apply to a surface |
| Shader | `Shader` | The main lit material. It has slots for Diffuse, NormalMap, Specular, Emissive and Opacity textures and the values that control them. |

A `Texture` is also a `Material`. You can apply a bare texture to a surface. BioShock's
surfaces almost always use a `Shader` with a diffuse, a normal map and a specular map. The
shader is the closest match to a `.vmt` with `$bumpmap` and `$envmapmask`, or to a simple
Unreal Engine 4 material. There is no node graph. See
[13-Materials-and-Textures.md](13-Materials-and-Textures.md).

## Static meshes carry the detail

A Radiant map is mostly brushes. A Source map is brushes plus props. A BioShock map is a
simple BSP shell with hundreds of **static meshes** placed inside it. Walls, trims,
archways, furniture and machines are all static meshes from the content library.

Rules that differ from Source props:

- Every library static mesh has its own collision shape. You do not set collision per
  placement.
- A static mesh that you create in the BioShock editor has no collision shape. The game crashes if
  such a mesh is in a baked map. Use library meshes. See
  [10-Static-Meshes.md](10-Static-Meshes.md).

## Lighting is baked, but the colour is live

vrad bakes a lightmap with bounce light. Unreal Engine 4 Lightmass bakes direct and
indirect light into lightmaps.

The BioShock editor's lighting build bakes only the **direct visibility** of each light: how much of
each light reaches each surface point, after distance falloff, spot cone and shadows. The
game applies the light's colour and brightness, the surface normal map and the specular
response at run time.

Consequences:

- A change to LightColor or LightBrightness does not need a new lighting build.
- A change to a light's position, radius, cone or the geometry needs a new lighting build.
- There is no bounce light. Fill light comes from the zone's ambient values and from extra
  lights that you place.
- Each static mesh actor takes up to MaxLightsStatic lights. The default is 3. You can set
  a different value on each actor. BSP surfaces have no cap.

See [14-Lighting.md](14-Lighting.md).

## Units, scale and rotation

| Item | Value |
|---|---|
| Unit | 1 unit. The grid, the Properties window and the builder brush use units. |
| Player collision cylinder | Radius 34 units, half-height 68 units (136 units tall). Crouch half-height 40. |
| Door class collision half-height | 80 units (160 units tall) |
| Rotation | 65536 per full turn. 16384 = 90 degrees. The Properties window shows these integers. |
| Axes | X and Y are horizontal. Z is up. |

Stock Unreal content uses about 16 units per foot. BioShock's content is larger than that.
Use the player cylinder and the content library's doors as your scale reference, not a
conversion factor.

The rotation fields are Pitch, Yaw and Roll. Yaw turns around Z. Pitch turns around Y. Roll
turns around X.

## Grid

The BioShock editor snaps to a grid the same way Hammer and Radiant do. The grid size is in the
right-click menu (Grid submenu, 1 to 4096 units) and in the bottom bar. Keep brushes on a grid
of at least 4 units. Off-grid brushes cause BSP errors. See
[07-BSP-Geometry.md](07-BSP-Geometry.md).

## Comparison table

| Concept | Hammer | Radiant | UE4/UE5 | The BioShock editor |
|---|---|---|---|---|
| World start state | Empty, additive | Empty, additive | Empty | Solid, subtractive |
| Basic shape tool | Block tool, new solid each time | Drag creates a brush | Geometry brush (rare) | One builder brush, re-used |
| Brush order | Not used | Not used | Additive/subtractive order | List order, To First / To Last |
| Level container | `.vmf` source, `.bsp` output | `.map` source, `.bsp` output | `.umap` | `.unr` saved map and `.bsm` baked map |
| Content container | `.vpk`, loose files | `.pk3` | `.uasset` per object | `.pkg` package with groups |
| Object reference | File path | File path | Asset path | `Package.Group.Object` |
| Material | `.vmt` | `.shader` script | Node graph | `Shader` object with texture slots |
| Placed object | Entity | Entity | Actor | Actor |
| Detail geometry | Props, func_detail | Brushes, models | Static meshes | Static meshes from the library |
| Visibility | visleafs, hint, areaportal | portals, visportals | Occlusion culling | Zones and zone portals |
| Per-room environment | env_fog_controller, soundscape | worldspawn keys | Post-process volumes | ZoneInfo actor |
| Geometry compile | vbsp | q3map2 -bsp | none | Geometry build |
| Lighting compile | vrad | q3map2 -light | Lightmass | Lighting build (direct light only) |
| Navigation | nav mesh | AAS | NavMesh | PathNodes and path build |
| Events and logic | Entity I/O: outputs wired to inputs | target and targetname keys | Blueprint or Kismet graph | Script actor with an ordered list of actions; wired by Labels and messages |
| Packaging | pakrat, `.bsp` embeds | `.pk3` | Cook | Bake |
| Transform widget | Yes | No | Gizmo | Gizmo (move, rotate, scale; world or local space); Ctrl + mouse buttons also work |
| Units | Hammer units | Quake units | Centimetres | Units |

## Background: why Unreal's BSP is not Quake's BSP

The subtractive world and the live CSG rebuild are not design choices that anyone made on
purpose. They come from a misunderstanding.

Quake's editor on the NeXT let a designer place brushes, but the BSP tree was built in a
separate offline compile step, the ancestor of `qbsp`, `vbsp` and `q3map2`. Tim Sweeney
had read about that editor and seen screenshots of it, but had never used it. He assumed
that it rebuilt the BSP tree in real time as brushes moved, and he set out to match that.
So the first UnrealEd rebuilt the whole tree live, and a solid world that you carve rooms
out of was the natural way to make that work. In his words:

> The one real piece of wizardry in the early Unreal Editor, before I implemented lighting
> and things like that, was the real-time BSP tree creation. The idea is that you can
> reposition brushes in 3-D space, and then all the BSP work is updated completely in
> real-time. [...] I created two toruses that were interlocked, and subtracted them from
> the world, and [James Schmalz] said "Whoa, no way? That's awesome!"
>
> Carmack had written this really advanced editor on the NeXT. I'd read all about it and I
> had seen screenshots of it, but I never actually used it. At the time, I thought to
> myself "Holy shit, Carmack wrote a real-time BSP editor!" What I didn't realize was that
> it wasn't actually real-time, there was this re-build process and all this other offline
> stuff. I didn't know that, and so I thought I had to create a completely real-time thing,
> and so I did. [...] A lot of the features in Unreal arose from fallacies of what I
> misperceived other people did.
>
> Tim Sweeney, interviewed by David Lightbown for gamedeveloper.com about the creation
> of the first Unreal Editor

Two things from that story still shape the BioShock editor:

- The world is solid and you subtract from it. The builder brush is the carving tool, and
  brush order matters because each brush is applied to the result of the ones before it.
- The BSP is still rebuilt as one operation by the geometry build, not compiled by a
  separate tool. The editor no longer rebuilds on every brush move, but the geometry build
  is fast enough to run after every few brushes, and this guide tells you to do so.

James Schmalz, whom Sweeney showed the interlocked toruses to, had been building his BSP
trees by hand before that.

## Related documents

- [01-Introduction.md](01-Introduction.md)
- [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md)
- [07-BSP-Geometry.md](07-BSP-Geometry.md)
- [08-Zones-and-Portals.md](08-Zones-and-Portals.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [10-Static-Meshes.md](10-Static-Meshes.md)
- [13-Materials-and-Textures.md](13-Materials-and-Textures.md)
- [14-Lighting.md](14-Lighting.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
