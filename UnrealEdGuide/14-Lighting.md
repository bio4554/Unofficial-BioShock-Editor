# Lighting

This document describes the BioShock lighting system and how you light a map in the
BioShock editor. It is the reference for Light actor properties, for the static lighting
bake, and for the receiver settings on surfaces and static mesh actors. Read it if you
place lights, if you tune the look of a room, or if lighting in the game does not match
the BioShock editor.

The system is different from VRAD (Source) and from Lightmass (Unreal Engine 4 and 5).
There is no radiosity and no bounce light. The bake stores only how much of each light
reaches each point. The colour of the light and the shading of the surface are applied
when the frame is drawn.

## Before you start

- Build the geometry of the map at least once (Build > Rebuild Geometry Only).
- Know how to open the Properties window (select an actor, press F4).
- Know how to select a surface and open Surface Properties (press F5).
- Read [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md) for the view modes.

## The lighting model

BioShock lighting has three layers.

| Layer | When it is computed | What it contains |
| --- | --- | --- |
| Static bake | In the BioShock editor, during a lighting build | For each light and each receiver point: one value from 0 to 1. The value is falloff x cone x visibility. |
| Live shading | Every frame, in the game and in the BioShock editor | The light colour, the surface normal (with normal maps), and specular. |
| Runtime effects | Every frame, in the game | Zone ambient light, dynamic lights, and shadow-map shadows. |

A receiver point is one vertex of a static mesh, or one texel of a BSP surface lightmap.

The bake does not store colour. It does not store the angle between the light and the
surface. It does not store bounce light or ambient light. The game multiplies the baked
value by the light colour and by the surface shading when it draws the frame.

**NOTE:** The editor applies LightColor and LightBrightness changes when it draws the frame.
These changes do not alter the stored value for an individual light and receiver point.
But they can change which lights the lighting build selects for MaxLightsStatic.
The cubemap captures can also show the old scene appearance.

### Which edits need a lighting build

The table gives the requirements before a map bake. A visible change in the viewport does not confirm that stored data is current.
Rebuild Lighting Only updates static lighting and then captures the cubemaps.

| Edit | Run Rebuild Lighting Only before the map bake |
| --- | --- |
| Move a light, or rotate a spot, sun or directional light | Yes |
| Change LightRadius, LightCone, LightWidth or LightEffect | Yes |
| Add or delete a light | Yes |
| Change SpecialLitChannel on a light or on a receiver | Yes |
| Change MaxLightsStatic on a receiver | Yes |
| Move, rotate, add, delete or scale geometry or a static mesh actor | Yes. Receivers need new lighting even if they do not block light. |
| Assign a different mesh to a static mesh actor | Yes |
| Change bCastStaticShadow, bCollideActors or bBlockZeroExtentTraces on a static mesh actor | Yes |
| Change the lightmap scale of a surface | Yes |
| Change LightColor or LightBrightness | Yes. The selected lights and cubemap captures can change. |
| Change LightType, LightPeriod or LightPhase | Yes. This clears the edited light's changed state and updates cubemap captures. |
| Change ZoneInfo ambient colors or fog | Yes, if the change affects the scene appearance in cubemap captures. |
| Change a material | Yes, if the change affects light blocking or the scene appearance in cubemap captures. |
| Add, move or delete a CubemapProbe | Yes, after a geometry rebuild. The geometry rebuild assigns probes to BSP leaves; the lighting build then captures the cubemaps. |

**CAUTION:** After any light edit, run Build > Rebuild Lighting Only before you bake the map.
The lighting build clears the light's changed state. A map bake does not replace the lighting build.
The game skips static lighting from lights that still have this state set.

A material change can change whether a static mesh blocks baked light. See "Visibility" below.
Appearance changes include changes to light colors, brightness, materials, textures and ambient light.
If these change the captured scene, update the cubemaps before you bake the map.
Keep a perspective viewport open during the lighting build so that the editor can capture the cubemaps.
See "Cubemaps" in [13-Materials-and-Textures.md](13-Materials-and-Textures.md).

### The MaxLightsStatic cap

The number of static lights that a receiver takes depends on the type of receiver.

