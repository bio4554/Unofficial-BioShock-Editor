# BioShock Mapping Guidelines

This document collects the conventions that the retail BioShock levels follow. It tells you
how a Rapture level is put together, which sizes the content library kit expects, how the
retail maps are zoned and lit, and in which order to build a new map. Read it before you
block out your first map. The numbers come from measurements of the retail maps and of the
content library meshes.

## Before you start

- Read [05-Concepts-for-Hammer-Radiant-and-UE-Users.md](05-Concepts-for-Hammer-Radiant-and-UE-Users.md)
  and [07-BSP-Geometry.md](07-BSP-Geometry.md).
- Load `Gen_Archways.pkg`, `Gen_Doors.pkg`, `Gen_Stairs.pkg` and `Gen_Lights.pkg` in the Static
  Mesh browser. These four packages are the core kit.

## How a retail level is built

A BioShock level is a simple BSP shell with a large number of static meshes inside it.

- **BSP** gives the level its floors, walls, ceilings and zones. It is plain geometry with
  tiling floor, wall and ceiling materials. It carries the collision of the world.
- **Static meshes** give the level everything you see: pillars, archways, door frames,
  windows, stairs, railings, trim, machines, furniture, debris and light fixtures. The kit
  meshes stand against or inside the BSP walls.

Every mesh in the content library carries its own full collision. A stair mesh, a railing
or a counter collides exactly as it looks. You do not build BSP or blocking volumes under
kit meshes for collision.

The retail maps hold four to seven static mesh actors for each brush. Subtracts outnumber
adds in every finished level.

| Map | Brushes | Static mesh actors | Lights | Zones | Blocking volumes |
| --- | --- | --- | --- | --- | --- |
| Welcome to Rapture | 540 | 2904 | 558 | 37 | 98 |
| Medical Pavilion | 922 | 3403 | 630 | 74 | 52 |
| Fort Frolic | 936 | 4745 | 632 | 112 | 92 |
| Point Prometheus | 1100 | 4445 | 888 | 98 | 26 |

Rules that follow from this:

- Build the BSP as rooms, corridors and floor levels only. Do not model a cornice, a
  pillar, a door frame or a staircase in BSP. Stairs are static meshes.
- Keep every BSP wall flat and on the grid. The kit meshes hide the joins.
- Do not texture BSP with detail materials. Use the tiling materials from
  `Gen_FloorsWallsAndCeilings.pkg`. The meshes carry the detail.
- Expect a finished room to hold twenty to fifty static mesh actors.

## Order of work

1. Block out the level in BSP on a 64-unit grid. Use the vertical module in the next
   section. Subtract the rooms and corridors. Set the floor levels so that a stair mesh
   fits between them.
2. Add a PlayerStart. Run the geometry build. Walk the block-out with Play Map.
3. Seal the rooms with zone portals. Add one ZoneInfo per zone and set the ambient
   preset. Add the sky zone away from the playable space.
4. Place the structural kit: archways in the openings, pillars along the walls, door
   frames and doors, window frames, railings and stairs.
5. Place the props: furniture, signs, machines, debris.
6. Place the light fixtures and the Light actors. Run the lighting build. Iterate.
7. Add water: water surfaces, fluid volumes, caustic projectors.
8. Add blocking volumes where the player snags on props.
9. Place PathNodes and run the path build.
10. Bake and play. See [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).

## The vertical module

The kit is built on a 256-unit vertical module. Build the BSP to these sizes so that the kit
fits without scaling.

| Item | Size in units |
| --- | --- |
| Player collision cylinder | 136 tall, 68 wide. Crouch 80 tall. |
| Kit module | 256 tall |
| Standard room and column height | 384 |
| Storey spacing in retail maps | 384 to 416 |
| Tall hall | 512 to 768 (two stacked modules) |
| Door opening | 192 wide, 256 tall |
| Medical Pavilion door opening | 256 wide, 385 tall |
| Corridor kit (`Gen_Architecture` tunnel sections) | 384 wide, 512 long, about 336 clear height |
| Stair step | 16 rise, 32 run |
| Stair mesh (`Gen_Stairs.stairs`) | 192 wide, 256 run, 128 rise, 8 steps |
| Railing | 84 tall |
| Counter (`Gen_Counter_System`) | 80 tall, 128 long per straight section |
| Archway module (`Gen_Archways.Archway_01n`) | 240 wide, 80 deep, 256 tall |
| Pillar (`Gen_Archways.Pillar_02`) | 82 by 68 footprint, 256 tall. Stacks in 256 steps. |
| Pillar (`Gen_Archways.Pillar_03`) | 104 by 80 footprint, 384 tall |
| Window (`Gen_Windows.window_256_corner`) | 256 tall glazing |
| Elevator shaft section | 218 square, 384 tall |
| Door frame (`Gen_Doors.Low_Rent_Door_Frame`) | 240 wide, 284 tall, 48 deep |

