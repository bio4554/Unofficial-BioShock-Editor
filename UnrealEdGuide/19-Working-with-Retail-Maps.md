# Working with Retail Maps

This document explains how to open, edit, and re-bake the maps that ship with the game.
Read it when you want to change an existing level, or when you want to study how the
original levels are built.

## Before you start

- The content library exists in the SDK folder. See [02-Setup.md](02-Setup.md).
- `BulkContentPath` points to the game's `Content\BulkContent` folder. Without it, retail
  textures show as blank.
- You have read [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).

## The retail map files

The game's `Content\Maps` folder holds one `.bsm` file per level and one `_int.bsm`
companion file per level.

| Main map | Level |
| --- | --- |
| `0-Lighthouse.bsm` | Lighthouse and bathysphere |
| `1-Welcome.bsm` | Welcome to Rapture |
| `1-Medical.bsm` | Medical Pavilion |
| `2-Fisheries.bsm` | Neptune's Bounty |
| `2-SubBay.bsm` | Smuggler's Hideout |
| `3-Arcadia.bsm` | Arcadia |
| `3-Market.bsm` | Farmer's Market |
| `4-Recreation.bsm` | Fort Frolic |
| `5-Hephaestus.bsm` | Hephaestus |
| `5-Ryan.bsm` | Rapture Central Control |
| `6-Resi.bsm` | Olympus Heights |
| `6-Slums.bsm` | Apollo Square |
| `7-Science.bsm` | Point Prometheus |
| `7-Gauntlet.bsm` | Proving Grounds |
| `7-BossFight.bsm` | Fontaine |
| `Entry.bsm` | The front-end shell level |

The BioShock editor opens every main map. The `_int.bsm` companion files hold the voice-over data of
a level. The BioShock editor does not open them.

## Open a retail map

