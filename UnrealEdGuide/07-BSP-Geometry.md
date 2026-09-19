# BSP Geometry

This document tells you how to build the BSP shell of a map: the builder brush, the brush
builders, CSG operations, brush editing tools, surface properties and the geometry build.
Every mapper needs it. Read [05-Concepts-for-Hammer-Radiant-and-UE-Users.md](05-Concepts-for-Hammer-Radiant-and-UE-Users.md)
first if you come from another editor.

## Before you start

- You know the viewports and the camera controls. See
  [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md).
- You know how to select actors and move them. See
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).

## The builder brush

The builder brush is the red wireframe shape in every viewport. It is a template. It is not
part of the map until you apply a CSG operation with it. There is only one builder brush.

The builder brush is an actor. You select it, move it, rotate it and scale it the same way
as any actor. See [09-Actors-and-Properties.md](09-Actors-and-Properties.md) for the
mouse-button combinations.

**NOTE:** The builder brush keeps its shape and position after a CSG operation. You can
apply it again at once.

## Set the shape with a brush builder

The Builders group of the left button bar holds one button per brush builder.

1. Left-click a builder button. The builder brush takes the builder's shape with the last
   used parameters.
2. Right-click the same button. A dialog opens with the builder's parameters.
3. Set the values. Click Build. The builder brush takes the new shape.

The builders and their parameters:

| Builder | Parameters |
|---|---|
| Cube | Height, Width, Breadth, WallThickness, GroupName, Hollow, Tessellated |
| Cylinder | Height, OuterRadius, InnerRadius, Sides, GroupName, AlignToSide, Hollow |
| Cone | Height, CapHeight, OuterRadius, InnerRadius, Sides, GroupName, AlignToSide, Hollow |
| Sheet | Height, Width, HorizBreaks, VertBreaks, Axis, GroupName |
| Linear Stair | StepLength, StepHeight, StepWidth, NumSteps, AddToFirstStep, GroupName |
| Curved Stair | InnerRadius, StepHeight, StepWidth, AngleOfCurve, NumSteps, AddToFirstStep, CounterClockwise, GroupName |
| Spiral Stair | InnerRadius, StepWidth, StepHeight, StepThickness, NumStepsPer360, NumSteps, SlopedCeiling, SlopedFloor, CounterClockwise, GroupName |
| Tetrahedron | Radius, SphereExtrapolation, GroupName |
| Volumetric | Height, Radius, NumSheets, GroupName |

Notes on the parameters:

- Hollow=True makes a shell. WallThickness controls the thickness of the shell.
  If you subtract a hollow cube from solid world space, only the shell becomes empty space.
  The center stays solid. To make a room, subtract a solid cube with Hollow=False.
  If you add a hollow cube inside a room, the shell becomes solid walls.
- Tessellated splits each cube face into two triangles.
- Sides on a cylinder or cone sets the number of flat faces. Use 8, 12, 16 or 24.
- AlignToSide rotates a cylinder or cone so that a flat side faces the X axis.
- GroupName sets the surface group that every face of the brush belongs to. Groups let you
  select surfaces by group later.
- Sheet makes a flat, two-sided polygon with no thickness. Use it for zone portals and for
  glass-like planes. Axis picks the plane the sheet lies in.
- Volumetric makes crossed sheets. Use it for light shafts and other card effects.
- Tetrahedron with SphereExtrapolation above 0 approximates a sphere.
- Terrain Builder exists in the button bar. Terrain is not tested with BioShock content.

## Apply a CSG operation

With the builder brush in place, apply one operation:

| Operation | Menu and key | Button bar | Result |
|---|---|---|---|
| Add | Brush > Add, Ctrl+A | CSG group, Add | A solid brush appears with the builder's shape |
| Subtract | Brush > Subtract, Ctrl+S | CSG group, Subtract | A hollow space appears with the builder's shape |
| Intersect | Brush > Intersect, Ctrl+N | CSG group, Intersect | The builder brush shrinks to the part of itself inside solid space |
| Deintersect | Brush > Deintersect, Ctrl+D | CSG group, Deintersect | The builder brush shrinks to the part of itself inside empty space |
| Add Special | Brush > Add Special... | CSG group, Add Special Brush | A brush with the flags you choose in the dialog |
| Add Mover | Brush > Add Mover | CSG group, Add Mover Brush | A mover actor with the builder's shape |
| Add Antiportal | Brush > Add Antiportal | CSG group, Add Antiportal | An antiportal actor with the builder's shape |
| Add Volume | Button bar, Volume | Volume | A volume actor with the builder's shape. Right-click the button for the list of volume classes. |