Rules:

- Make room heights a multiple of 128, and prefer 384. Make hall heights 512 or 768.
- Cut door openings 192 by 256 and let the door frame mesh cover the cut edges. The
  frame is wider and taller than the opening.
- Stairs are static meshes. Place `Gen_Stairs` meshes between two floor levels and leave
  the BSP open under them. The mesh's own collision carries the player. Make the
  difference between floor levels a multiple of 128 so that the 128-rise stair mesh fits,
  or use the two-step mesh for a 32-unit change.
- For a storey height that no stair mesh matches, do what the retail designers did: take
  a tall stair mesh, align its top step with the upper floor, and let the bottom of the
  mesh sink through the BSP floor. The steps below the floor are inside solid space, so
  the game never draws them, and the player walks onto the mesh from the lower floor at
  whichever step meets it. One tall stair mesh serves many storey heights this way.
  `Gen_Stairs.stairs` is the mesh the retail maps use for this.
- A flooded staircase is the same recipe with a second mesh. Place
  `Gen_Water.FX_StairWater_C`, the waterfall mesh made for `Gen_Stairs.stairs`, over the
  stair mesh with the same alignment, and let its bottom clip through the floor with the
  stairs. Then place a puddle mesh such as `Gen_Water.LongPuddle` on the floor at the foot
  of the stairs so that the water has somewhere to land. `Gen_Water.pkg` holds puddles of
  several sizes: `LongPuddle`, `LongPuddleDrippy`, `RoundPuddle`, `RoundPuddleDrippy`,
  `Drip_puddle`, `UPuddle` and `puddle_alt`.
- A leaking wall uses the same clipping trick. `Gen_Water.WallSheet` is a 512-unit-tall
  sheet of water that runs down a wall. Place it flat against the wall with its top at the
  ceiling or at the leak. In a room shorter than 512 units, let the bottom of the sheet
  clip through the floor. Place a puddle mesh on the floor under the sheet to complete
  the look. `Gen_Water.wallsheet_alt` is a second shape of the same sheet. The retail maps
  use wall sheets on many walls, so this is the standard way to show water damage.
  In the game a sheet only behaves as falling water inside a CascadingWaterVolume that
  points at it and at a splash emitter on the floor. The full recipe is in
  [15-Water-and-Fluids.md](15-Water-and-Fluids.md), "Place falling water".
  The same meshes also work away from a wall, as a curtain of water that falls from a
  ceiling leak into a puddle. The sheet meshes carry a collision proxy and the retail
  maps leave their collision on. Keep it on when you place them.
  **NOTE:** Not verified: what the game does with that collision. It is probably how the
  game knows to play the wet-screen effect when the player walks through the water.
- Stack pillar meshes in 256 steps. The retail maps stack `Pillar_02` at 512 and
  `Pillar_03_tile` at 256.

## Placement conventions

- **Rotation.** Three quarters of the retail static meshes stand at a multiple of 90
  degrees of yaw (16384 units). Most of the rest stand at 45 or 22.5 degrees. Keep the
  rotation grid on.
- **Position.** Retail meshes are not on a fine grid. Fewer than one in six stands on a
  16-unit grid. The kit was placed by eye and by vertex snap. Use the Toggle Vertex Snap
  button to fit a mesh to a wall corner, then move it off grid when the join needs it.
- **Scale.** About one third of the retail meshes have a uniform DrawScale other than 1.
  Common values are 0.5, 0.7, 0.8, 1.5 and 2.0. DrawScale3D is never used. Scale props,
  not the structural kit.
- **Collision.** About one in ten retail meshes has bCollideActors or bBlockActors off.
  Turn collision off on small decor that the player must not snag on: papers, bottles,
  streamers, cloth. Keep it on for anything the player can stand on.