- A static mesh actor takes up to MaxLightsStatic lights. MaxLightsStatic is a property
  in the Display category of every actor. The default is 3. The retail maps keep the
  default on most actors, and set values from 0 to 12 on the rest. A value of 1 or 2
  is common on small clutter meshes. A value above 3 is used on large meshes that many
  lights reach.
- A BSP surface has no cap. The lighting build stores every light that reaches the
  surface.

**NOTE:** The game stores and draws baked light in groups of three lights. The first
group is free. Each further group costs one more draw of the receiver. This is why the
default is 3, and why you should raise MaxLightsStatic only on the actors that need it.

The bake selects the lights for a receiver in this order:

1. It keeps only the lights whose SpecialLitChannel matches the receiver (see
   "Special lit channels").
2. It keeps only the lights whose shape (sphere, cone or beam) reaches at least one point of the receiver.
3. It keeps only the lights that reach at least one point through the geometry.
4. It sorts the remaining lights by strength at the receiver, with sunlights first.
5. It keeps the first MaxLightsStatic lights.

**NOTE:** Every Light actor in the map is a candidate, including lights with LightType set
to LT_None. Delete the lights you do not need, so that they do not use one of the
MaxLightsStatic slots.

### No bounce light

The bake has no radiosity. A room lit by one lamp has a hard black shadow behind every
object, and the ceiling stays black. Retail maps fill the dark areas with these methods:

- The zone ambient colour (see "Ambient light").
- Extra Light actors with a low LightBrightness and a large LightRadius.
- Emissive materials on lamp meshes and signs.
- Light beam meshes from the content library, which fake volumetric light.

## Add a light

1. In a viewport, right-click a surface or the backdrop.
2. Click Add Light here.
3. Select the light. Press F4 to open the Properties window.
4. Set the values in the Lighting category.
5. Run Build > Rebuild Lighting Only.

A new Light actor has these defaults:

| Property | Default |
| --- | --- |
| LightEffect | LE_Pointlight |
| LightType | LT_Steady |
| LightBrightness | 0.35 |
| LightColor | R=255, G=255, B=255 |
| LightRadius | 1024 |
| LightCone | 128 |
| LightWidth | 255 |

A Light actor is static, hidden in the game, and cannot be deleted by the game.

### Aim a light

The rotation of a Light actor sets the direction of a spot light, a sun light and a
directional light. A point light has no direction. To aim a light, use one of these
methods:

- Set Rotation in the Movement category of the Properties window. A full turn is 65536.
  Pitch -16384 points straight down.
- Rotate the actor with the mouse. See [38-Keyboard-and-Mouse-Reference.md](38-Keyboard-and-Mouse-Reference.md).
- Move the perspective camera to the position and direction you want. Right-click the
  light and click Set > Location/Rotation from Camera.

## Light properties

The tables mark each property as Bake or Live. A Bake property changes the stored lighting values.
The renderer applies a Live property every frame. Live does not mean that you can omit the lighting build.
LightColor and LightBrightness also affect light selection during the build.
Use "Which edits need a lighting build" above to select the required rebuilds.

### Lighting category

| Property | Bake or Live | Description |
| --- | --- | --- |
| LightEffect | Bake | The shape of the light. See "Light effects". |
| LightType | Live | On, off, or an animation. See "Light types". |
| LightBrightness | Live | Strength of the light, 0 to 5. The default is 0.35. |
| LightColor | Live | Colour of the light as R, G, B. The alpha value is not used. |
| LightRadius | Bake | Distance in units at which the light reaches zero. The default is 1024. |
| LightCone | Bake | Width of a spot light cone, 0 to 255. See "Spot cone". |
| LightWidth | Bake | Width of the beam of an LE_Directionallight, in units. Other light effects do not use it. The default is 255. |
| LightPeriod | Live | Length of one cycle for an animated LightType. |
| LightPhase | Live | Offset into the cycle for an animated LightType. |
| bSpecialLit | Live | The light affects only special lit surfaces and actors. See "Special lit channels". |
| bCastsShadowMapShadows | Live | The light casts shadow-map shadows in the game. See "Dynamic lights and shadow maps". |
| bDynamicLight | Live | The light is a dynamic light. |
| bImportantDynamicLight | Live | The game gives this dynamic light priority. |
| bOnlyAffectBase | Live | The light affects only the actor it is attached to. |
| bCorona | Live | The light draws a corona sprite. See "Corona category". |
| bDirectionalCorona | Live | The corona is larger when the light faces the camera. |
| bAttenByLife | Live | The light fades as its lifespan runs out. Used by effects, not by placed lights. |
| bLightingVisibility | Live | The game tests the line of sight to this light with traces. |
| bActorShadows | Live | Stock Unreal Engine 2 property. **NOTE:** Not verified for BioShock. Leave it off. |
| MaxTraceDistance | Live | **NOTE:** Not verified. Leave the default. |
| MergeDynamicLightGroup | Live | Read-only. Groups dynamic lights for fire, electricity and explosions. |
| ShadowProjectionMask | Live | A texture that the light projects as a shadow mask. The game uses it for security camera and searchlight cones. |
| TextureLoopPeriodMultiplier | Live | Speed of the texture-palette light types. |

