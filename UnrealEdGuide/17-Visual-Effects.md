# Visual Effects

This document tells you how to put effects in a map: bubbles, drifting brine, water splashes
and foam, drips, steam, mist, fire, sparks and insects. It also covers the decal projectors
that put grime and stains on surfaces. Read it if a room needs atmosphere, if a cascade needs
a splash at its foot, or if an effect does not look right in the game.

Water surfaces, fluid volumes and caustics are in [15-Water-and-Fluids.md](15-Water-and-Fluids.md).
The effects that the game spawns by itself, such as weapon hits and plasmid impacts, are not
placed in the editor and are not covered here.

**CAUTION:** None of the game's gameplay systems has been extensively tested on custom maps.
Test every setup in the game.

## Before you start

- The content library is loaded. `FXClass.pkg` holds the ready-made effects.
- Know how to open a package in the Actor Class browser and add an actor from it. See
  "Where the placeable classes are" in [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- Know how to open the Properties window (select an actor, press F4).
- Know the Realtime Preview button and the Dynamic Light view mode. See
  [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md).

## How effects work

An effect is an Emitter actor. The actor holds a list of sub-emitters in its `Emitters`
property. Each sub-emitter spawns particles: flat sprites that always face the camera, or small
meshes. Each particle lives for a set time, moves, changes size and colour, and dies. The
sub-emitter's material gives the particles their look.

`FXClass.pkg` holds hundreds of ready-made effects. Each one is a class with its sub-emitters
filled in. You place the class, and the effect plays. The retail maps place these classes for
almost all of their effects. Build an effect from an empty Emitter actor only when no class in
the package does what you need.

The package also holds the effects that the game spawns by itself: muzzle flashes, tracers,
plasmid impacts, blood, teleport mists, overlays and lights. Those classes are not meant to be
placed. The tables in this document list the classes that are.

Particles are lit by the lights in the room unless the actor has `bUnlit` on. A lit effect
takes the colour of the room's lights. The actor's `bUnlit` property shows which kind a
placed effect is.

## Place an effect

1. In the Actor Class browser, click File > Open Package and open `Content\FXClass.pkg`.
2. Expand Actor > Emitter. The fires and flame jets are one level deeper, under
   DamageEmitter.
3. Select the class. Use the tables under "Which effect to place" to choose one.
4. Right-click the floor, wall or water surface in a viewport where the effect belongs, and
   click Add <ClassName> Here.
5. Turn on Realtime Preview in a Dynamic Light viewport. The effect plays.
6. Move the actor into place. For a jet, rotate the actor: the jet fires along the actor's
   rotation.
7. Build > Rebuild Lighting Only, then test with Play Map.

**NOTE:** An effect placed on a surface starts at the actor's location. Most effects rise or
spread from there. Put a splash at the foot of the water, a steam cloud at the vent, a mist bed
at floor level, and a brine cloud in the middle of the space it fills.

## Which effect to place

The classes below are the ones the retail maps use most. Names with a level prefix (`Eng_`,
`Int_`, `Rec_`, `Wel_`, `Fis_`, `Ry_`, `Sci_`, `Arc_`) are tunings that one level's team made.
They work anywhere, but start with the general classes.

### Water

| Class | What it looks like | Where to put it |
|---|---|---|
| `BubblesA` | A steady rise of bubbles | Flooded rooms and under water. The most placed effect in the game |
| `BubblesRising` | A taller rise of bubbles | Deep water, vents, leaks under water |
| `Bubbles_512x_128`, `FishBubbles` | Bubble variants: a wide sheet, a small trail | Wide vents; fish tanks |
| `BrineOmni` | A medium cloud of floating specks. The look of the ocean floor | Rooms and zones that are in the ocean, usually seen through a window |
| `BrineSparse_1024x_512` | The same specks, far fewer of them | Large ocean spaces where one dense cloud is too much |
| `WaterSplash_Small`, `WaterSplash_SmallPP`, `WaterSplashSpray_Small` | A horizontal line of splashes at the foot of falling water. The three differ in colour, speed and height | On the floor at the foot of a cascade or wall sheet. See "Place falling water" in document 15 |
| `Splash_Trickle`, `Splash_SmallStream` | Smaller splashes | At the foot of a thin leak or stream |
| `FoamIntro` | A wide horizontal line of water foam | At the foot of a waterfall or cascade |
| `Foam_Gen` | The same foam, not as wide | At the foot of a narrower cascade |
| `Splash_Foam` | Foam as wide as `Foam_Gen`, with bigger splashes | At the foot of a heavy cascade |
| `Drips_Gen` | Drips that fall from the actor | Ceilings and pipes. Put the actor at the drip source |
| `WaterDripSplash`, `WaterDrip_256`, `WaterDrip_512` | Drips with a splash where they land, in two fall heights | Ceilings over a floor or a puddle |

### Steam, mist and dust

| Class | What it looks like | Where to put it |
|---|---|---|
| `Steam_LightRising` | A small rising cloud of steam | Pipes, vents, machines |
| `EngSteam_1` | A large rising cloud of steam | Big vents and machine rooms |
| `Eng_CoreSteamJet` | A small, fast, dark jet of steam | A burst pipe or valve. Rotate the actor to aim the jet |
| `SteamLeakSmall`, `SteamLeakBig`, `SteamLeakTiny` | Leak jets in three sizes | Pipe joints and valves |
| `MistLow` | A short, wide bed of mist with some movement. One actor covers about 512 by 512 units, 256 units high | Floors, water surfaces, cold rooms. Place one per 512 units of floor and add more where the mist thins out |
| `DustHanging_400x_400` | Hanging dust that drifts slowly | Rooms with a beam of light through them |
| `Int_DustMotes`, `Rec_DustMotes` | Finer dust | Quiet interiors |

### Fire

| Class | What it looks like | Where to put it |
|---|---|---|
| `Fire_Big` | A large fire with flames and smoke | Burning wreckage, a large brazier |
| `Fire_Mid` | A medium fire | Small fires, burning furniture |
| `Fire_LargeFlame` | A tall flame without the extras of `Fire_Big` | Where flame is wanted but not the full effect |
| `FlameJet` | A jet of flame | A burst gas pipe. Rotate the actor to aim it |
| `EmberFloat_50x_50`, `Rec_GrateEmbers` | Floating embers | Above a fire or a grate |

`Fire_Big`, `Fire_Mid` and `FlameJet` are damage emitters. They burn the player and anything
else that touches them. Their damage settings are in the actor's `SimpleDamageData` property.
`Fire_LargeFlame` and the embers are plain effects and do no harm.

### Sparks

| Class | What it looks like | Where to put it |
|---|---|---|
| `SparksSmall` | Dense, fast orange sparks, like a welding torch | Broken machinery, cut cables |
| `SparksMid` | A larger spark shower | Larger breaks |
| `Welcome_LiveWireSparking`, `Welcome_LiveWireSparkB` | Blue electrical sparks, the kind on a door panel that needs Electro Bolt | Live wires, broken panels, junction boxes |
| `SecurityCameraSparks` | Sparks for a damaged camera | A broken security camera |

### Insects and small life

| Class | What it looks like | Where to put it |
|---|---|---|
| `Fireflies_Area` | A medium area of yellow fireflies | Gardens and greenhouses |
| `Flies` | A small area of black flies | Corpses, garbage |
| `Insect_Apiery` | A small, short, fast swarm | Beehives |
| `Fis_RosePetals`, `Rec_RosePetals` | Falling petals | Under trees and arbours |

## Adjust an effect

Every placed effect keeps the properties of its class. Change them on the actor when the
default does not fit the room.

### Actor properties

1. Select the actor and press F4.
2. Open the Display category for the look of the whole effect, and the Emitter category for
   the sub-emitters.

| Property | Category | Function |
|---|---|---|
| `DrawScale` | Display | Scales the whole effect. Use it before you change any sub-emitter |
| `bUnlit` | Display | On: the lights in the room do not affect the particles |
| `bHidden` | Advanced | Hides the actor and its effect |
| `Emitters` | Emitter | The list of sub-emitters. Expand it to reach their properties |

### Sub-emitter properties

Expand `Emitters`, then expand one entry. The properties are grouped by category. The
properties below are the ones worth changing on a placed effect.

| Property | Category | Function |
|---|---|---|
| `Material` | Texture | The material of the particles. Effect materials are in `FX_tex.pkg` and `Gen_Water.pkg` |
| `Blending` | Texture | How the particles blend with the scene. `FB_AlphaBlend` for smoke, steam, mist and dust. `FB_Translucent` adds light: fire, sparks, foam |
| `ParticlesPerSecond` | Spawning | How many particles start each second. `MaxParticles` in the General category shows the cap and cannot be changed |
| `LifetimeRange` | Time | How long each particle lives, in seconds |
| `StartSizeRange` | Size | The size of a new particle, in units |
| `StartVelocityRange` | Velocity | The speed and direction of a new particle |
| `Acceleration` | Acceleration | A constant push on every particle. Negative Z makes drops fall, positive Z makes steam rise |
| `ColorScale` | Color | The colour and transparency of a particle over its life |
| `SizeScale` | Size | The size of a particle over its life |
| `FadeIn`, `FadeInEndTime`, `FadeOut`, `FadeOutStartTime` | Fading | Fades a particle in after it starts and out before it dies |
| `BackFadeDistance` | Fading | Softens the particles where they meet walls, floors and props. Larger values soften more |
| `SpinParticles` | Rotation | Rotates each particle over its life |
| `CoordinateSystem` | General | `PTCS_Relative`: the particles follow the actor when it moves. `PTCS_Independent`: they stay where they were spawned |
| `RelativeWarmupTime` | Warmup | Runs the effect ahead before it is first seen, so it does not start empty |
| `Disabled` | Local | Turns one sub-emitter off without deleting it |

**NOTE:** The particle count of a placed effect is a balance between look and speed. When a
room has many effects, lower `ParticlesPerSecond` on the ones farthest from the player's path.

## Decals

A decal is a Projector actor that projects a material onto the surfaces in front of it. Use
decals for grime, stains and gore. Caustics are the same kind of actor; see "Caustics" in
[15-Water-and-Fluids.md](15-Water-and-Fluids.md).

| Class | What it looks like | Where to put it |
|---|---|---|
| `GrimeHeavy_Decal` | A dark grime stain | Floors under leaks, walls behind machines, corners |
| `GoreLight_Decal`, `GoreHeavy_Decal` | Blood stains, light and heavy | Around corpses and in fights |

### Add a decal

This procedure has not been tested on a custom map.

1. In the Actor Class browser, click File > Open Package and open `Content\FXClass.pkg`.
2. Expand Actor > Projector and select the class.
3. Right-click the surface in a viewport and click Add <ClassName> Here.
4. Move the actor a short distance off the surface. The projection points along the actor's
   rotation. Rotate the actor so that it points at the surface.
5. Press F4 and set the properties in the table below.
6. Test in Dynamic Light mode. The decal draws on BSP and on static meshes whose material
   accepts projectors.

| Property | Function |
|---|---|
| `Material` | The material that is projected |
| `MaxTraceDistance` | How far in front of the actor the projection reaches, in units |
| `FOV` | The width of the projection cone in degrees. 0 gives a parallel projection |
| `DrawScale` | The size of the decal |
| `Tile` | Repeats of the material across the decal |
| `MaterialBlendingOp` | How the decal blends with the surface it lands on |
| `bProjectOnBackfaces` | On: projects on faces that point away from the projector |

A surface takes a decal when its material has `AcceptProjectors` on. Water and glass
materials have it off. A static mesh actor also needs `bAcceptsProjectors` on.

## Build an effect from an empty Emitter

Use this when no class in `FXClass.pkg` fits. This procedure has not been tested on a custom
map.

1. In the Actor Class browser, expand Actor and select Emitter.
2. Right-click in a viewport and click Add Emitter Here.
3. Press F4. Open the Emitter category and select `Emitters`.
4. Add an entry and choose SpriteEmitter. Choose MeshEmitter for particles that are small
   meshes, such as debris.
5. Expand the entry. Set `Material` in the Texture category. For a MeshEmitter, set
   `StaticMesh` in the Mesh category instead.
6. Set `ParticlesPerSecond`, `LifetimeRange`, `StartSizeRange` and `StartVelocityRange`.
7. Turn on Realtime Preview and adjust until the effect looks right.

A faster way is to place the closest class from `FXClass.pkg` and change its sub-emitters.

## What the BioShock editor shows

- Effects play in a viewport with Realtime Preview on. Other viewports show one still frame
  of the effect.
- Particles are lit by the room's lights and sorted with the scene. They show in reflective
  water.
- An effect that emits meshes shows its meshes, lit like the static meshes around it.
- An effect whose particles do not respawn, such as debris that fades out, plays once and
  then shows nothing until the map is reloaded.
- The effects that the game spawns on events do not play in the editor.
- Cubemap captures do not include effects.

## Troubleshooting

| Symptom | Cause and correction |
|---|---|
| The effect is not in the Actor Class browser | `FXClass.pkg` is not open. Click File > Open Package and open it |
| The effect does not move in the viewport | Realtime Preview is off in that viewport |
| The effect is black or very dark | The room has no light on it and the actor is lit. Add a light, or turn `bUnlit` on |
| Sparks or fire show as squares | `Blending` on the sub-emitter is `FB_AlphaBlend`. Set it to `FB_Translucent` |
| Smoke, steam or mist is a hard-edged sheet where it meets a wall | `BackFadeDistance` is 0. Set it to 20 or more |
| A jet points the wrong way | Rotate the actor. Jets fire along the actor's rotation |
| The effect is the wrong size | Change `DrawScale` on the actor before you change any sub-emitter |
| The effect played once and is now empty | Its particles do not respawn. Reload the map to see it again |
| The effect is missing in the game after a bake | The actor is inside a zone the player never sees, or `bHidden` is on |
| The player takes damage near a fire | `Fire_Big`, `Fire_Mid` and `FlameJet` burn. Use `Fire_LargeFlame` for a flame that does no harm |
| A decal does not draw on a prop | The prop's material has `AcceptProjectors` off, or the actor has `bAcceptsProjectors` off |
| A decal draws on the wrong surface | The projector points the wrong way, or `MaxTraceDistance` reaches past the intended surface |

## Related documents

- [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [13-Materials-and-Textures.md](13-Materials-and-Textures.md)
- [14-Lighting.md](14-Lighting.md)
- [15-Water-and-Fluids.md](15-Water-and-Fluids.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)
