# Zones and Portals

This document tells you how to divide a map into zones, how to make zone portals, how to set
the ambient light and fog of a zone, how to make a sky zone, and how volumes relate to
zones. Every mapper needs it. Zones control both visibility and the look of each room.

## Before you start

- You can build and subtract brushes. See [07-BSP-Geometry.md](07-BSP-Geometry.md).
- You can place actors and edit their properties. See
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).

## What a zone is

A zone is a sealed pocket of empty space in the BSP. When you subtract your first room, the
whole map is one zone. When you close an opening between two rooms with a zone portal, the
geometry build splits the space into two zones.

Zones do two jobs:

- Visibility. The game renders a zone only when it is visible through a chain of open
  portals. Actors and BSP in zones that are not visible are not drawn.
- Environment. Each zone has one ZoneInfo actor. The ZoneInfo sets the ambient light,
  the distance fog, the reverb and other values for everything in that zone.

A zone with no ZoneInfo uses the default values of the level. The default ambient is
black. A room with no ZoneInfo and no lights is dark.

## Make a zone portal

A zone portal is a flat sheet brush that fills an opening between two spaces. The sheet
must close the opening exactly. Any gap between the sheet's edge and the surrounding BSP
merges the two zones again.

1. Select the Sheet builder in the button bar. Right-click it and set Height and Width to
   the size of the opening. Set Axis to the plane of the opening.
2. Move the builder brush into the opening. The sheet must lie inside the opening, with
   its edges on the walls, floor and ceiling of the opening. It can extend into the solid
   rock around the opening. It must not leave a gap.
3. Open Brush > Add Special....
4. Select Zone Portal in the Prefabs list. The dialog sets the Portal and Invisible flags
   and the Non-Solid type.
5. Click OK.
6. Run a geometry build (Build > Rebuild Geometry Only).

The portal is invisible in the game. It does not block movement. It shows as a wireframe
in the BioShock editor.

Good places for zone portals:

- Door openings between rooms.
- Corridor ends.
- The top and bottom of a stairwell.
- Any narrow opening between two large spaces.

**NOTE:** A zone portal in the middle of a large open space does not seal anything and
does nothing. Put portals in openings that the BSP surrounds on all sides.

## Check the zones

Use the Zone/Portal view mode in a perspective viewport (View > Mode, or Alt+2). Each zone
draws in its own colour. Portals draw as tinted sheets. If two rooms that a portal
separates show the same colour, the portal has a gap.

The Stats tab of the build window (Build > Build Options...) shows the zone count after a
geometry build.

## Add a ZoneInfo

1. Open the Actor Class Browser (View > Show Actor Class Browser).
2. Expand Actor > Info > ZoneInfo and select ZoneInfo.
3. Right-click a floor surface inside the zone and click Add ZoneInfo Here.
4. Run a geometry build. The build assigns every ZoneInfo to the zone that contains it.
5. Select the ZoneInfo and press F4 to edit its properties.

Place one ZoneInfo per zone. Place it inside the zone, away from the walls. A ZoneInfo
outside every zone, or in solid rock, does nothing.

**NOTE:** Two ZoneInfos in one zone give undefined results. Keep one per zone.

## ZoneInfo properties

The ZoneInfo class has many categories. The tables below list the ones a mapper uses.
Categories for the game's spawning system, map user interface and pressure system are not
covered in this guide.

### ZoneInfo category

| Property | Meaning |
|---|---|
| ZoneTag | A name for the zone. Other actors can refer to it. |
| LocationName | The text the game shows for this location |
| KillZ | Actors that fall below this height are destroyed. Default -10000. |
| KillZType | The kind of death for actors that fall below KillZ |
| bSoftKillZ | Gives 2000 units of grace before the kill applies |
| bDistanceFog | Turns distance fog on for the zone |
| bClearToFogColor | Clears the screen to the fog colour when distance fog is on |
| bTerrainZone | The zone contains terrain. Not tested with BioShock content. |

### ZoneLight category: ambient light

BioShock's ambient light is two colours. The high colour lights surfaces that face up. The
low colour lights surfaces that face down. Surfaces at an angle get a blend of the two. The
blend follows the surface normal, so a sphere shows the high colour on top, the low colour
underneath and a gradient between them.