The effective colour of a light is LightColor divided by 255, multiplied by
LightBrightness. A white light at LightBrightness 1.0 gives full strength at the light
position. A red light with R=255, G=0, B=0 and LightBrightness 0.5 gives half strength
red.

**NOTE:** The LightColor category also shows LightHue and LightSaturation. They are legacy
values from Unreal Engine 2. BioShock uses LightColor. Leave LightHue and LightSaturation
at their defaults.

### Light effects

LightEffect selects one of four shapes.

| LightEffect | Shape | Uses the rotation | Uses LightRadius | Uses LightCone |
| --- | --- | --- | --- | --- |
| LE_Pointlight | A sphere around the light. Full strength at the centre, zero at LightRadius. | No | Yes | No |
| LE_Spotlight | A cone along the rotation direction. Full strength on the axis, soft at the edge. | Yes | Yes | Yes |
| LE_Sunlight | Parallel rays that travel along the rotation direction. No falloff. Infinite range. | Yes | No | No |
| LE_Directionallight | A beam of parallel rays along the rotation direction. The beam is a cylinder, LightWidth wide and LightRadius deep. The strength is full at the light and zero at the far end. The edge of the beam is soft. | Yes | Yes, as the depth of the beam | No |

A sun light can be placed anywhere in the map. Only its rotation matters. A sun light
lights every point that has a clear line to the sky along the ray direction. See
"Sunlight and the sky".

### The light shape gizmo

When you select a light, every viewport draws the shape of the light in the light colour.
The shape is always visible for a selected light. Radii View is not necessary.

| LightEffect | What is drawn |
| --- | --- |
| LE_Pointlight | A sphere with the radius LightRadius. An ortho view shows one circle. |
| LE_Spotlight | The cone at the LightCone half angle. The LightRadius sphere closes the cone. |
| LE_Directionallight | The beam. Two discs, LightWidth wide and LightRadius apart, with lines between them. Arrows show the ray direction. |
| LE_Sunlight | The direction arrow only. |

The shape changes when you change a property in the Properties window. Use it to set
LightRadius, LightCone and LightWidth. The cone has the half angle from the table under
"Spot cone". The edge of the sphere is where the falloff reaches zero. The viewports draw
the shape of a very dark light in white.

### Light types

LightType turns the light on and off, or animates it.

| LightType | Behaviour |
| --- | --- |
| LT_None | The light is off. |
| LT_Steady | The light is on at a constant level. This is the default. |
| LT_Pulse | The level rises and falls in a smooth cycle. |
| LT_Blink | The light switches on and off. |
| LT_Flicker | The level changes at random. |
| LT_Strobe | The light flashes. |
| LT_BackdropLight | Stock Unreal Engine 2 value. **NOTE:** Not verified for BioShock. |
| LT_SubtlePulse | A weaker pulse. |
| LT_TexturePaletteOnce | The level follows a texture palette one time. |
| LT_TexturePaletteLoop | The level follows a texture palette in a loop. |
| LT_FadeOut | The light fades out. |

LightPeriod sets the length of one cycle. LightPhase offsets the cycle, so that two lights
with the same LightPeriod do not pulse together.

The animation is applied live. It scales the light colour. The static bake is the same
for every LightType.

### Spot cone

LightCone is a byte from 0 to 255. It sets the half angle of the cone.