The Add and Subtract results are brushes in the map. They show as blue (add) and yellow
(subtract) wireframes. The viewport does not show the result of the operation until you
run a geometry build. Rebuild often.

**NOTE:** Ctrl+S is Subtract. It is not Save. Save is Ctrl+L.

Intersect and Deintersect do not change the map. They change the builder brush. Use them
to cut a builder brush to fit a space before you add it, or to copy the shape of existing
geometry into the builder brush.

### The Add Special dialog

Add Special creates a brush with a preset of flags. The Prefabs list offers:

| Preset | Use |
|---|---|
| Invisible Collision Hull | An invisible brush that blocks movement |
| Zone Portal | A zone portal. See [08-Zones-and-Portals.md](08-Zones-and-Portals.md). |
| Anti-Portal | An antiportal |
| Regular Brush | A brush with the flags you set by hand |

The check boxes and radio buttons set the flags: Portal, Invisible, Two Sided, Mirror,
Anti-Portal, and Solid / Semi-Solid / Non-Solid. Click OK to add the brush. The dialog stays
open, so you can add more than one brush.

## Brush types

Every added brush has a type. Set it when you add the brush with Add Special, or later with
right-click > Type.

| Type | Cuts the BSP | Blocks movement | Use |
|---|---|---|---|
| Solid | Yes | Yes | Walls, floors, pillars, everything structural |
| Semisolid | No | Yes | Detail that must not cut the surrounding surfaces: trim, small steps, supports |
| Nonsolid | No | No | Decorative sheets and shapes with no collision |

A semisolid brush gives you detail without extra BSP cuts on the walls around it. It must
not intersect another brush. Overlapping semisolids cause holes and invisible collision.

Subtracted brushes are always solid.

## Brush order

The map applies its brushes in list order. Later brushes cut earlier ones. Two commands on
the right-click menu change the order of a selected brush:

- Order > To First: the brush becomes the first in the list.
- Order > To Last: the brush becomes the last in the list.
- Order > Swap Order: two selected brushes exchange places.

A typical use: a subtract that must cut through an add that was placed later. Move the add
to first, or the subtract to last. Run a geometry build after any order change.

## Move, rotate and scale a brush

A brush is an actor. Select it and use the actor controls in
[09-Actors-and-Properties.md](09-Actors-and-Properties.md). Extra commands for brushes:

- Brush > Scale...: scale the selected brush by a factor per axis.
- Right-click > Transform > Mirror About X / Y / Z: mirror the brush.
- Right-click > Transform > Transform Permanently: write the current rotation and scale
  into the vertices. Do this before a geometry build if a brush was rotated or scaled, so
  that the BSP cut is exact.
- Right-click > Reset > Rotation / Scaling / Pivot / All: undo transforms.
- Right-click > Pivot > Place Pivot Here: set the rotation and scale pivot at the click
  point. Snapped Here snaps the pivot to the grid.
- Click a brush vertex in a viewport to set the pivot at that vertex. Right-click a vertex
  to set the pivot at the snapped vertex position.

## Brush clipping

Brush clipping cuts a brush along a plane.

1. Select the brush to clip.
2. Click Brush Clipping in the Modes group of the button bar.
3. Hold Ctrl and right-click in a viewport to place a clip marker. Place two markers in an
   orthographic viewport for a vertical cut. Place three markers to define any plane.
4. Choose the operation:

| Command | Menu | Result |
|---|---|---|
| Clip | Brush > Brush Clip > Clip | Keeps the part of the brush on the side of the plane's normal, removes the rest |
| Split | Brush > Brush Clip > Split | Cuts the brush into two brushes |
| Flip Normal | Brush > Brush Clip > Flip Normal | Reverses the side that Clip keeps |
| Delete All Clip Markers | Brush > Brush Clip > Delete All Clip Markers | Removes the markers |

