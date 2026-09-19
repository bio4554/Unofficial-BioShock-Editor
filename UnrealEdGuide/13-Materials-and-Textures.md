# Materials and Textures

This document explains how the BioShock editor handles materials and textures for BioShock content.
It covers the Texture browser, how to apply a material to a surface or a static mesh actor,
the material classes the game uses, the properties of the Shader material, how to create
and import your own materials and textures, and how the bulk content works. Read it if you
texture geometry, build your own materials, or bring your own textures into a map.

## Before you start

- The content library is installed under the SDK folder as `Content\*.pkg`. See
  [02-Setup.md](02-Setup.md).
- `BulkContentPath` under `[Engine.Engine]` in `System\BioShockSDK.ini` points to your game install's
  `Content\BulkContent` folder. Without it, retail textures show no pixels.
- You know how to select surfaces and actors. See
  [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md) and
  [07-BSP-Geometry.md](07-BSP-Geometry.md).

## Three terms: texture, material, shader

The BioShock editor uses three related words. Keep them apart.

| Term | Meaning | Hammer or Radiant equivalent |
|---|---|---|
| Texture | An image with mip levels. It has no lighting behaviour of its own. | A `.vtf` or `.tga` image file |
| Material | An object that tells the renderer how to draw a surface. A material can be a texture, a shader, or a modifier that wraps another material. | A `.vmt` file or a Radiant shader script |
| Shader | The main lit material class of the game. A shader combines a diffuse texture, a normal map, specular inputs, an emissive input and a blend mode. | A `VertexLitGeneric` `.vmt` |

You apply a **material** to a surface or to a static mesh actor. A plain texture is also a
material, so you can apply a texture directly. The game's own content almost always applies
a Shader, not a bare texture.

## Where materials come from

Every material lives in a package. A full material name has three parts:
`Package.Group.Name`, for example `Gen_FloorsWallsAndCeilings.Marble.MarbleFloor_Shader`.
Some materials have no group and use `Package.Name`.