- **Blocking volumes.** Every retail map has 15 to 130 BlockingVolumes. Use them to smooth
  the collision around clusters of props and to keep the player out of gaps between
  meshes.

## Using the kit

- The `Gen_` packages supply three quarters of the placements in every map.
  `Gen_Archways` alone is one quarter. `Gen_Stairs`, `Gen_Decor`, `Gen_BattleDebris`,
  `Gen_Lights` and `Gen_Windows` follow.
- The most placed meshes in Welcome to Rapture are `Pillar_02`, `Pillar_03_tile`,
  `Theater_Light_Support`, `Archway_01n`, `Pillar_03_base`, `Archway_01nb`,
  `railing_open`, `Pillar_03`, `window_256_corner`, `stairs` and `light_floor`.
- The district packages (`Wel_`, `Bat_`, `Med_`, `Res_`, `Rec_`, `Hyd_`, `Eng_`, `Sci_`,
  `Gau_`) supply the rest. The retail maps borrow from other districts freely. Welcome to
  Rapture uses Medical, Apollo Square and Arcadia meshes.
- Build a wall as a rhythm: pillar, archway or window, pillar. Fill the bay between the
  pillars with a flat BSP wall and a wall material. Put a light fixture on every second
  bay.
- Put battle debris, rubble, papers and water damage last. They break the repetition of
  the kit.
- Cover every raw BSP corner with a corner mesh. A bare BSP corner reads as unfinished.
  Fort Frolic uses the `Rec_Architecture` column family for this: `zoo_column_plate` on
  flat wall corners, `zoo_column_corner` and `zoo_column_corner_in` on outside and inside
  corners, and `zoo_column_arch_128`, `zoo_column_arch_corner`, `zoo_column_arch_corner_in`
  and `zoo_column_arch_seam` where the columns meet the ceiling. The `_mrw` variants are
  the same pieces with a different material. Other districts use their own pillar and
  trim meshes for the same job, for example the `Gen_Archways` pillars.
- Build neon fixtures from the `Gen_Lights.Neon_System` pieces: `Neon_System_32_Section`,
  `Neon_System_128_Section`, `Neon_System_512_Section` and `Neon_System_90deg_Corner`.
  Chain the straight sections and turn corners with the 90-degree piece to outline a
  doorway, a sign or a ceiling edge. The colour comes from the material: on each placed
  piece, set Skins[0] in the Display category to `Gen_Lights.Neon_System_Blue`,
  `Neon_System_Green` or `Neon_System_Red`. Add a Light actor of the same colour with
  LightType set to LT_Flicker or LT_SubtlePulse for the buzz. Fort Frolic places about
  160 of these pieces, but the system fits any district.
- Make a cracked floor with a recessed debris mesh. Subtract a shallow rectangle from the
  floor BSP where the damage goes. Place `Gen_BattleDebris.Marble_Tile_Broken_Section` in
  the recess so that its intact tiles sit level with the floor. Set Skins[0] on the actor
  to the same shader that the surrounding BSP floor uses, so that the tiles match and
  only the cracks and the missing tiles show. `Marble_Tile_Broken_Section_02` and
  `Marble_Tile_Broken_Single` are smaller variants. Scatter `Rubble_marble` pieces from
  the same package around the hole to finish it. Welcome to Rapture and Fort Frolic use
  this pattern often.
- A kit mesh with an opening in it needs a pocket in the BSP behind it. The Little Sister
  vent is the common case: the vent mesh sits on the wall, and a small cube is subtracted
  from the wall behind the grille. Without the pocket the wall material shows through the
  hole. Apply the same rule to any recessed mesh: wall safes, alcoves, ducts and pipe
  openings. Subtract the pocket first, then place the mesh over it.

## Set dressing with the debris kit

`Gen_BattleDebris.pkg` is the kit that fills the floors. It supplies about one placement in
twenty in every retail map, and Fort Frolic uses it heavily. Place it after the structural
kit and the props. The pieces fall into six groups:

| Group | Meshes | Use |
| --- | --- | --- |
| Rubble piles | `Rubble_marble`, `Rubble_marble_single`, `Rubble_Marble_Large`, `Rubble_Marble_Large_single`, `Rubble_marble_fish`, `debrisPile`, `debrisPile_2`, `debrisPile_singlepiece`, `girderPile00`, `girderPile01`, `girder00`, `ConcreteWall_Pile`, `IcePileMeltable` | Fill a corner, the foot of a damaged wall or the edge of a hole. |
| Broken kit pieces | `Pillar_03_Broken`, `Archway_01n_broken`, `Broken_Stairs`, `Broken_Stairs_Only`, `Broken_Stairs_Single`, `Gen_Counter_StraightBroken`, `Gen_Counter_90outsideWideBroken`, `Concrete_Barrier_Broken`, `Concrete_SingleBroken`, `Broken_Pipe`, `WoodShotUp`, `GougedWood` | Swap for the intact kit piece where the level is damaged. They match the `Gen_Archways`, `Gen_Stairs` and `Gen_Counter_System` sizes. |
| Holes and damage | `WallHole01`, `Hole03`, `wallHole_corner`, `ConcreteWall_Hole`, `ConcreteWall_Hole_Rim`, `ConcreteWall_singlepiece`, `marble_ceiling_damage`, `marble_ceiling_damage__2`, `corner_blast`, `Marble_Tile_Broken_Section`, `Marble_Tile_Broken_Section_02`, `Marble_Tile_Broken_Single` | Seat in a subtracted pocket in the BSP. See the cracked floor recipe above. |
| Decals | `BloodSplat_1`, `BloodSplat_2`, `BloodSplat_3`, `bloodsmear`, `bloody_footprints_00`, `bloody_footprints_01`, `ScorchMark`, `damdec_01` to `damdec_04`, `eng_damagedecal_01`, `eng_damagedecal_02`, `Broken_Glass_01`, `BrokenGlassA`, `GlassDust`, `shells`, `shells_small` | Flat meshes laid on floors and walls. Their materials blend, so they draw in the sorted translucent pass. Turn collision off on them. |
| Barricades | `bags_01` to `bags_07`, `Concrete_Barrier`, `Concrete_Barrier_Ceiling`, `woodBarrier_01`, `woodBarrier_02`, `woodBarrier_destroyed_01`, `Armored_Barricade`, `BarbedWire_01`, `BarbedWire_04` | Block a route or mark a battle line. Keep collision on. |
| Story pieces | `Cat_Dead`, `Pillow00`, `Pillow01`, `Breakable_Sign_02`, `Breakable_Sign_Wires`, `Breakable_Sign_Blockage_Chunk`, `IceSpillInvisible`, `IceBarrierInvisible` | Single details and gameplay blockers. The invisible ice meshes are collision for the game's ice effects and are not covered in this guide. |

Habits from the retail maps:

- Put rubble where damage would drop it: under a hole in the ceiling, at the foot of a
  broken pillar, along a cracked wall. Rubble in the middle of a clean floor reads as
  random.
- Rotate and scale the rubble piles. Rubble is the one kit whose pieces repeat visibly if
  you do not.
- Turn collision off on decals, papers and small rubble so that the player does not snag.
  Keep it on for piles the player can climb and for barricades.
- Layer a decal under a pile: a blood splat under a body, a scorch mark under a burst
  pipe, glass dust under a broken window.

## Zoning conventions

- The retail maps have one zone per room or corridor segment. Welcome to Rapture has 37
  zones. Fort Frolic has 112.
- A zone holds six to fifteen lights.
- Zone portals sit in every doorway, at every corridor end, and at the top and bottom of
  every stairwell.
- Antiportals are rare. Most maps have fewer than ten. Zone portals do the culling.
- Every map has exactly one SkyZoneInfo in a small sealed box. The box sits away from
  the playable space, in most maps far below or beside it.

**CAUTION:** Never place the SkyZoneInfo inside a playable zone. The renderer draws sky-zone
geometry only in the sky pass. A SkyZoneInfo in a normal room makes the room's walls show
a hall of mirrors, hides its meshes, and makes its lights look as if the bake ignored them.
Check the Region of the actors in the room if you see these symptoms.

## Ambient and fog presets

Almost every retail zone uses one ambient preset. Only the colour tint changes between
districts.

| Property | Value |
| --- | --- |
| NormalPressureAmbientColorHigh | R=32, G=44, B=53 (Welcome, Medical) or R=20, G=43, B=44 (Fort Frolic) |
| NormalPressureAmbientColorHighMultiplier | 40 |
| NormalPressureAmbientColorLow | Default |
| NormalPressureAmbientColorLowMultiplier | 1.0 |
| NormalPressureAmbientContrastPower | 2.0 |
| NormalPressureAmbientXGroundRatio | 0.15 |