The same commands are in the Clipping group of the button bar. You can clip the builder
brush or any brush in the map.

## Vertex editing

Vertex editing moves single vertices of a brush.

1. Select the brush.
2. Click Vertex Editing in the Modes group of the button bar.
3. Click a vertex to select it. Hold Ctrl and click to add more vertices to the selection.
4. Hold Ctrl and drag with the mouse buttons to move the selected vertices. The button
   combinations are the same as for actors: in a perspective viewport the left button moves
   along X, the right button along Y, both buttons along Z. In an orthographic viewport the
   left button moves along the screen's horizontal axis and the right button along the
   vertical axis.
5. Right-click a selected vertex to snap the selected vertices to the grid.

**CAUTION:** A brush with a non-planar face or a face whose vertices cross each other
gives BSP errors. Keep each face flat. Keep vertices on the grid.

In Camera Movement mode, hold Alt and drag on a vertex to move it directly.

## Face dragging

Face Drag in the Modes group of the button bar moves one face of a brush. Select the brush,
select the mode, then hold Ctrl and drag a face.

## The 2D Shape Editor

Tools > 2D Shape Editor opens a window where you draw a flat outline and turn it into a
brush shape.

1. Draw the outline. Edit > Insert New Shape adds a shape. Edit > Split Side (Ctrl+I) adds a
   vertex to a side. Drag vertices to move them. Edit > Delete removes a vertex.
2. Use the Grid submenu for snap sizes 1 to 64. A side can be Linear or Bezier (Segment
   submenu). Detail Level sets the number of segments per Bezier side.
3. Use Image > Open From Disk or Image > Get From Current Texture to show a reference
   image behind the outline.
4. Use Edit > Scale Up / Scale Down, Rotate 45 / Rotate 90, Flip Horizontally /
   Flip Vertically to adjust the outline.
5. Choose a process:

| Process | Result |
|---|---|
| Sheet | A flat sheet with the outline |
| Extrude... | The outline extruded to a depth you enter |
| Extrude to Point... | The outline extruded to a single point |
| Extrude to Bevel... | The outline extruded with a bevel |
| Revolve... | The outline revolved around an axis, for arches and columns |

The result becomes the builder brush. File > Save stores the outline as a file that you can
open again.

## Save, open, import and export brushes

- Brush > Save Brush As...: writes the builder brush to a `.u3d` file.
- Brush > Open Brush (Ctrl+B): reads a `.u3d` file into the builder brush.
- Brush > Import...: reads a brush from `.t3d`, `.dxf`, `.asc` or `.ase`.
- Brush > Export...: writes the builder brush as `.t3d`.
- Brush > Batch Import...: imports several brush files at once.

Use `.t3d` to move brushes between maps or to hand-edit a brush as text.

## Surface properties

A surface is one polygon of the BSP after a geometry build. Click a surface in a viewport to
select it. Ctrl+click adds to the selection. Selected surfaces show a blue-purple tint.

Open the Surface Properties window with F5 or right-click > Surface Properties. The window
has four tabs.

### Flags tab

| Flag | Meaning |
|---|---|
| Invisible | The surface does not draw. It still blocks movement. |
| Fake Backdrop | The surface shows the sky zone instead of its own texture. Light rays pass through it. See [08-Zones-and-Portals.md](08-Zones-and-Portals.md). |
| Two Sided | Both sides of the surface draw. Use on sheets. |
| Special Lit | Only lights with bSpecialLit affect this surface. Normal lights ignore it. |
| Unlit | The surface draws at full brightness with no lighting. |
| Portal | The surface is a zone portal. |
| Mirror | The surface reflects the scene. Not tested with BioShock content. |
| Anti-Portal | The surface is an antiportal. |

### Pan/Rot/Scale tab

| Control | Effect |
|---|---|
| Pan U / V buttons (1, 4, 16, 64) | Moves the texture by that many texels along U or V. Hold Shift to pan in the opposite direction. |
| Rotation 45 / 90 | Rotates the texture. Hold Shift to rotate the other way. |
| Flip U / Flip V | Mirrors the texture along one axis |
| Scaling, Simple | One scale factor for both axes, from 0.0625 to 16. Click Apply. |
| Scaling, U and V | Separate factors. Click Apply. Relative? applies the factor on top of the current scale. |
| Light map | The lightmap texel size in units, from 1 to 256. Default 32. See below. |

