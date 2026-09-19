# Build, Save, Bake and Play

This document explains the Build menu, the difference between a saved map and a baked map,
the Bake Map dialog, the deployment procedure, and Play Map. Every mapper needs this
document. Read it before you test a map in the game for the first time.

## Before you start

- The BioShock editor opens and shows your map.
- The SDK Manager's setup has run. It sets `BulkContentPath` in `System\BioShockSDK.ini` to
  the game's `Content\BulkContent` folder and `GameDir` in `System\UnrealEd.ini`. See
  [02-Setup.md](02-Setup.md).
- You know where your working folder is. Keep it separate from the game's `Content\Maps` folder.

## The two map formats

The BioShock editor writes two kinds of map file. A saved map is a `.unr` file. A baked map is a `.bsm` file.

| Format | Command | Contents | Use |
| --- | --- | --- | --- |
| Saved map | File > Save, File > Save As | The map's own geometry, actors and any content that has no equivalent in the content library. All other content is an import from the content library. | Editing. Keep this file as your master copy. |
| Baked map | File > Bake, Play Map | Everything the map references, embedded in one file, plus the data that the game expects a cooked map to carry. | Playing in the game. |

A saved map needs the content library to open. Do not move a saved map to a computer that
has no content library.

A baked map opens in the BioShock editor too. Keep the saved map as the master copy. Bake again from
the saved map after each change.

**CAUTION:** A bake overwrites the output file. It does not merge into an existing file.

## The Build menu

The Build menu runs the build steps that turn brushes, lights and path nodes into the data
the game uses.

| Menu item | What it does |
| --- | --- |
| Play Level (Ctrl+P) | Bakes the map into the game's install and starts the game. See "Play Map" below. |
| Rebuild Geometry Only | Runs the CSG operations of all brushes, builds the BSP tree, builds the bounds, and computes zones and portal visibility. Run this after any brush change. |
| Rebuild Lighting Only | Runs the BioShock lighting bake, then bakes the cubemaps of every CubemapProbe. This writes the static light data and the reflections that the game reads. See [14-Lighting.md](14-Lighting.md) and the Cubemaps section of [13-Materials-and-Textures.md](13-Materials-and-Textures.md). |
| Rebuild Changed Lighting Only | Runs the same full lighting bake. There is no changed-only lighting mode in the BioShock editor. |
| Rebuild AI Paths | Builds the path network from the placed PathNodes. See [26-AI-Paths.md](26-AI-Paths.md). |
| Rebuild Changed AI Paths | Rebuilds only the paths that changed. |
| Build All | Runs geometry, BSP, lighting, paths and fluids in that order. |
| Build Options... | Opens the Build Options window. See below. |

### Which build step you must run

Run every build step that applies to your changes. Run geometry before lighting, and lighting before paths.

| After this change | Run this |
| --- | --- |
| Any brush added, moved, deleted or edited | Rebuild Geometry Only, then Rebuild Lighting Only |
| Any zone portal or ZoneInfo change | Rebuild Geometry Only, then Rebuild Lighting Only |
| Any light added, moved, deleted or edited | Rebuild Lighting Only |
| Any static mesh actor added, moved, rotated, scaled, deleted or assigned a different mesh | Rebuild Lighting Only. Receivers need updated lighting even if they do not block light. |
| MaxLightsStatic or SpecialLitChannel changed on an actor | Rebuild Lighting Only |
| Surface lightmap scale changed | Rebuild Lighting Only |
| bCastStaticShadow, bCollideActors or bBlockZeroExtentTraces changed on a static mesh actor | Rebuild Lighting Only |
| A material change affects whether a mesh blocks baked light | Rebuild Lighting Only |
| Materials, textures or other appearance settings change the scene in cubemap captures | Rebuild Lighting Only |
| Any CubemapProbe added, moved or deleted | Rebuild (the zone build assigns probes to BSP leaves), then Rebuild Lighting Only |
| Any PathNode added, moved, deleted or edited | Rebuild AI Paths |
| Geometry, static meshes or collision settings change where AI can move | Rebuild AI Paths after the other required builds |
| Any GathererVent added, moved, rotated, scaled, deleted or edited | Rebuild AI Paths after the other required builds |