cos(half angle) = 1 - LightCone / 255

| LightCone | Half angle | Full angle |
| --- | --- | --- |
| 32 | 29 degrees | 58 degrees |
| 64 | 41 degrees | 82 degrees |
| 96 | 51 degrees | 102 degrees |
| 128 | 60 degrees | 120 degrees |
| 160 | 68 degrees | 136 degrees |
| 192 | 76 degrees | 152 degrees |
| 224 | 83 degrees | 166 degrees |

The default of 128 is a wide cone. Use 32 to 64 for a lamp or a security spotlight.

The cone has a soft edge. The strength is full on the axis and falls to zero at the edge
of the cone. Points behind the light get no light.

### Corona category

A corona is a sprite drawn at the light position when the camera can see it. Set bCorona
in the Lighting category and set the Texture property of the light to the corona texture.

| Property | Description |
| --- | --- |
| MinCoronaSize | Smallest size of the corona sprite. |
| MaxCoronaSize | Largest size of the corona sprite. |
| CoronaRotation | Rotation of the sprite. |
| CoronaRotationOffset | Start rotation of the sprite. |
| UseOwnFinalBlend | The sprite uses its own blend mode. |

**NOTE:** Not verified: the BioShock editor does not show coronas in the lit view modes.

### Pressure category

The game has a pressure system that changes the mood of a level. The Pressure category
holds a low set and a high set of light values (LowLightBrightness, LowLightColor,
LowLightType, LowLightPeriod, LowLightPhase, and the High equivalents). PressureEffect
selects whether the light follows the pressure system. Leave these properties at their
defaults unless you script the pressure system. The pressure system is not covered in
this guide.

## The static bake

The lighting build computes one value for each light and each receiver point:

value = falloff x cone x visibility

Each factor is between 0 and 1. The value is stored with 8 bits, so a value below 1/255
is stored as zero.

### Falloff

Point lights, spot lights and directional lights use the same falloff curve. R is
LightRadius. For a point light and a spot light, d is the distance from the light to the
point. For a directional light, d is the depth of the point along the beam.

falloff = (1 - saturate(d^2 / R^2))^2

| d / R | Falloff |
| --- | --- |
| 0.00 | 1.00 |
| 0.25 | 0.88 |
| 0.50 | 0.56 |
| 0.75 | 0.19 |
| 1.00 | 0.00 |

The falloff is smooth and reaches exactly zero at the radius. Half of the strength is
gone at about half of the radius. A light with a large radius and a low brightness
spreads its 8-bit precision over a large volume, so the far part of the volume is black.

A directional light is a beam of parallel rays. The falloff runs along the beam. The
strength is full at the light and zero at LightRadius depth. The beam is a cylinder,
LightWidth wide, with a soft edge. Points behind the light get no light. Points beside the
beam get no light.

### Cone

A spot light multiplies the falloff by the cone factor. The factor is 1 on the axis. It
falls to zero at the half angle set by LightCone. The edge is soft, and the softness
scales with the cone width.

### Sunlight and the sky

A sun light stores only the visibility. It sends one parallel ray from each receiver point
in the direction opposite to the light rotation. If the ray reaches a surface that has the
Fake Backdrop flag, the point is lit. If the ray hits any other geometry, the point is in
shadow.

To use a sun light:

1. Build the outside of the map as a sky box or a sky zone.
2. Open Surface Properties (F5) for the sky surfaces and set the Fake Backdrop flag.
3. Add a Light actor and set LightEffect to LE_Sunlight.
4. Rotate the light so that it points in the direction the sun shines. Pitch -16384 is
   straight down.

**NOTE:** A sun light with a Pitch of 0 shines horizontally. A window wall in the ray path
shadows the whole room.

### Visibility

The bake traces a ray from each receiver point to the light. For a sun light and a
directional light, the rays are parallel and run against the light direction. A sun ray
ends at the sky. A directional ray ends at the light. These items block the ray:

- BSP surfaces, except Fake Backdrop surfaces.
- Static mesh actors that have collision, that block zero-extent traces, and that have
  bCastStaticShadow set. Static mesh actors have all three set by default.
- The receiver itself. A mesh shadows its own back faces.

These items do not block the ray:

