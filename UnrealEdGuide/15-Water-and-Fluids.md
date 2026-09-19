# Water and Fluids

This document explains how water is built in BioShock content and how to place water in
your own map. It covers the water materials, the volumes that give water its physical
behaviour, and the caustic projectors that light submerged floors. Read it after
[13-Materials-and-Textures.md](13-Materials-and-Textures.md). This is an advanced topic.

## Before you start

- The content library is loaded. `Gen_Water.pkg`, `Gen_WaterSurface.pkg` and `FXClass.pkg` hold the
  water content.
- You know how to add a volume from the builder brush. See
  [07-BSP-Geometry.md](07-BSP-Geometry.md).
- You know how to place an actor from the Actor Class browser. See
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).

## How water works in BioShock

Water in BioShock is not one object. It is three separate layers that you place one by one:

| Layer | What it is | What it does |
|---|---|---|
| The water surface | A static mesh or a BSP surface with a fluid material | Draws the water: refraction, reflection, movement |
| The water volume | A FluidVolume or a CascadingWaterVolume | Makes the water physical: wading, buoyancy, electric shocks |
| The caustics | Caustic_Projector actors | Draw moving light patterns on floors and props under the water |

A puddle on a floor needs only the first layer. A pool that the player wades through needs
all three. Falling water (a wall sheet, a ledge cascade or a small stream) is its own
recipe: a water mesh, a CascadingWaterVolume around it, a splash emitter at the floor and a
puddle mesh where the water lands. See "Place falling water" below.

## Water materials

The water materials are in `Gen_Water.pkg` and `Gen_WaterSurface.pkg`. Open them in the Texture
browser with File > Open. They belong to three material classes:

| Class | Use |
|---|---|
| FluidShader | Flowing and standing water on meshes and surfaces: cascades, trickles, puddles, floor flow, spew, ocean. Every shader in `Gen_WaterSurface.pkg` is a FluidShader |
| FluidSurfaceShader | One special case: `Gen_Water.WoodWaterSurface_Shader`, water over a wood floor |
| PondScumFluidShader | Still water with scum on top |

Examples in `Gen_Water.pkg`: `WaterCascadeA_Shader`, `WaterFallFlow_Shader`,
`WaterTrickle_Shader`, `FloorFlowA_Shader`, `LongPuddleCalm`, `RoundPuddleDrippy`,
`StairPour_Shader`, `WallSheet_Shader`, `ToiletWater_Shader`, `FountainFlow_Shader`,
`RoughWater_Shader`, `OilyOcean_Shader`. Names that end in `_NoReflect` or `_NoRef` skip the
reflection term and cost less.

`Gen_WaterSurface.pkg` holds a matrix of surface shaders: calm, rough and flowing, each with
no, low or mid reflection.

The fluid materials move through animator objects (TexturePanner) that the library
materials already contain. The animator values are not editable in the BioShock editor. Choose the
library material whose flow speed and direction fit your geometry, and rotate the surface or
mesh to match.

`Gen_Water.pkg` also holds the water meshes: water jets, cascades, sheets and puddles that
carry a fluid material on a shaped mesh. The retail maps use these often:

| Mesh | Use |
| --- | --- |
| `FX_StairWater_C` | A waterfall shaped for the `Gen_Stairs.stairs` mesh. Place it over the stairs with the same alignment. |
| `WallSheet`, `wallsheet_alt` | A 512-unit-tall sheet of water that runs down a wall, or falls free from a ceiling leak. Let the bottom clip through the floor in a shorter room. Place a puddle under it. Keep its collision on. Needs a CascadingWaterVolume and a splash emitter. See "Place falling water". |
| `LongPuddle`, `LongPuddleDrippy` | A long puddle. Place one at the foot of a stair waterfall. |
| `RoundPuddle`, `RoundPuddleDrippy`, `Drip_puddle`, `UPuddle`, `puddle_alt` | Puddles of other shapes and sizes. |
| `WaterSpewB`, `waterspew` | Water forced through a window crack or a seam. |
| `FX_WaterJet`, `WaterJetNew` | A narrow jet from a burst pipe. |
| `Drips`, `DripsSparse` | Ceiling drips. |
| `Cascade__512`, `Cascade__1024`, `CascadeSplit__512` | Water that overflows a ledge, in 512 and 1024 lengths. Needs a CascadingWaterVolume and a splash emitter. See "Place falling water". |
| `SmallStream` | A thin stream that falls from a leak. Needs a CascadingWaterVolume and a splash emitter. |
| `WaterTrickle__512` | A small flow down a wall or a step. |
| `WaterFallFlow`, `FX_waterfall` | A large waterfall. |