**NOTE:** The class default of the high multiplier is 1.0. A zone that keeps that default is
almost black. Set it to 40.

Fog differs by district:

| District | bDistanceFog | Start | End | Colour | Contribution |
| --- | --- | --- | --- | --- | --- |
| Welcome to Rapture | On in every zone | 0 | Default | R=22, G=33, B=30 | 0.02 (a faint haze) |
| Medical Pavilion | On in one zone of six | 0 | 4000 to 5000 | R=70, G=94, B=85 | 0.1 |
| Fort Frolic | Most zones | 1000 | 3000 | R=17, G=38, B=31 | 0.25 |
| Sky zone | On | 0 | 2000 to 20000 | Dark teal | 1.0 |

**NOTE:** Not verified: the retail maps write the fog values into both the ZoneLight fields
(DistanceFogStart, DistanceFogEnd, DistanceFogColor) and the read-only ZoneFog fields
(NormalDistanceFogStart and the others). The BioShock editor lets you set the ZoneLight fields and
bDistanceFog only. Test fog in the game.

## Lighting conventions

The retail lights follow a small set of habits. Use them as starting values.

| Setting | Retail habit |
| --- | --- |
| LightBrightness | Median 1.0. Point lights 0.6 to 0.75. Spot lights 1.3 to 1.5. The class default of 0.35 is rare. |
| LightRadius | Median 400. Point lights 300 to 500. Spot lights 600 to 800. A radius of 1024, the class default, is rare indoors. |
| LightEffect | Point lights 65 to 85 percent. Spot lights 13 to 35 percent. Directional lights 6 to 12 per map. Sun lights only in the exterior of the Lighthouse. |
| LightCone | 50 to 60 for most spot lights (a half angle of about 36 to 40 degrees). 25 to 32 for narrow beams. |
| LightType | LT_Steady on 82 to 89 percent. LT_Flicker, LT_SubtlePulse and LT_Pulse on the rest, for damaged fixtures and neon. |
| bCastsShadowMapShadows | On for about one light in ten. Almost all of them are spot lights with a radius of 512 to 1200. |
| Directional lights | LightRadius (the depth of the beam) 2000 to 4000 and LightWidth (the width of the beam) 1000 to 3000. They are the big soft fills of a hall. |
| SpecialLitChannel | Set on about 100 lights per map. They light machines and signs on their own channel. |

Colour habits:

- Fill light is mint or teal: R=170, G=255, B=210 and R=119, G=255, B=227 are the most
  common colours. They give the underwater look.
- Accent light is warm amber or orange: R=255, G=235, B=174, R=244, G=165, B=77 and
  R=233, G=154, B=97 stand for incandescent fixtures.
- White light is rare outside Fort Frolic.

Placement habits:

- One point light per bay, at fixture height, with a radius of 300 to 500. The radius
  covers one bay and reaches the next pillar. Most actors keep the default of 3 for
  MaxLightsStatic, so keep the radii small so that neighbouring bays do not fight for
  the slots. Raise MaxLightsStatic on a large mesh that many lights must reach.
- One spot light per fixture that has a visible cone, aimed down, with a light beam mesh
  under it.
- One directional or large point light per hall as the fill, with a low brightness.
- A light fixture mesh from `Gen_Lights.pkg` at every Light actor that stands for a lamp.
  A bare light with no fixture reads as an error in Rapture.
- God rays from `Gen_Lights.Light_Beams`. The mesh is a fan of translucent planes that
  fakes shafts of light through dust. Place it under a skylight, a high window, a broken
  ceiling or a stage light, aimed along the direction of the light source, and scale it to
  the space. Change the colour with Skins[0]: `Gen_Lights.White_Beams`, `Orange_Beams`,
  `Light_Beam_01`, `light_Beam_Bright` and `Light_Beams_Science_Yellow` are the common
  variants. `SpotLight_Beam_01` and `light_beam_02` are single-cone beam meshes for one
  lamp. Beam meshes do not block baked light and keep their material in the untextured
  view modes. They carry NeverCollide (see chapter 10), so they never block the player, the
  AI or Build Paths, whatever the Collision settings of the actor. Fort Frolic uses
  `Light_Beams` in most of its large rooms.