- Surfaces of a static mesh whose material is a LightBeamShader, a PlantShader, a
  FinalBlend, a ConstantColor, or a Shader with an Opacity mask. Light beams, foliage and
  cut-out glass let light through.
- Static mesh actors with bCastStaticShadow off, or with collision off.
- Brush-based movers. The bake traces the world BSP and the placed static meshes only.
- Skeletal mesh actors and other actors without a static mesh.

The shadow edge is sharp. A small filter softens it slightly. There is no adjustable
shadow radius.

### Static mesh receivers

The bake samples each triangle of a static mesh at several points and stores the average
at each vertex. The result has these properties:

- The shadow detail is limited by the vertex density. A large floor mesh with four
  vertices shows one flat value per vertex, and a lamp above the centre lights the corners
  as much as the centre. Use BSP for large floors and walls, or use a mesh with more
  vertices.
- Small props with many vertices get good self-shadowing.

These properties on a static mesh actor control how it receives light. They are in the
Display category unless noted.

| Property | Default | Description |
| --- | --- | --- |
| bStaticLighting | True | Stock Unreal Engine 2 flag. The bake lights every static mesh actor and does not read it. Keep it set. |
| bUnlit | False | Lights do not affect the actor. The BioShock editor and the game draw it at full brightness, with no shading. |
| bShadowCast | True | Stock Unreal Engine 2 flag for static shadows. Keep it set. |
| bCastStaticShadow | True | The actor blocks rays in the bake. Clear it to let light through a mesh. |
| MaxLightsStatic | 3 | Maximum number of static lights on the actor. |
| MaxLightsDynamic | 3 | Maximum number of dynamic lights on the actor. |
| SpecialLitChannel | 0 | Lighting channel. See "Special lit channels". |
| bUseDynamicLights | True | Dynamic lights affect the actor. |
| bUseLightingFromBaseWhenAttached | True | An attached actor takes the lighting of its base. |
| LightMapScale | LMS_Default | Per-actor lightmap scale enum. **NOTE:** Not verified: the static mesh bake is per vertex and does not use this value. |
| bAcceptsProjectors | True | Projected decals draw on the actor. |
| IgnoreLight | None | One light that the actor ignores. **NOTE:** Not verified in the bake. |
| bForceStaticLighting | False | **NOTE:** Not verified. |

### BSP surface receivers

The bake stores one value per lightmap texel for each BSP surface. The texel size is the
lightmap scale of the surface.

To set the lightmap scale:

1. Select one or more surfaces.
2. Press F5 to open Surface Properties.
3. Open the Pan/Rot/Scale page.
4. Select a value in the Lightmap scale list.

The list offers 1, 2, 4, 8, 16, 32, 64, 128 and 256 units per texel. The default is 32
units per texel. A smaller value gives sharper shadows and uses more lightmap space.

The lightmaps are packed into atlas textures of 1024 by 1024 texels. One surface tile is
limited to 512 texels on each axis. A surface that needs more texels is scaled down to
512, and its shadows become blurred. Keep each surface below 512 times the lightmap scale
on each axis. With the default scale of 32, that is 16384 units, so the limit matters only
when you use a scale of 4 or smaller on a large surface.

| Lightmap scale | Use |
| --- | --- |
| 64 to 256 | Large ceilings and distant walls with soft light. Saves lightmap space. |
| 32 | Default. Good for most walls and floors. |
| 8 to 16 | Floors under lamps, stairs, and detail where a sharp shadow matters. |
| 1 to 4 | Small trim surfaces only. A large surface at this scale blurs at the 512 limit. |

The Flags page of Surface Properties has these lighting flags:

| Flag | Effect |
| --- | --- |
| Unlit | The surface is drawn at full brightness. It gets no lightmap. |
| Special Lit | The surface takes only special lit lights. See "Special lit channels". |
| Fake Backdrop | The surface is the sky. It gets no lightmap. Sun rays pass through it. |

### Special lit channels

A lighting channel separates a group of lights from the rest of the map. The game uses
channels to light a machine or a door with its own lights.

- A light with SpecialLitChannel 0 is a normal light.
- A light with SpecialLitChannel other than 0 is a special lit light.
- A static mesh actor takes only the lights whose SpecialLitChannel is equal to its own.
  An actor on channel 0 takes only normal lights. An actor on channel 5 takes only lights
  on channel 5.