| Property | Meaning |
|---|---|
| NormalPressureAmbientColorHigh | The ambient colour from above. RGB. |
| NormalPressureAmbientColorHighMultiplier | A brightness factor for the high colour. Default 1.0. |
| NormalPressureAmbientColorLow | The ambient colour from below. RGB. |
| NormalPressureAmbientColorLowMultiplier | A brightness factor for the low colour. Default 1.0. |
| NormalPressureAmbientContrastPower | Shapes the blend between high and low. 1.0 is a linear blend. Higher values make the transition sharper. |
| NormalPressureAmbientXGroundRatio | How much of the low colour reaches surfaces that face sideways. Default 0.15. |
| DramaticLightingScale | A scale factor the game applies to lighting in this zone. Default 1.2. |
| DistanceFogColor, DistanceFogStart, DistanceFogEnd | The fog values the BioShock editor and the game use when bDistanceFog is on. See the fog section. |
| DistanceFogEndMin, DistanceFogBlendTime | Limits and blend time for fog changes at run time |
| EnvironmentMap, TexUPanSpeed, TexVPanSpeed | Legacy values with no effect on BioShock content |
| AmbientBrightness, AmbientHue, AmbientSaturation | Legacy single-colour ambient. Leave at the defaults. Use the two-colour values above. |

The "Normal" in the property names refers to the normal state of the game's pressure
system. The ZoneLightPressure category holds a High and a Low set of the same values plus
EnablePressureSystemForAmbientLighting. Leave that category at its defaults. The game
uses the Normal set until a script changes the pressure.

The BioShock editor shows the ambient result at once when you change these values in the
Properties window. No build is needed for ambient light.

The retail maps use one preset in almost every zone. Only the colour changes between
districts. Start with these values:

| Property | Value |
|---|---|
| NormalPressureAmbientColorHigh | R=32, G=44, B=53 (blue-grey) or R=20, G=43, B=44 (teal) |
| NormalPressureAmbientColorHighMultiplier | 40 |
| NormalPressureAmbientColorLow | Leave at the default |
| NormalPressureAmbientColorLowMultiplier | 1.0 |
| NormalPressureAmbientContrastPower | 2.0 |
| NormalPressureAmbientXGroundRatio | 0.15 |

**NOTE:** The class default for the high multiplier is 1.0. With that value a colour of
R=32, G=44, B=53 is almost black. The retail maps set the multiplier to 40.

Adjust to taste. Ambient is the only fill light in BioShock. The lighting build has no
bounce light. See [14-Lighting.md](14-Lighting.md) and
[06-BioShock-Mapping-Guidelines.md](06-BioShock-Mapping-Guidelines.md).

### Fog

Set bDistanceFog to True and set these values in the ZoneLight category:

| Property | Meaning |
|---|---|
| DistanceFogColor | The fog colour. Default grey (128, 128, 128). |
| DistanceFogStart | The distance in units where fog begins. Default 3000. |
| DistanceFogEnd | The distance in units where fog is full. Default 8000. |

The ZoneFog category holds NormalDistanceFogColor, NormalDistanceFogStart,
NormalDistanceFogEnd, NormalEnableDistanceFog and NormalMaxFogContribution, with High
and Low sets for the pressure system. These fields are read-only in the Properties
window. They hold the values the game's pressure system switches between. Edit the
ZoneLight fog values above.

bDistanceFogClip in the ZoneFog category clips geometry beyond the fog end.

### ZoneSound and Sound categories

| Property | Meaning |
|---|---|
| ZoneEffect | An I3DL2Listener reverb preset for the zone. Click the field and choose New to create one. |
| ReverbType | The game's reverb class for the zone: None, Room, BathRoom, Hall or Cathedral. Read-only in the Properties window. |

### ZoneVisibility category

| Property | Meaning |
|---|---|
| bLonelyZone | The zone never sees other zones and other zones never see it. Use for a sealed room that is far from everything. |
| ManualExcludes | A list of zones that this zone never renders, even if a portal chain connects them |

## The sky zone

A sky zone is a small sealed room, away from the playable map, that holds the sky. Surfaces
in the playable map that have the Fake Backdrop flag show the sky zone instead of their own
texture, as if the sky room were infinitely far away.