### Alignment tab

Select an aligner in the list and click Align:

| Aligner | Effect |
|---|---|
| Default | Resets the texture axes to the world axes |
| Planar | Projects the texture along the surface normal's main axis. Options let you pick the plane. |
| Box | Projects the texture from the six world axes, useful for a whole brush |
| Face | Aligns the texture to the surface's own edges |

The right-click menu has the same aligners under Alignment, plus Planar Walls and Planar
Floor shortcuts.

### Stats tab

Shows the surface's brush, texture and zone.

### Light map scale

The Light map value on the Pan/Rot/Scale tab sets the size of one lightmap texel on the
surface, in units. The default is 32. A smaller value gives sharper shadows and larger
lightmap data. A larger value gives softer, cheaper lighting.

The lightmap of one surface is at most 512 texels along each axis. A surface 4096 units
long with a Light map value of 4 needs 1024 texels and is scaled down to 512. Keep the
value at 16 or above on large surfaces. See [14-Lighting.md](14-Lighting.md).

## Texture the surfaces

The current texture is the one selected in the Texture Browser.

- Select surfaces and click Apply Texture on the right-click menu, or hold Shift and
  left-click a surface to apply the current texture to all selected surfaces.
- Hold Alt and left-click a surface to apply the current texture to that surface only.
- Hold Alt and right-click a surface to make its texture the current texture.
- Hold Alt and Ctrl and left-click a surface to apply the current texture with the pan,
  rotation and scale of the surface you last picked with Alt and right-click.
- Hold Ctrl and Shift and left-click a surface to select the brush that owns it.

The Texture Pan and Texture Rotate modes in the button bar let you pan and rotate the
textures of the selected surfaces by dragging with Ctrl held.

See [13-Materials-and-Textures.md](13-Materials-and-Textures.md) for the material side.

## Select surfaces

The Select Surfaces submenu (Edit menu, or right-click a surface) has these commands:

| Command | Key | Selects |
|---|---|---|
| Matching Groups | Shift+G | Surfaces in the same group |
| Matching Items | Shift+I | Surfaces with the same item name |
| Matching Brush | Shift+B | Surfaces of the same brush |
| Matching Texture | Shift+T | Surfaces with the same material |
| All Adjacents | Shift+J | Surfaces that touch the selection |
| Adjacent Coplanars | Shift+C | Touching surfaces in the same plane |
| Adjacent Walls | Shift+W | Touching vertical surfaces |
| Adjacent Floors/Ceilings | Shift+F | Touching horizontal surfaces |
| Adjacent Slants | Shift+S | Touching sloped surfaces |
| Reverse | Shift+Q | Inverts the selection |
| Memorize Set | Shift+M | Stores the selection |
| Recall Memory | Shift+R | Restores the stored selection |
| Or / And / Xor With Memory | Shift+O / U / X | Combines the selection with the stored set |

Edit > Select All Surfaces (Shift+S) and Select None (Shift+Z) also apply.

## Extrude and bevel

Right-click a surface > Extrude > 16, 32, 64, 128, 256 or Custom pushes the surface out by
that many units and makes a new brush from the result. Bevel does the same with a slope.
Both put the result in the builder brush. Add it to the map with Brush > Add.

## Convert between brushes and static meshes

- Right-click a brush > Convert > To Static Mesh: creates a static mesh from the brush and
  places a static mesh actor. A dialog asks for the package, group and name.
- Right-click a static mesh actor > Convert > To Brush: makes the builder brush take the
  mesh's shape.
- Right-click a brush > Save Brush As Collision: stores the brush as the collision shape of
  the selected static mesh, for the BioShock editor's own collision.