- A small, bright light in front of every vending machine and other interactive machine.
  The retail maps place a Light actor with a small radius and a brightness above the
  room's lights just in front of the machine, so that the machine stands out from the
  wall and the player's eye goes to it. The machine's own emissive material lights the
  front panel; the extra light lights the floor and the frame around it.

See [14-Lighting.md](14-Lighting.md) for the lighting model.

## Water conventions

- Every retail map has 6 to 21 FluidVolumes and 0 to 14 CascadingWaterVolumes.
- Caustic projectors are few: 0 to 12 per map. Only flooded rooms with visible moving
  water get them.
- Standing water is a non-solid sheet brush that faces up, with a `Gen_WaterSurface.pkg`
  shader, plus a FluidVolume from the floor up to the sheet. Flowing water is a
  `Gen_Water.pkg` mesh. See [15-Water-and-Fluids.md](15-Water-and-Fluids.md), "Place a body
  of water".

The `Gen_Water.pkg` meshes cover every kind of leak in Rapture. Pick by the source of the
water:

| Water source | Mesh |
| --- | --- |
| Cracked window or bulkhead seam, water forced in under pressure | `WaterSpewB`, `waterspew` |
| Burst pipe, a narrow jet | `FX_WaterJet`, `WaterJetNew` |
| Ceiling drips | `Drips`, `DripsSparse` |
| Wall run-off | `WallSheet`, `wallsheet_alt` |
| Flooded staircase | `FX_StairWater_C` over `Gen_Stairs.stairs` |
| Ledge or balcony overflow | `Cascade__512`, `Cascade__1024`, `CascadeSplit__512` |
| Small flow down a wall or step | `WaterTrickle__512` |
| Large waterfall | `WaterFallFlow`, `FX_waterfall` |
| Where the water lands | `LongPuddle`, `LongPuddleDrippy`, `RoundPuddle`, `RoundPuddleDrippy`, `Drip_puddle`, `UPuddle`, `puddle_alt` |

Place the spew meshes at window cracks, aimed into the room, and put a puddle or a
FluidVolume where the water lands. The retail maps use the spews to show that the sea is
coming in, so they sit on the exterior walls and windows.

Wall sheets, ledge cascades and small streams need more than the mesh: a CascadingWaterVolume
around the mesh, a splash emitter from `FXClass.pkg` at the floor, a puddle mesh, and the
volume's `WaterMesh_1` and `WaterSplashEffect` set to the mesh and the emitter. Every retail
cascade is built this way. See [15-Water-and-Fluids.md](15-Water-and-Fluids.md).

See [15-Water-and-Fluids.md](15-Water-and-Fluids.md).

## Budgets

Use the largest retail maps as the upper bound of what the game handles well.

| Item | Largest retail value |
| --- | --- |
| Brushes | 1100 |
| Static mesh actors | 5300 |
| Lights | 890 |
| Zones | 112 |
| PathNodes | 750 |
| Map extent | About 48000 by 10000 by 17000 units (Medical Pavilion) |

A map of a few rooms stays far below these values. Keep your working files as saved maps
and check the baked size after each milestone.

## Checklist before the first bake

1. Every room is sealed by zone portals and has one ZoneInfo with the ambient preset.
2. The SkyZoneInfo is in its own box outside the playable space.
3. Every door opening has a door frame mesh, and every door has a door actor or a mover.
4. Every Light actor stands for a fixture mesh, and the lighting build has run after the
   last light edit.
5. Small decor has collision off. Blocking volumes cover the snag points.
6. PathNodes cover every walkable route, and the path build has run.
7. The geometry build has run after the last brush change.
8. The map has a PlayerStart above the floor.

## Related documents

- [05-Concepts-for-Hammer-Radiant-and-UE-Users.md](05-Concepts-for-Hammer-Radiant-and-UE-Users.md)
- [07-BSP-Geometry.md](07-BSP-Geometry.md)
- [08-Zones-and-Portals.md](08-Zones-and-Portals.md)
- [10-Static-Meshes.md](10-Static-Meshes.md)
- [14-Lighting.md](14-Lighting.md)
- [15-Water-and-Fluids.md](15-Water-and-Fluids.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [26-AI-Paths.md](26-AI-Paths.md)