See [06-BioShock-Mapping-Guidelines.md](06-BioShock-Mapping-Guidelines.md) for the flooded
staircase recipe.

### Place a body of water

A pool, a flooded room or a flooded corridor needs two things: a non-solid sheet brush with
a surface shader, and a FluidVolume that fills the water. The sheet is the water you see.
The volume is the water the game knows about: without it the player does not wade, the
water effects do not play, and Electro Bolt does not shock the water.

**Place the water sheet**

1. Build the pool or the flooded room in BSP and rebuild.
2. In the Builders group of the button bar, right-click the Sheet button. Set Width and
   Height to cover the water, and Axis to the floor and ceiling plane. Click Build.
3. Move the builder brush so that the sheet sits at the water level.
4. The sheet must face up. A new floor and ceiling sheet faces down. With the builder brush
   selected, right-click a viewport and click Transform > Mirror About Z.
5. Click Brush > Add Special... (or the Add Special Brush button in the CSG group). Select
   the Regular Brush preset, click Non-Solid, then click OK.
6. Click the new sheet in a viewport to select its surface.
7. In the Texture browser, click File > Open and open `Gen_WaterSurface.pkg`. Click a surface
   shader. The BioShock editor applies it to the selected surface.
8. In the Surface Properties window (F5), scale and align the material so the ripples have
   a natural size.

The surface shaders come in three families: `CalmWater_*` for a still pool, `Roughwater_*`
and `RoughWater*` for open or disturbed water, and `FlowingWater_*` for a current. The
`_NoRef`, `_LowRef` and `_MidRef` suffixes set how much the surface reflects. Retail maps use
the sheet as the water surface: 4-Recreation has seven FluidShader surfaces on its BSP.

**Add the FluidVolume**

9. Shape the builder brush to fill the water from the pool floor to the sheet.
10. In the CSG group of the button bar, right-click the Volume button and click FluidVolume.
11. Leave the properties at their defaults. `bWaterVolume` is already on.
12. Build > Rebuild, then test with Play Map: wade in, then shock the water with Electro Bolt.

**NOTE:** A water mesh from `Gen_Water.pkg` (a puddle, a trickle) does not need a volume. Only
water that the player or the AI can enter needs a FluidVolume.

Fluid materials with an Opacity mask do not block baked light. Light passes through the
water surface to the floor below. See [14-Lighting.md](14-Lighting.md).

## FluidVolume

FluidVolume is a PhysicsVolume. It marks the space that counts as water for the game.
Actors inside it wade, float and conduct electricity.

### Add a FluidVolume

1. Shape the builder brush to fill the water from the floor to the water surface.
2. In the CSG group of the button bar, right-click the Volume button. A list of volume
   classes opens.
3. Click FluidVolume. The BioShock editor creates the volume from the builder brush.
4. Select the new volume and press F4 to open the Properties window.

### FluidVolume properties

The Properties window shows the PhysicsVolume properties and one FluidVolume property.

| Property | Category | Function |
|---|---|---|
| Wave | (none) | The wave that moves actors in the volume. See the table below |
| bWaterVolume | (none) | Marks the volume as water for the game's movement code. On by default; leave it on |
| FluidFriction | (none) | Slows movement in the water |
| EntrySound, ExitSound | (none) | Sounds played when an actor enters or leaves |
| EntryActor, ExitActor | (none) | Effect classes spawned on entry and exit, for example a splash |
| Priority | (none) | Which volume wins where volumes overlap |

The Wave property has four fields:

| Field | Range | Function |
|---|---|---|
| Amplitude | 0 to 5 | Height of the wave in meters |
| WaveLength | 0.001 to 12 | Distance between wave peaks in meters |
| WavePeriod | 0.001 to 12 | Seconds for the wave to travel one wave length |
| direction | vector | Travel direction of the wave |