**CAUTION:** A static mesh made with Convert > To Static Mesh has no game collision
shape. The game crashes when it loads a baked map that places such a mesh.
Use these meshes for editor work only. For playable maps, use meshes from the content library.
See [Collision in the game](10-Static-Meshes.md#collision-in-the-game).

## The geometry build

The geometry build turns the brush list into the BSP. Run it after every brush change.

- Build > Rebuild Geometry Only runs the build with the current options.
- Build > Build Options... opens the Options tab of the build window.

The Options tab:

| Option | Meaning |
|---|---|
| Geometry | Runs the CSG pass. Only Rebuild Visible Actors? limits it to visible brushes. |
| BSP | Runs the BSP pass. Optimization: Lame, Good or Optimal. Optimal gives the fewest cuts. Keep it on Optimal; the build takes seconds on a current computer. |
| Minimize Cuts / Balance Tree slider | Trades fewer surface cuts against a balanced tree. The default is 15. |
| Ignore Portals / Portals Cut All slider | Sets how much zone portals influence the cuts. The default is 70. |
| Lighting | Runs the lighting build as part of Build. Format, Dither? and Save as BMP? are legacy controls with no effect on BioShock lighting. |
| Define Paths | Runs the path build as part of Build. Create New Path Network and Only Build Changed are the path commands. |
| Report Errors? | Opens the map check window after the build |
| Configuration, Save, Delete | Stores option sets by name |
| Build | Runs every checked step |

The Stats tab shows brush, zone, polygon, node and light counts.

The geometry build produces:

- The BSP tree with its surfaces and nodes.
- The zones and the portal links between them. See [08-Zones-and-Portals.md](08-Zones-and-Portals.md).
- The leaf data that the bake needs.

**NOTE:** Run a geometry build before the first bake of a new map. A bake without it lacks
the zone and leaf data the game needs. See
[18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).

## Common BSP problems

| Symptom | Cause | Correction |
|---|---|---|
| A hole in a wall shows solid grey or other rooms | Overlapping semisolid or nonsolid brushes, or a brush with a bad face | Move the semisolids apart. Make the brush solid. Run Tools > Check Map for Errors. |
| Invisible wall where nothing is drawn | Overlapping semisolids | Move the semisolids apart or make one of them solid |
| Many thin slivers on a wall (BSP Cuts view mode) | Many solid brushes cut the wall | Make small detail brushes semisolid, or replace them with static meshes. Set Optimization to Optimal. |
| A brush is missing after the build | The brush is fully inside solid space, or a later subtract removed it | Check brush order. Use Zone/Portal and BSP Cuts view modes. |
| Brush vertices drift off the grid | The brush was rotated or scaled | Right-click > Transform > Transform Permanently, then snap the vertices |
| Player falls through a floor | The floor is nonsolid, or the floor is a subtract with no solid below it | Set the brush to solid |
| Zones did not form | The portal sheet does not close the opening exactly | Fix the sheet. See [08-Zones-and-Portals.md](08-Zones-and-Portals.md). |

Tools > Check Map for Errors lists brushes and actors with problems. Double-click a line to
select the actor.

## Tips

- Keep the BSP shell simple: rooms, corridors and floor levels. Put stairs, trim, arches,
  pipes and furniture in as static meshes from the content library. See
  [06-BioShock-Mapping-Guidelines.md](06-BioShock-Mapping-Guidelines.md) for the sizes the
  kit expects.
- Keep every brush on a grid of 4 units or more. Use 16 or 32 for rooms.
- Set Hollow=False for room and wall brushes. Subtract solid cubes to make rooms.
  Add solid cubes to make walls between rooms.
- Set the Sides of cylinders to a power of two or a multiple of 4.
- Run the geometry build after every few brushes. Errors are easier to find early.
- Use the BSP Cuts view mode to see how the brushes cut each other. Fewer cuts render
  faster and light better.

## Related documents

- [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md)
- [05-Concepts-for-Hammer-Radiant-and-UE-Users.md](05-Concepts-for-Hammer-Radiant-and-UE-Users.md)
- [08-Zones-and-Portals.md](08-Zones-and-Portals.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [10-Static-Meshes.md](10-Static-Meshes.md)
- [13-Materials-and-Textures.md](13-Materials-and-Textures.md)
- [14-Lighting.md](14-Lighting.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [38-Keyboard-and-Mouse-Reference.md](38-Keyboard-and-Mouse-Reference.md)