Generated data includes geometry, static lighting, cubemap captures and path data. Actor property changes can affect this data.
Bake directly only if your changes affect none of this data. If you are not sure, run the required builds before you bake.

The path build updates vent socket positions and the FrontPathNode used by Little Sisters.
See [26-AI-Paths.md](26-AI-Paths.md) and [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md).

The first bake of a new map always needs a geometry build. The geometry build creates the
leaf data, the zone masks and the per-leaf bounds that the bake writes.

**CAUTION:** The lighting bake clears the "light changed" state on every light. The game skips
a light that still has this state set. Run Rebuild Lighting Only after any light edit, before
you bake.

### The Build Options window

Build > Build Options... opens a window with two pages.

**Options page.** The controls, top to bottom:

| Control | Meaning |
| --- | --- |
| Configuration | A named set of build options. Select one from the list. |
| Save | Saves the current options under the name in the Configuration box. |
| Delete | Deletes the selected configuration. |
| Geometry | When checked, the Build button runs the geometry step. |
| Only Rebuild Visible Actors? | When checked, the geometry step uses only the brushes that are visible in the viewports. |
| BSP | When checked, the Build button runs the BSP step. |
| Optimization: Lame, Good, Optimal | The BSP build quality. Optimal gives the fewest cuts and the smallest tree. Keep it on Optimal. |
| Minimize Cuts ... Balance Tree slider | The BSP balance value, 0 to 100. The default is 15. |
| Ignore Portals ... Portals Cut All slider | The portal bias value, 0 to 100. The default is 70. |
| Lighting | When checked, the Build button runs the lighting bake. |
| Format, Dither?, Save as BMP? | Stock lightmap options. The BioShock lighting bake ignores them. |
| Define Paths | When checked, the Build button runs the path build. |
| Only Build Changed | Runs the path build for changed paths only. |
| Report Errors? | When checked, the build shows the map-check window when it finds a problem. |
| Build | Runs every checked step in order. |

**Stats page.** Shows the brush count, the zone count, the BSP polygon and node counts, the
tree ratio and depth, and the light count. Click Refresh to update the values.

Leave Optimization on Optimal. The three levels date from much slower computers. On a
current computer an Optimal build of a large retail map takes seconds.

## Save a map

1. Click File > Save As...
2. Select a folder outside the game's install.
3. Type a file name. The extension is `.unr`. A map you opened as a `.bsm` can be
   saved as `.bsm` again; pick that file type in the dialog.
4. Click Save.

You can open several maps in one session. File > Open and File > New drop the previous
map's embedded content before the next map loads. Packages you loaded in the browsers stay
loaded.

The BioShock editor removes brush clip markers and polygon markers before it writes the file.

**CAUTION:** Never save into the game's `Content\Maps` folder. The game does not run a
saved map. A saved map with a retail file name replaces a level of the game.

## Bake a map

The bake writes a playable map. The bake also creates data that only the game uses and
that the BioShock editor does not author:

- the level label and the level animation information,
- the registration of the spawner and the effects and sound databases for this map,
- the list of objects that the game keeps in memory for this map,
- the per-leaf lighting bounds,
- the package flags that mark the file as a cooked map.

The bake takes this data from the map, from the content library and from the `System\*Effects.ini`
files. You do not edit this data.

### Bake procedure

1. Run the build steps that your changes need. See "Which build step you must run".
2. Click File > Bake...
3. Set the fields in the Bake Map dialog. See the table below.
4. Click Bake.
5. Read `System\Editor.log`. See "Read the log after a bake".

### The Bake Map dialog

| Field | Meaning |
| --- | --- |
| Output .bsm | The path of the baked file. The default is the saved map's folder and name with the `.bsm` extension. Use a staging folder, not the game's `Content\Maps` folder. Click `...` to browse. |
| Level name | The name the game uses for this map. The default is the file name without the extension. See "The level name rule". |
| Externalize streamable textures to <Level>Level.blk + Catalog.bdc | When checked, the bake writes the large texture data to a `.blk` file next to the output and writes a merged `Catalog.bdc` next to the output. When not checked, every texture stays inside the `.bsm`. |
| Source Catalog.bdc | The catalog that the bake merges the map's texture records into. The default is the catalog in the configured bulk content folder. Click `...` to browse. |
| Keep DynamicBulkFileTextures.blk entries | When checked, textures that already exist in the game's shared dynamic texture file are not written again. Keep this checked. |