Retail maps leave the FluidVolume properties at their defaults; the volumes in the retail
maps set nothing beyond their Label. Start with the defaults. `bWaterVolume` is on by default,
and so are buoyancy, waves and the shocked-water effect.

**NOTE:** The switches that enable buoyancy, waves and the volume itself are not shown in
the Properties window of the BioShock editor. The volume uses its default behaviour.

## CascadingWaterVolume

CascadingWaterVolume marks falling water: a sheet that runs down a wall, a cascade that
overflows a ledge, a thin stream from a ceiling leak. It gives the water mesh its behaviour
in the game and plays the splash where the water lands. The retail maps use 0 to 14 per map,
and every one of them points at a `wallsheet_alt`, `Cascade__512`, `Cascade__1024` or
`SmallStream` mesh actor and at a splash emitter next to it.

### Place falling water

Falling water that looks correct in the game needs five things. If one is missing, the
water is a still, flat mesh, the splash does not play, or the water lands on bare floor.

| Part | What to place | Where |
|---|---|---|
| The water mesh | A static mesh from `Gen_Water.pkg`: `wallsheet_alt` or `WallSheet` for a wall run-off or a free-falling sheet, `Cascade__512` or `Cascade__1024` for a ledge overflow, `SmallStream` for a thin leak | Top at the ceiling, the leak or the ledge. Let the bottom clip through the floor |
| The puddle mesh | A puddle static mesh from `Gen_Water.pkg`: `LongPuddle`, `RoundPuddle`, `Drip_puddle`, `UPuddle` or one of the `Drippy` variants | On the floor where the water lands |
| The splash emitter | An Emitter actor from `FXClass.pkg`: `WaterSplash_Small`, `WaterSplash_SmallPP`, `WaterSplashSpray_Small`, `Splash_Trickle` or `Splash_SmallStream` | On the floor at the foot of the water mesh, inside the puddle |
| The volume | A CascadingWaterVolume | A brush that encloses the water mesh from top to floor |
| The links | The volume's `WaterMesh_1` and `WaterSplashEffect` properties | Point them at the water mesh actor and the splash emitter |

1. Place the water mesh. In the Static Mesh browser, open `Gen_Water.pkg` and select the
   mesh. Right-click the wall or ceiling in a viewport and click **Add Static Mesh**. Move
   the actor so that its top sits at the source of the water. Keep its collision on.
2. Place the puddle mesh on the floor under the bottom of the water mesh.
3. Place the splash emitter. In the Actor Class browser, click File > Open Package and open
   `Content\FXClass.pkg`. Expand Actor > Emitter, select `WaterSplash_Small` (or one of the
   other splash classes), right-click the floor inside the puddle and click Add
   WaterSplash_Small Here. Its Emitters list is filled in for you.
4. Shape the builder brush so that it encloses the water mesh from top to floor. Make it a
   little wider than the mesh.
5. In the CSG group of the button bar, right-click the Volume button and click
   CascadingWaterVolume. The BioShock editor creates the volume from the builder brush.
6. Select the volume and press F4. Open the Water category.
7. Click `WaterMesh_1`, click the Find button, then click the water mesh actor in a viewport.
8. Click `WaterSplashEffect`, click the Find button, then click the splash emitter in a
   viewport.
9. Build > Rebuild, then Build > Rebuild Lighting Only, then test with Play Map.

Pick the splash class by the size of the flow: `Splash_Trickle` and `Splash_SmallStream` for
a thin leak, `WaterSplash_Small` and `WaterSplash_SmallPP` for a sheet, `WaterSplashSpray_Small`
where the water hits hard. The retail maps use all of them with the same meshes, so the choice
is a matter of look.

**CAUTION:** SDK 0.1.4 and earlier saved a placed splash emitter in a form that crashes the
game as the map loads. Use a newer SDK. If you must use an older build, delete the emitters
before Play Map or Bake. A map saved by an older build is repaired when a newer editor opens
it; save it again.

### CascadingWaterVolume properties