To make a sky zone:

1. Subtract a cube of 1024 to 2048 units, away from the rest of the map. Do not connect it
   to the playable space.
2. Texture its inside with the sky materials, or place sky static meshes inside it.
3. Add a SkyZoneInfo actor inside the cube (Actor Class Browser: Actor > Info > ZoneInfo >
   SkyZoneInfo).
4. In the playable map, select the surfaces that must show the sky. Press F5 and set the
   Fake Backdrop flag.
5. Run a geometry build.

The SkyZoneInfo's position is the camera position in the sky room. Its rotation turns the
sky. Every zone in the map links to the same SkyZoneInfo at load time.

Every retail map has a sky zone. In BioShock the sky zone holds the ocean and the city
lights that you see through the windows.

**NOTE:** Light rays pass through Fake Backdrop surfaces in the lighting build. A sunlight
that shines through a Fake Backdrop window lights the room behind it. See
[14-Lighting.md](14-Lighting.md).

## Antiportals

An antiportal is a solid occluder. The game does not draw anything that is fully behind an
antiportal from the camera's point of view. Use antiportals inside large solid objects
that block the view: a thick wall, a large pillar, a machine.

To add one, shape the builder brush to fit inside the solid object and use Brush > Add
Antiportal. The antiportal must be fully inside solid geometry or inside a static mesh.
An antiportal that sticks out hides things the player must see.

Zone portals give you visibility between rooms. Antiportals give you visibility inside a
large room. Most BioShock maps need zone portals first.

## Volumes

A volume is an actor with a brush shape that marks a region of space. Volumes do not cut
the BSP and do not make zones. A volume can overlap zones.

To add a volume:

1. Shape and place the builder brush.
2. Right-click the Volume button in the button bar and select the volume class.
3. The volume appears with the builder's shape. Select it and press F4 for its properties.

Volume classes a mapper uses:

| Class | Use |
|---|---|
| BlockingVolume | Blocks movement without drawing anything. bClassBlocker and BlockedClasses limit the block to listed classes. |
| PhysicsVolume | Changes physics in the region: Gravity, ZoneVelocity, GroundFriction, TerminalVelocity, DamagePerSec and DamageType, bPainCausing, bWaterVolume, Priority for overlaps |
| FluidVolume | BioShock's water volume. See [15-Water-and-Fluids.md](15-Water-and-Fluids.md). |
| CascadingWaterVolume | A waterfall region. See [15-Water-and-Fluids.md](15-Water-and-Fluids.md). |
| PathBlockingVolume | Blocks the path build without blocking the player |

Every volume has these properties in the Volume category:

| Property | Meaning |
|---|---|
| AssociatedActorTag | The Tag of an actor linked to this volume |
| LocationPriority | Which volume's LocationName wins when volumes overlap |
| LocationName | The text the game shows for this location |

## Common problems

| Symptom | Cause | Correction |
|---|---|---|
| Two rooms show the same colour in Zone/Portal view | The portal sheet does not close the opening | Move or resize the sheet until every edge touches the BSP. Run a geometry build. |
| A room is black in Dynamic Light view | The zone has no ZoneInfo, or the ambient colours are zero | Add a ZoneInfo and set the NormalPressureAmbient colours |
| The ZoneInfo values apply to the wrong room | The ZoneInfo sits in a different zone than intended | Move the ZoneInfo. Run a geometry build. |
| The sky shows the room's own texture | The surface lacks the Fake Backdrop flag, or there is no SkyZoneInfo | Set the flag with F5. Add a SkyZoneInfo. Run a geometry build. |
| Objects vanish when the player moves | An antiportal sticks out of its solid object | Shrink the antiportal |
| Fog does not show | bDistanceFog is False | Set it to True |

## Related documents

- [05-Concepts-for-Hammer-Radiant-and-UE-Users.md](05-Concepts-for-Hammer-Radiant-and-UE-Users.md)
- [07-BSP-Geometry.md](07-BSP-Geometry.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [14-Lighting.md](14-Lighting.md)
- [15-Water-and-Fluids.md](15-Water-and-Fluids.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