The BioShock editor stores the dialog values in `System\UnrealEd.ini` under `[Bake]`. The next bake
starts with the same values.

### The level name rule

The game keys the map's own textures in the bulk catalog by the level name. The level name
must equal the file name that the game loads, without the extension.

| You deploy the map as | Level name must be |
| --- | --- |
| `Content\Maps\MyMap.bsm` | `MyMap` |
| `Content\Maps\1-Welcome.bsm` | `1-Welcome` |

The dialog derives the level name from the output file name until you type your own level
name. If you bake to a staging file with a different name, type the deployed name in the
Level name field.

A wrong level name has this effect: the map loads, but the map's own textures do not resolve
in the game.

### Externalized bakes

An externalized bake writes three files next to the output:

| File | Contents |
| --- | --- |
| `<Output>.bsm` | The map, without the large texture data. |
| `<LevelName>Level.blk` | The large texture data of the map's own textures. |
| `Catalog.bdc` | The source catalog plus the records for the new `.blk`. |

An externalized bake is much smaller than an inline bake. You must deploy all three files.

An inline bake (Externalize not checked) writes one `.bsm` with every texture inside. It is
tens of megabytes larger. It does not change the catalog. Use it for quick tests.

### Bake several maps for the same install

Every externalized bake merges into the source catalog. If the source catalog is always the
game's original catalog, a second map's bake drops the records of the first map.

To keep the records of every map:

1. Deploy the first bake, including its `Catalog.bdc`.
2. Open `System\UnrealEd.ini`.
3. Under `[Bake]`, set `DeployCatalog` to the deployed catalog:

   ```ini
   [Bake]
   DeployCatalog=C:\Games\BioShock\Content\BulkContent\Catalog.bdc
   ```

4. In the Bake Map dialog, set Source Catalog.bdc to the same deployed catalog.

Each bake then merges into the catalog that already holds the other maps' records.

### The console command

The Bake Map dialog runs one editor console command. You can type the command yourself in
the command box at the bottom of the BioShock editor window.

```
MAP SAVE FILE="C:\Staging\MyMap.bsm" BAKE=1
MAP SAVE FILE="C:\Staging\MyMap.bsm" BAKE=1 BLK=1 LEVELNAME="MyMap" CATALOG="C:\Games\BioShock\Content\BulkContent\Catalog.bdc" REUSEDYNAMIC=1
```

| Argument | Meaning |
| --- | --- |
| `FILE=` | The output path. |
| `BAKE=1` | Bake instead of save. |
| `BLK=1` | Externalize textures. |
| `LEVELNAME=` | The level name. The default is the output file name. |
| `CATALOG=` | The source catalog. |
| `REUSEDYNAMIC=1` | Keep the shared dynamic texture entries. `0` rewrites them. |

The related build commands:

| Command | Same as |
| --- | --- |
| `LIGHT BIOBAKE` | Build > Rebuild Lighting Only |
| `PATHS DEFINE` | Build > Rebuild AI Paths |
| `MAP REBUILD` | The geometry step of Build > Rebuild Geometry Only |

### Read the log after a bake

The BioShock editor writes `System\Editor.log`. Open it after a bake.

1. Search for lines that start with `Bake:`. Each line reports one bake step.
2. Search for `Bake: WARNING`. Each warning names a problem in the map.
3. Confirm that the output file exists and is not zero bytes.

One warning that every mapper can see:

```
Bake: WARNING: 12 reachspecs, none with size-class bits: the path network was built by an older editor. Run Build > Rebuild AI Paths, or no AI will find paths in the game.
```

Run Build > Rebuild AI Paths and bake again.