| Property | Category | Function |
|---|---|---|
| Axis | Water | The flow axis: CWV_X_Axis or CWV_Y_Axis. The retail maps leave it at the default |
| WaterMesh_1 | Water | The water mesh actor. Use the Find button, then click the actor in a viewport |
| WaterSplashEffect | Water | The Emitter actor that plays the splash at the bottom. Use the Find button, then click the actor |

A volume with an empty `WaterMesh_1` or `WaterSplashEffect` does not complete the effect.
Set both references before you test in the game.

**NOTE:** The script action Enable or disable a cascading fluid volume targets this volume by
Label, but it appears to have no effect in the game. See
[22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md).

## Caustics

Under moving water, the game draws light patterns on the floor. A Caustic_Projector actor
projects the material `Gen_Water.CausticCheap_Shader` onto the surfaces below it. The
material animates on its own.

Caustic_Projector is a class in `FXClass.pkg`. Load `FXClass.pkg` in the Actor Class browser to
place it. It is a Projector with these defaults:

| Property | Default | Function |
|---|---|---|
| Material | Gen_Water.CausticCheap_Shader | The caustic material |
| MaxTraceDistance | 250 | How far below the actor the projection reaches, in units |
| FOV | 0 | Width of the projection cone in degrees. 0 gives a parallel projection |
| DrawScale | 2 | Size of the pattern |
| Tile | 3, 3 | Repeats of the pattern across the projection |
| bProjectOnBackfaces | off | Projects on faces that point away from the projector |

### Add a caustic projector

1. In the Actor Class browser, click File > Open Package and open `Content\FXClass.pkg`.
2. Expand Actor > Projector and select Caustic_Projector.
3. Right-click the floor under the water and click Add Caustic_Projector Here.
4. Move the actor up to the water surface. The projection points down along the actor's
   rotation.
5. Set MaxTraceDistance to the depth of the water plus a margin.
6. Place one projector per 500 to 1000 units of floor. Retail maps use about twelve per
   flooded area.

Caustic projectors draw in the BioShock editor in Dynamic Light mode. They draw on BSP and on static
meshes that have AcceptProjectors on in their materials.

## What the BioShock editor shows

- Fluid materials draw in the BioShock editor with refraction and reflection in Dynamic Light mode.
- Caustic projectors draw in the BioShock editor.
- Water, glass and light beams keep their real materials in the Lighting Only and
  Lit Untextured view modes.
- Some water effects appear only in the game: surface ripples from movement, the
  waterfall depth masks and shocked-water effects.

Test every water setup in the game with Play Map before you fine-tune it in the BioShock editor.

## Stock Unreal water

The BioShock editor still contains the stock FluidSurfaceInfo and FluidSurfaceOscillator actors.
BioShock content does not use them, and the SDK's renderer is untested with them.
Do not use them for BioShock water.

## Troubleshooting

| Symptom | Cause and correction |
|---|---|
| The water surface is opaque and dull | The material is a plain texture or a Shader. Use a FluidShader or FluidSurfaceShader |
| The player walks through the water without wading, or Electro Bolt does not shock the water | No FluidVolume, or the volume does not reach the water sheet |
| The water surface draws, but the ripple and shock effects are missing or upside down | The sheet faces down. Mirror the sheet brush about Z and rebuild |
| No caustics on the floor | No Caustic_Projector, or MaxTraceDistance is shorter than the water depth |
| Caustics draw on the ceiling | The projector points up. Reset its rotation |
| Caustics do not draw on a prop | The prop's material has AcceptProjectors off, or the actor has bAcceptsProjectors off |
| The water does not move | The material has no animator. Pick a library material with a panner |
| A wall sheet or cascade is a still, flat mesh in the game, or no splash plays | No CascadingWaterVolume around the mesh, or its `WaterMesh_1` or `WaterSplashEffect` is empty. See "Place falling water" |
| The water falls onto bare floor | No puddle mesh under the water mesh |
| The game crashes as the map loads after you placed a splash emitter | The map was saved by SDK 0.1.4 or earlier. Open and save it with a newer SDK, or delete the emitters |

## Related documents

- [07-BSP-Geometry.md](07-BSP-Geometry.md)
- [08-Zones-and-Portals.md](08-Zones-and-Portals.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [13-Materials-and-Textures.md](13-Materials-and-Textures.md)
- [14-Lighting.md](14-Lighting.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