1. Click File > Open...
2. Browse to `<game>\Content\Maps\`.
3. Select a main map, for example `1-Welcome.bsm`.
4. Click Open.

A retail map is large. The first open compiles every shader that the map uses, so the
viewports stutter for a while. Later opens are faster.

**CAUTION:** Do not click File > Save while the map path still points into the game's
install. Click File > Save As... first and select a working folder.

### What you see

- The BSP shell of the level, with its zones and portals.
- Thousands of static mesh actors. Most of the visible detail is static meshes, not BSP.
- One SkyZoneInfo. Every retail map has a sky zone.
- Lights, ZoneInfos, volumes, PathNodes and gameplay actors.
- Skeletal mesh actors such as corpses, drawn in the pose they were placed in.

**NOTE:** A corpse's pose comes from its StartingPose property. Select the actor, expand
StartingPose in the Properties window and pick a PoseName from the drop-down. The list holds
the pose markers of the animation named in AnimationName. The viewport updates at once.

The map loads the packages it uses, so they appear in the browsers while the map is open.
The designer class packages and the door packages also exist in the content library. A
retail map also embeds a few packages that are not in the content library, such as its
prefab package.

The level scripting actors show their action lists in the Properties window, one sentence
per action. Expand a row to edit the action. See [03-Interface-Tour.md](03-Interface-Tour.md).

## Save a working copy

1. Click File > Save As...
2. Select a folder outside the game's install.
3. Keep the file name, or type a new one.
4. Click Save.

The saved copy is an editing-format map. It holds the map's own geometry and actors. Every
object that also exists in the content library becomes an import. The saved copy therefore
needs the content library to open.

## Edit the map

You edit a retail map in the same way as your own map:

- Move, add and delete actors. See [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- Place static meshes from the content library. See [10-Static-Meshes.md](10-Static-Meshes.md).
- Add and change lights. See [14-Lighting.md](14-Lighting.md).
- Change materials on surfaces. See [13-Materials-and-Textures.md](13-Materials-and-Textures.md).

### Rebuilds on a retail map

| Build step | Effect on a retail map |
| --- | --- |
| Rebuild Geometry Only | Rebuilds the BSP from the map's brushes. The result is a new BSP tree. Run it after any brush change. |
| Rebuild Lighting Only | Replaces the retail static lighting with a new lighting bake. Run it after any light change. |
| Rebuild AI Paths | Rebuilds the path network. Run it after any PathNode change. |

You do not need a geometry build for actor-only edits. The retail BSP data stays as it is.

**NOTE:** A lighting build on a retail map takes minutes. The map has hundreds of lights and
thousands of meshes.

## Bake a retail map into its own slot

Use this procedure to play your changed version of a level in place of the original.

Keep the backup copies in a separate folder. Make all three backup copies before you replace files in the game folder.

1. Back up the original `<game>\Content\Maps\<Map>.bsm`.
2. Back up the original `<game>\Content\BulkContent\Catalog.bdc`.
3. Back up the original `<game>\Content\BulkContent\<LevelName>Level.blk`.
4. Run the build steps that your changes need.
5. Click File > Bake...
6. Set Output .bsm to a staging folder and the original file name, for example
   `C:\Staging\1-Welcome.bsm`.
7. Confirm that Level name equals the map name, for example `1-Welcome`.
8. Keep Externalize checked.
9. Click Bake.
10. Copy the baked `.bsm`, the `<LevelName>Level.blk` and the merged `Catalog.bdc` into the
    game's install. See "Deploy a baked map" in [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).
11. Start a new game, or start the game on the map.

The level name must equal the slot name. The game keys the map's own textures in the bulk
catalog by that name. The wrong level name loads the map with missing textures.

## Bake a retail map into a test slot

You can bake any map into a different slot. Example: bake your edit of Fort Frolic as
`0-Lighthouse.bsm`, so that a new game starts in it.

Make backup copies of the files for the destination slot. Keep the backup copies in a separate folder.

1. Back up the original `<game>\Content\Maps\0-Lighthouse.bsm`.
2. Back up the original `<game>\Content\BulkContent\Catalog.bdc`.
3. Back up the original `<game>\Content\BulkContent\0-LighthouseLevel.blk`.
4. Set Output .bsm to `C:\Staging\0-Lighthouse.bsm`.
5. Set Level name to `0-Lighthouse`. This is the name of the destination slot.
6. Bake the map. See "Bake a retail map into its own slot" above.
7. Copy the baked files to the game folder. See "Deploy a baked map" in
   [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).

**NOTE:** Some mid-game maps do not start directly from the command line. The game normally
enters them through level travel from the previous level. If a map does not start with
`Bioshock.exe <Map>.bsm`, bake it into the Lighthouse slot and start a new game.

## Restore the original level

1. Copy the backed-up `.bsm` over the deployed file.
2. Copy the backed-up `Catalog.bdc` over the deployed catalog.
3. Copy the backed-up `<LevelName>Level.blk` over the deployed `.blk`.

Use the three backup copies for the destination slot. These must be the copies you made before you replaced the original files.

## File sizes

The retail maps are externalized bakes. Your bake of the same map is larger than the
original when Externalize is off, because every texture stays inline. With Externalize on,
your bake is close to the original size plus the size of any content you added. A map that
embeds a character package grows by the size of that package, because a character package
carries all of its skins.

## Limits

- The `_int.bsm` companion files do not open in the BioShock editor.
- A corpse whose in-game pose comes from ragdoll settling shows its authored marker pose, not
  the settled one. Models attached to a posed actor are not posed.
- The BioShock editor draws the game's bloom and streaks from the level's `GlowSettings` (switch
  them per viewport with the viewport menu's View > Post Processing item). Colour grading, the
  post-effect stack and scripted glow flashes are not drawn; judge those in the game.

## Related documents

- [02-Setup.md](02-Setup.md)
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [10-Static-Meshes.md](10-Static-Meshes.md)
- [13-Materials-and-Textures.md](13-Materials-and-Textures.md)
- [14-Lighting.md](14-Lighting.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [36-Troubleshooting.md](36-Troubleshooting.md)