- **The content library** (`Content\*.pkg`) holds the game's materials. Package names follow
  the game's naming scheme. `Gen_*` packages hold generic art that every district uses.
  A district prefix marks district-specific art: `Wel` and `Bat` (Welcome to Rapture),
  `Med` (Medical Pavilion), `Fis` (Neptune's Bounty), `Hyd` and `Mar` (Arcadia and the
  Farmer's Market), `Rec` (Fort Frolic), `Eng` (Hephaestus), `Res` (Apollo Square),
  `Sci` (Point Prometheus), `Gau` (Proving Grounds).
- **Retail maps** hold materials of their own. When you open a retail map, its map-owned
  materials appear in the Texture browser under the map's package name.
- **MyLevel** is the special package that belongs to the open map. Materials that you create
  in MyLevel save inside the map file and travel with it.
- **Your own packages**. You can create a new package name in the New Material or Import
  dialog. The package is written to `Content\` when you save it from the Texture browser.

Useful library packages for a mapper:

| Package | Contents |
|---|---|
| `Gen_FloorsWallsAndCeilings.pkg` | Tiling surface materials: marble, concrete, wood floor, tin ceiling, lounge walls |
| `Gen_Glass.pkg` | Window glass, condensation, ripples, breakable glass, tunnel glass |
| `Gen_Water.pkg` | Water shaders and panners: cascades, trickles, puddles, floor flow, ocean, caustics |
| `Gen_WaterSurface.pkg` | Water surface shaders: calm, rough and flowing, with and without reflection |
| `Gen_Signs.pkg` | Neon storefronts, bulletin boards, arrows, scrolling sign panners |
| `Gen_Lights.pkg` | Light fixture meshes, light beam shaders, flicker colour cycles |

**CAUTION:** Do not save over a content library package or a game package. Put your own
materials in MyLevel or in a new package with a unique name.

## The Texture browser

Open the Texture browser with View > Show Texture Browser. The browser docks into the
Master Browser by default. Use View > Docked in the browser to float it.

The browser has these controls:

| Control | Function |
|---|---|
| Package list | Selects the package to show. Only loaded packages appear. |
| Group list | Selects one group in the package. |
| All button | Shows every group of the package at once. |
| Material grid | Shows a thumbnail per material. The selected material has a highlight. |
| File menu | New, Open, Save, Import, Export. |
| Edit menu | Properties, Duplicate, Rename, Remove from level, Cull Unused Textures, Load Entire Package, Replace Textures, Prev Group, Next Group, Delete. |
| View menu | Docked, and the thumbnail size: 200%, 100%, 50%, 25%, or fixed 32 to 512 pixels. |
| Tools > Compress | Compresses the selected texture to DXT1, DXT3 or DXT5. |
| Filter menu | Shows or hides material classes: Textures, Shaders, Modifiers, Combiners, Final Blends. |
| "In Use" Filter menu | Limits the Used page to materials on Actors, Sprites, Brushes, Static Meshes, Terrain or Emitters. |

The browser has three pages. The Materials page shows the selected package. The Used page
shows the materials that the open map uses. The MRU page shows the materials that you
selected most recently.

### Open a package

1. In the Texture browser, click File > Open.
2. Select a `.pkg` file in `Content\`. The dialog filters for `*.pkg`.
3. Click Open. The package appears in the Package list.

Opening a package loads only its header and the objects you look at. Use
Edit > Load Entire Package to load every object of the package.

### The context menu on a material

Right-click a thumbnail to open the context menu. The menu has Properties, Duplicate,
Rename, Remove from level, Detail Hack, Compress, Export to File and Delete.

- **Properties** opens the material's Properties window. See "Edit a material" below.
- **Remove from level** clears the material from every surface that uses it.
- **Delete** removes the material from its package. The BioShock editor refuses when the map still
  uses the material.
- **Detail Hack** is a legacy stock function. Do not use it.

**CAUTION:** Delete and Rename change the loaded package only. The change reaches disk when
you save the package with File > Save in the browser.

## Apply a material to surfaces

The Texture browser keeps one **current material**. Clicking a thumbnail makes it current.

1. Select the surfaces in a viewport. Click a surface to select it. Ctrl+click adds
   surfaces to the selection.
2. In the Texture browser, click the material.

The click applies the current material to every selected surface at once. You can also
apply the current material with these mouse actions in a viewport:

| Action | Result |
|---|---|
| Click a thumbnail in the browser | Applies the material to all selected surfaces |
| Shift + left click on a surface | Applies the current material to all selected surfaces |
| Alt + left click on a surface | Applies the current material to that one surface |
| Ctrl + Alt + left click on a surface | Applies the current material and copies the texture alignment of the last picked surface |
| Alt + right click on a surface | Picks the surface's material and makes it current |
| Surface right-click menu > Apply Texture | Applies the current material to the selected surfaces |

Alignment, pan, rotation and scale of the material on a surface are in the Surface
Properties window (F5). See [07-BSP-Geometry.md](07-BSP-Geometry.md).

## Apply materials to a static mesh actor

A static mesh carries its own materials, one per section. To override them on one placed
actor:

1. Select the static mesh actor.
2. Press F4 to open the Properties window.
3. Expand the Display category.
4. Expand the Skins array. Element 0 overrides the first material section, element 1 the
   second, and so on.
5. Select the material in the Texture browser.
6. Click the Use button on the array element.

An empty Skins element keeps the mesh's own material for that section.

## The material class tree

The game's material classes form a tree. The Filter menu of the Texture browser groups them
in the same way.

| Class | Branch | What it does |
|---|---|---|
| Texture | Bitmap | A 2D image with mip levels |
| Cubemap | Bitmap | Six textures that form a cube for reflections |
| Shader | Rendered | The standard lit surface material. Use it for walls, floors, props |
| FacingShader | Rendered | Adds a rim term that depends on the view angle. The game uses it on characters |
| FluidShader, FluidSurfaceShader, PondScumFluidShader | Rendered | Water. See [15-Water-and-Fluids.md](15-Water-and-Fluids.md) |
| PlantShader | Rendered | Foliage with translucency |
| LayeredShader | Rendered | Blends two material layers with a mask |
| WindowShader | Rendered | Glass with distortion and reflection |
| LightBeamShader | Rendered | Volumetric light shaft meshes |
| CausticShader | Rendered | Animated caustic light, projected onto floors under water |
| ShimmerShader, TargetedShader, BlenderShader, ScriptSideShader, RippleShader | Rendered | Special effect materials |
| TerrainMaterial | Rendered | Stock terrain layers. Untested with BioShock content |
| MaterialSwitch | Modifier | Holds an array of materials and shows one of them |
| MaterialSequence | Modifier | Plays a timed sequence of materials |
| ColorModifier | Modifier | Tints another material |
| FinalBlend | Modifier | Stock frame-buffer blend wrapper |

A Modifier wraps another material through its Material property. The wrapped material can be
a texture, a shader, or another modifier.

**NOTE:** The game's content uses Shader and the specialised shaders, MaterialSwitch,
MaterialSequence and its own animator objects. It does not use the stock Combiner or
FinalBlend classes. Those classes exist in the BioShock editor, but the SDK's renderer is
untested with them. Use a Shader with an OutputBlending mode instead of a FinalBlend.

## Shader properties

Open a shader's Properties window from the Texture browser (Edit > Properties, or
right-click > Properties). The properties are grouped by category.

### Inputs

| Property | Type | Function |
|---|---|---|
| Diffuse | Material | The base colour map |
| DiffuseColor | Color | Tint applied to the diffuse map. Default white |
| NormalMap | Material | Tangent-space normal map. Lighting uses it at render time |
| HeightMap | Mask | Height map for parallax. Used with HeightMapStrength (0 to 0.5) |
| Opacity | Mask | Alpha source for cutouts and blending |
| Emissive | Material | Self-illumination map |
| EmissiveColor, EmissiveBrightness | Color, 0 to 20 | Tint and strength of the emissive term |
| EmissiveMask | Mask | Limits the emissive term to part of the surface |
| Subsurface, SubsurfaceColor2x, SubsurfaceMask | Material, Color, Mask | Subsurface scattering term. The game uses it on flesh and sea life |

### Specular

| Property | Type | Function |
|---|---|---|
| SpecularColor | Color | Tint of the highlight. Default white |
| SpecularBrightness | 0 to 20 | Strength of the highlight |
| SpecularColorMap | Material | Per-pixel specular colour |
| SpecularMask | Mask | Per-pixel specular strength |
| Glossiness | 0 to 512 | Highlight tightness. High values give a small sharp highlight |
| GlossinessMask | Mask | Per-pixel glossiness |
| UseSpecularCubemaps | bool | Adds a cubemap reflection to the specular term |
| SpecularCubeMapBrightness | 0 to 1 | Strength of the cubemap reflection |

### Reflection

| Property | Type | Function |
|---|---|---|
| ReflectionCubemap | Cubemap | An explicit cubemap for mirror-like reflection |
| ReflectionBrightness | byte | Strength of the reflection. Default 255 |
| ReflectionMask | Mask | Per-pixel reflection strength |

### Blending

| Property | Type | Function |
|---|---|---|
| OutputBlending | enum | How the surface blends with what is behind it. See "Blending and transparency" |
| Masked | bool | Enables alpha-test cutouts from the Opacity mask |
| TwoSided | bool | Draws both faces. Use for glass panes, leaves, thin walls |
| ForceTransparentSorting | bool | Forces the surface into the sorted translucent pass |
| DistortionStrength | byte | Refraction strength behind the surface. It needs a normal map to show |

### Clipping

| Property | Type | Function |
|---|---|---|
| ClipMask | Mask | An alpha-clip source separate from Opacity |
| MinAlphaClipValue, MaxAlphaClipValue | 0 to 1 | The alpha range that stays visible |

### Animation

| Property | Type | Function |
|---|---|---|
| DiffuseColorAnimator, SelfIllumColorAnimator | Color animator | Animates a colour over time |
| DiffuseTextureAnimator, OpacityTextureAnimator, SelfIllumTextureAnimator | Texture animator | Pans, rotates or scales the texture coordinates over time |

**NOTE:** The animator objects that the game's materials use (TexturePanner, TextureRotator,
TextureScalar, ColorCycle) appear in the material tree of the Properties window. Their
values are not editable in the BioShock editor. To get a panning or flickering surface, use a
library material that already has the animator you need.

### Material base properties

Every material, not only a shader, has these properties.

| Property | Category | Function |
|---|---|---|
| MaterialVisualType | MaterialType | The physical surface type. The game selects footstep sounds, impact effects and decals from it |
| AcceptProjectors | Projectors | Lets projector decals draw on the surface. Default on |
| FallbackMaterial | (none) | Stock fallback. Leave empty |
| SurfaceType | (none) | Stock surface type. Not verified: the game reads MaterialVisualType, not this property |

MaterialVisualType values: Default, Concrete, Stone, ThinGlass, ThickGlass, ThinCloth,
ThickCloth, ThinMetal, ThickMetal, Wood, Plastic, Cardboard, Plaster, Water, Flesh, Carpet,
Dirt, WaterPipe, Plant, FleshAlternate, OpaqueGlass, ElectricalGlass, ElectricalMetal,
ExteriorGlass, Mud, BreakableGlass, Paper, Trash.

## Mask properties

A property of type Mask has two fields:

| Field | Function |
|---|---|
| Material | The texture to sample |
| Channel | Which channel to read: MC_A (alpha), MC_R, MC_G or MC_B |

One greyscale texture can drive several masks. For example, put the specular strength in
the red channel and the glossiness in the green channel of one texture, then point both
SpecularMask and GlossinessMask at it with different channels.

## Blending and transparency

OutputBlending selects the blend mode. The game's content uses the first four values.

| Value | Result | Use for |
|---|---|---|
| FB_Overwrite | Opaque. The Opacity mask is ignored unless Masked is on | Walls, floors, props |
| FB_AlphaBlend | Blends by the Opacity mask. Drawn in the sorted translucent pass | Glass, dirty windows |
| FB_Translucent | Additive blend. Drawn in the sorted translucent pass | Light beams, glows |
| FB_Modulate | Multiplies the surface behind it. Drawn in the sorted translucent pass | Stains, shadow decals |

For a cutout (a fence, a grate, leaves):

1. Set OutputBlending to FB_Overwrite.
2. Set Masked to on.
3. Set Opacity.Material to a texture with the cutout in its alpha channel.
4. Set Opacity.Channel to MC_A.

Translucent surfaces do not write depth and do not block baked light. See
[14-Lighting.md](14-Lighting.md).

## How lighting uses a material

The lighting build stores only light reach and shadowing per surface. The game applies the
material inputs at render time: the diffuse colour, the normal map, the specular inputs and
the emissive term. Therefore:

- A change to a material's textures or colours needs no lighting build.
- A normal map changes the look of every baked light on the surface without a rebuild.
- A dark diffuse texture stays dark under strong light. Balance materials and lights together.

See [14-Lighting.md](14-Lighting.md) for the lighting model.

## Create a new material

1. In the Texture browser, select the target package and group in the lists.
2. Click File > New. The New Material dialog opens.
3. Enter the Package. Use MyLevel or a new package name.
4. Enter the Group. A group name cannot contain spaces.
5. Enter the Name. A name cannot contain spaces.
6. In the Class list, keep "Raw Material".
7. In the property list below, set MaterialClass to the material class you want. The
   default is Shader.
8. Click New.

The new material appears in the browser with empty inputs. Set its inputs as described in
"Edit a material".

## Edit a material

1. Right-click the material in the Texture browser.
2. Click Properties. The Properties window opens. The left side shows the material tree.
   The right side shows the property grid of the selected tree node.
3. To set an input, select the source texture or material in the Texture browser, then
   click the Use button next to the property.
4. To empty an input, click the Clear button next to the property.
5. To pick an input from a list instead, click the "..." button next to the property.
6. Click a nested material in the tree to edit its own properties.

The viewport updates when you close the property or move the mouse over the viewport.

## Import a texture

The BioShock editor imports textures in bmp, pcx, tga, dds and upt format. Use dds for compressed
textures with mip levels already generated. Use tga for 32-bit images with an alpha channel.

1. In the Texture browser, click File > Import.
2. Select one or more image files.
3. Click Open. The Import Texture dialog opens for the first file.
4. Enter the Package, Group and Name.
5. Set the options in the table below.
6. Click OK. For several files, click OK All to import them with the same options, or
   Skip to leave one file out.

| Option | Function |
|---|---|
| Masked | Marks the texture as a colour-key cutout. Use it for pcx and bmp images with a keyed colour |
| Generate MipMaps | Builds the mip chain. Keep it on for world textures |
| Alpha | Keeps the alpha channel of a 32-bit tga |
| Detail Hack? | Legacy stock option. Leave it off |

After the import, compress the texture: right-click it, then click Compress > DXT1 for
opaque textures or Compress > DXT5 for textures with an alpha channel. Uncompressed textures
make the map larger and slow the game.

**NOTE:** Not verified: the format the game expects for imported normal maps. Test a normal
map in the game before you build a set of materials on it.

**NOTE:** Textures and materials can also be imported at compile time, from a script package,
with the same options. See [34-Importing-Assets-with-UCC.md](34-Importing-Assets-with-UCC.md).

### Texture properties

| Property | Category | Function |
|---|---|---|
| bMasked | Surface | The texture is a colour-key cutout |
| bAlphaTexture | Surface | The texture has a usable alpha channel |
| bTwoSided | Surface | Draws both sides when used directly on a surface |
| LODSet, NormalLOD | (none) | Texture detail group and mip bias |
| Detail | Texture | A stock detail texture. The game's shaders do not use it |
| bStreamable | Streaming | Lets the game stream the texture from the bulk content |
| AnimNext, MinFrameRate, MaxFrameRate | Animation | Frame-flip animation to the next texture |

## Cubemaps

A Cubemap is a material with six Faces. The game's reflective shaders sample cubemaps
through UseSpecularCubemaps and ReflectionCubemap.

The content library holds no cubemaps. Each map holds its own, one per CubemapProbe actor,
in a Cubemaps group inside the map. The BioShock editor bakes them for you:

1. Place a CubemapProbe actor (Actor Class browser, Engine group) in each room or area that
   has reflective surfaces. Put it where a reflective surface would "see" the room from,
   about eye height and away from walls. One probe per room is the usual density; retail
   maps use 3 to 31 per level.
2. Run Build > Rebuild Lighting Only. The lighting bake renders the six faces of every probe
   from the probe's position and writes them into the map. The log reports one line per
   probe, prefixed `Cubemap bake:`.
3. Save the map. The cubemaps are part of the saved map and of any bake.

The geometry rebuild (Build > Rebuild, which runs the zone build) gives every BSP leaf one
probe: the probe whose zone is the fewest zone portals away, and among those the nearest to
the leaf. Static meshes and BSP surfaces then reflect the probe of the leaves they sit in.
A leaf in a zone that no probe can reach through zone portals gets no reflection at all, so
give every sealed-off area its own probe. The log line `Cubemap probes:` reports how many
leaves were assigned and how many were unreachable. A material takes part when it sets
UseSpecularCubemaps with SpecularBrightness and SpecularCubeMapBrightness both at 0.01 or
more.

**NOTE:** Adding, moving or deleting a probe changes which leaves use which probe, and that
table is written by the geometry rebuild, not by the lighting build. After a probe change run
Build > Rebuild first, then Build > Rebuild Lighting Only for the captures.

The faces are rendered from the baked, lit scene without editor helpers, sprites, water
surfaces or particles, and are stored the way retail stores them: 64 by 64, three mips, with
an overflow channel for bright lamps. Bake the lighting before you judge a reflection.

**NOTE:** A probe's Cubemap reference is filled by the bake. Do not set it by hand. Moving a
probe or changing the room needs a lighting rebuild to update the reflection.

**NOTE:** A perspective viewport must be open during the bake. The faces are captured
through it, whatever view mode or show flags it has.

Run the cubemap step alone from the console with `LIGHT BIOCUBEMAPS`.

## The bulk content

The game stores most texture pixels outside the packages. `Catalog.bdc` in the game's
`Content\BulkContent` folder lists every externalized texture, and the `.blk` files in the
same folder hold the pixels.

- The BioShock editor reads the game's bulk content through `BulkContentPath`. A retail texture whose
  pixels the BioShock editor cannot find draws without its image.
- The content library packages hold their texture pixels inline. Your own imports do too.
- A bake can externalize the map's textures into a new `.blk` file and a merged catalog.
  See [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).

## Save your materials to a package

1. In the Texture browser, select your package in the Package list.
2. Click File > Save.
3. Type a file name and save it as `Content\<YourPackage>.pkg`. The dialog defaults to
   the `.pkg` extension and to the content library folder, which is the search path.

Materials in MyLevel need no separate save. They save with the map.

**CAUTION:** A saved map imports library materials by name. If you rename or delete a
material in your own package later, every map that uses it loses the reference.

## Troubleshooting

| Symptom | Cause and correction |
|---|---|
| Every retail texture is blank or shows no image | `BulkContentPath` is missing or incorrect. Close the BioShock editor. Open `System\BioShockSDK.ini`. Under `[Engine.Engine]`, set `BulkContentPath` to the game's `Content\BulkContent` folder. Start the BioShock editor again. |
| A surface draws black in Dynamic Light mode | No lighting build since the last change, or the actor has bUnlit off with no light in reach. Run the lighting build |
| A surface draws with the texture but no shading | The viewport is in Textured mode. Switch to Dynamic Light |
| A cutout draws as a solid quad | Masked is off, or Opacity has no Material, or the Channel is wrong |
| A translucent surface hides things behind it | OutputBlending is FB_Overwrite. Set FB_AlphaBlend or FB_Translucent |
| A glass pane is visible from one side only | TwoSided is off |
| The BioShock editor stutters when a material first appears | The material's shader compiles on first use. This is expected |
| A new material draws with the default checker texture | Diffuse is empty |
| The browser refuses to delete a material | The map still uses it. Use Remove from level first |

## Related documents

- [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md)
- [07-BSP-Geometry.md](07-BSP-Geometry.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [10-Static-Meshes.md](10-Static-Meshes.md)
- [14-Lighting.md](14-Lighting.md)
- [15-Water-and-Fluids.md](15-Water-and-Fluids.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