- A BSP surface with the Special Lit flag takes every special lit light. A surface
  without the flag takes only normal lights.

SpecialLitChannel is in the Display category of every actor, including Light actors. The
bSpecialLit flag in the Lighting category of a light is applied live by the game. Set both
bSpecialLit and SpecialLitChannel on a special lit light.

## Ambient light

Ambient light comes from the ZoneInfo actor of each zone, not from the bake. Open the
ZoneInfo properties (F4) and set the ZoneLight category.

| Property | Default | Description |
| --- | --- | --- |
| NormalPressureAmbientColorHigh | Black | Ambient colour on surfaces that face up. |
| NormalPressureAmbientColorHighMultiplier | 1.0 | Strength of the high colour. |
| NormalPressureAmbientColorLow | Black | Ambient colour on surfaces that face down. |
| NormalPressureAmbientColorLowMultiplier | 1.0 | Strength of the low colour. |
| NormalPressureAmbientContrastPower | 1.0 | Sharpness of the change from high to low. |
| NormalPressureAmbientXGroundRatio | 0.15 | Amount of the low colour that reaches vertical surfaces. |
| DramaticLightingScale | | Stock property. **NOTE:** Not verified for BioShock. |

The ZoneLightPressure category holds the same set for the high and low pressure states.
Leave them at their defaults unless you script the pressure system.

The ambient is a two-colour gradient. Surfaces that face up take the high colour.
Surfaces that face down take the low colour. Walls take a mix. The retail maps use a dim
blue-grey or teal high colour with NormalPressureAmbientColorHighMultiplier set to 40,
NormalPressureAmbientContrastPower 2.0, and the low colour left at its default. See
[06-BioShock-Mapping-Guidelines.md](06-BioShock-Mapping-Guidelines.md) for the values.

**NOTE:** The stock Unreal Engine 2 properties AmbientBrightness, AmbientHue and
AmbientSaturation are also in the ZoneLight category. BioShock uses the
NormalPressureAmbient properties.

A map without a ZoneInfo in a zone has no ambient light in that zone. Unlit areas are
black.

## Dynamic lights and shadow maps

A dynamic light is a Light actor with bDynamicLight set, or a light that a script or an
effect moves at run time. The game applies dynamic lights per frame to the actors near
them. Each actor takes at most MaxLightsDynamic dynamic lights. Set bImportantDynamicLight
on a dynamic light that must not be dropped when the game reaches this limit.

A light with bCastsShadowMapShadows set casts shadows from moving actors in the game. The
game renders a shadow map for that light every frame. A ShadowProjectionMask texture on
the light shapes the shadow map. Security cameras and searchlights use this.

**NOTE:** The BioShock editor does not show shadow-map shadows. Test them in the game.

The BioShock editor previews dynamic lights in the Dynamic Light view mode, but the result can
differ from the game.

## Workflow

1. Place the Light actors and set their properties.
2. Run Build > Rebuild Lighting Only. The status bar shows the progress. The bake ends
   with the cubemap step for every CubemapProbe in the map (see the Cubemaps section of
   [13-Materials-and-Textures.md](13-Materials-and-Textures.md)).
3. Set the perspective viewport to the Lighting Only view mode. Judge the placement and
   the strength of each light on white surfaces.
4. Set the viewport to Lit Untextured to see the result with normal maps and specular.
5. Set the viewport to Dynamic Light to see the game look with materials.
6. Repeat from step 1 until the result is correct.
7. Run the lighting build one more time after the last light edit. Then bake the map.

**NOTE:** The lighting build is always a full bake of the map. Build > Rebuild Changed
Lighting Only also runs the full bake.

**NOTE:** The viewports use stored static lighting between builds. A moved light uses old receiver data until the next lighting build.
Live color and brightness changes do not update that data or the cubemap captures.

Useful tools:

| Tool | Use |
| --- | --- |
| Tools > Scale Lights | Scales LightBrightness of the selected lights by a factor. |
| Edit > Search for Actors | Finds a light by name. |
| Right-click a light > Select > All Light Actors | Selects every light in the map. |
| Build > Build Options > the level statistics page | Shows the number of lights. |