Other warnings concern the spawning system. See [27-Spawning-AI.md](27-Spawning-AI.md) and the
documents that follow it.

## Deploy a baked map

1. Back up `<game>\Content\BulkContent\Catalog.bdc`. Copy it to a safe folder.
2. Copy `<Output>.bsm` to `<game>\Content\Maps\`.
3. For an externalized bake, copy `<LevelName>Level.blk` to `<game>\Content\BulkContent\`.
4. For an externalized bake, copy the merged `Catalog.bdc` to `<game>\Content\BulkContent\`.
   Replace the original.
5. Open a command prompt in `<game>\Builds\Release\`.
6. Start the game on the map:

   ```
   Bioshock.exe MyMap.bsm -nointro
   ```

The game resolves its configuration and content relative to `Builds\Release`. Start the game
from that folder.

**CAUTION:** Files in the game's install can be read-only. Clear the read-only attribute on
`Content\Maps` and `Content\BulkContent` before you copy.

**CAUTION:** Keep the original `Catalog.bdc`. Restore it to return the game to its original state.

## Play Map

Play Map bakes the open map into the game's install and starts the game. Use it for quick
tests. It is available as Build > Play Level, as Ctrl+P, and as the toolbar button.

### Configure Play Map

1. Open `System\UnrealEd.ini`.
2. Find the `[Play]` section. Add it if it does not exist.
3. Set the values:

   ```ini
   [Play]
   GameDir=C:\Games\BioShock
   MapName=EdPlay
   Args=-nointro
   ```

| Key | Meaning |
| --- | --- |
| `GameDir` | Required. The install root: the folder that holds `Builds\Release\Bioshock.exe` and `Content\Maps`. Not the `Builds\Release` folder. |
| `MapName` | The file name of the quick-play map, without the extension. The default is `EdPlay`. |
| `Args` | Extra arguments for the game. The default is `-nointro`. |

**CAUTION:** Do not set `MapName` to the name of a retail map. Play Map deletes and rewrites
`Content\Maps\<MapName>.bsm` on every run.

### What Play Map does

1. It deletes `<GameDir>\Content\Maps\<MapName>.bsm`. A failed bake cannot start an old map.
2. It bakes the open map inline to that file. Every texture stays inside the `.bsm`. It
   writes no `.blk` file and does not change the catalog.
3. It starts `Bioshock.exe <MapName>.bsm <Args>` with `Builds\Release` as the working folder.

The BioShock editor stays open. The map is keyed by `MapName`, so the level name is always correct.

Play Map does not run any build step. Run the geometry, lighting and path builds first.

### Play Map messages

| Message | Cause |
| --- | --- |
| `Play Map: set [Play] GameDir=<BioShock install root> in UnrealEd.ini first` | `GameDir` is empty or missing. |
| `Play Map: game executable not found: ...` | `GameDir` is not the install root. |
| `Play Map: bake did not produce ...` | The bake failed. Run File > Bake on the same map and read the log. A read-only `Content\Maps` folder also causes this. |
| `Play Map: could not start ...` | Windows refused to start the game. Check the path and the permissions. |

## File size

An inline bake of a small map is tens of megabytes. An externalized bake is smaller because
the texture data moves into the `.blk`. Retail maps are externalized. A character or
animation package embeds all of its skins, so a map with characters grows by the size of
those packages.

## Working folder layout

Keep three folders apart:

| Folder | Contents |
| --- | --- |
| Working folder | Saved maps. Your master copies. |
| Staging folder | Baked maps, `.blk` files and merged catalogs. |
| `<game>\Content\Maps` | Deployed baked maps only. |

Saved maps are `.unr` files and baked maps are `.bsm` files. Keep them in separate folders anyway.

## Related documents

- [02-Setup.md](02-Setup.md)
- [14-Lighting.md](14-Lighting.md)
- [26-AI-Paths.md](26-AI-Paths.md)
- [19-Working-with-Retail-Maps.md](19-Working-with-Retail-Maps.md)
- [36-Troubleshooting.md](36-Troubleshooting.md)
- [38-Keyboard-and-Mouse-Reference.md](38-Keyboard-and-Mouse-Reference.md)