## Light fixtures from the content library

The content library package Gen_Lights.pkg holds the fixture meshes of Rapture: sconces,
chandeliers, lanterns, desk lamps, ceiling lamps, wall lamps and neon signs. It also holds
light beam meshes. A light beam mesh uses a LightBeamShader material and fakes a
volumetric shaft of light.

To place a lit fixture:

1. Open the Static Mesh browser and load `Gen_Lights.pkg` with File > Open.
2. Place the fixture mesh.
3. Place a Light actor just outside the fixture mesh. A Light actor inside a closed mesh
   is blocked by the mesh.
4. Place a light beam mesh under the fixture if the fixture is a spotlight or a lamp with
   a visible cone.
5. Run the lighting build.

A light beam mesh does not block light. A fixture mesh blocks light unless you clear
bCastStaticShadow on it.

Many lamp materials in Gen_Lights.pkg carry emissive maps, so the fixture glows without a
Light actor. The Light actor lights the room around it.

## Troubleshooting

| Symptom | Cause | Correction |
| --- | --- | --- |
| A static mesh is black | No lighting build since the mesh was placed. | Run Build > Rebuild Lighting Only. |
| A static mesh draws at full brightness, with no shading | bUnlit is set. | Clear bUnlit in the Display category of the actor. |
| A static mesh is black | Its SpecialLitChannel differs from the lights. | Set the same channel on the lights and the actor, or set both to 0. |
| A static mesh is black | MaxLightsStatic is 0. | Set MaxLightsStatic to 3 or higher. |
| A static mesh is black | No light reaches it. | Increase LightRadius or move a light closer. |
| The map is lit in the BioShock editor but dark in the game | The map was baked before the last lighting build. | Run the lighting build, then bake again. |
| Shadows are blocky on BSP | The lightmap scale is large. | Set a smaller lightmap scale on the surface. |
| Shadows are wrong on a static mesh | The mesh has few vertices. | Use BSP or a mesh with more vertices. |
| Shadows are blurred on a large surface | The surface tile reached 512 texels. | Increase the lightmap scale, or split the surface. |
| A spot light is a wide blob | LightCone is too large. | Set LightCone to 32 to 64. |
| A spot light points the wrong way | The rotation is wrong. | Set Rotation, or use Set > Location/Rotation from Camera. |
| A directional light lights a thin strip only | LightWidth is small. The default of 255 is a narrow beam. | Increase LightWidth. The light shape gizmo shows the beam. |
| A directional light lights nothing | The light points away from the room. | Rotate the light. The arrows in the light shape gizmo show the ray direction. |
| A sun light lights nothing | The sky surfaces are not Fake Backdrop, or the rotation is wrong. | Set the Fake Backdrop flag on the sky. Check Pitch. |
| A light has no effect near other lights | Only the strongest MaxLightsStatic lights apply to the actor. | Remove weaker lights, or raise MaxLightsStatic on the actor. |
| Light passes through a mesh | bCastStaticShadow or collision is off on the mesh. | Set bCastStaticShadow, bCollideActors and bBlockZeroExtentTraces. |
| Light passes through a wall | The wall is a mover, or the material lets light through. | Build the wall from BSP or from a static mesh. |
| A dim light looks black far away | The 8-bit precision is used up. | Reduce LightRadius and raise LightBrightness. |
| A room has no ambient light | The zone has no ZoneInfo, or the ambient colours are black. | Add a ZoneInfo and set the ZoneLight colours. |
| The lighting build took no effect | The Lighting option is off in Build Options. | Open Build > Build Options and enable Lighting. |

## Related documents

- [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md): view modes.
- [07-BSP-Geometry.md](07-BSP-Geometry.md): surfaces and Surface Properties.
- [08-Zones-and-Portals.md](08-Zones-and-Portals.md): ZoneInfo and sky zones.
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md): the Properties window.
- [10-Static-Meshes.md](10-Static-Meshes.md): static mesh actors.
- [13-Materials-and-Textures.md](13-Materials-and-Textures.md): emissive and light beam materials.
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md): the build and bake commands.
- [36-Troubleshooting.md](36-Troubleshooting.md): other problems.
