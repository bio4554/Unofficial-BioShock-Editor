# Setup

This document tells you how to install the SDK, how the SDK Manager makes the content
library from your copy of the game, how the SDK folder is organized, how to start the
BioShock editor, and where the BioShock editor writes its log. Read it before you open the
BioShock editor for the first time.

## Before you start

- You have the installer. Its name is `BioShockSDK-<version>-Setup.exe`.
- You have a retail install of BioShock (2007, PC). The disc, Steam and GOG releases of the
  original game are all this version. BioShock Remastered is not supported. Example:
  `C:\Games\BioShock`.
- You have about 2 GB of free space on the drive where you install. The content library is
  about 900 MB.
- You have time for the first setup. It reads every map of the game once; the SDK Manager shows
  which step runs and the time used so far.

## Install the SDK

1. Start the installer. Windows asks for administrator rights. The installer needs them to
   install the Microsoft Visual C++ 2005 runtime, which the BioShock editor uses.
2. Accept the install folder or choose another one. The default is `C:\BioShockSDK`. This
   folder is the SDK folder in the rest of this guide. Do not install into
   `Program Files`: the BioShock editor writes files next to its own folder. Choose a path
   without spaces (`D:\BioShockSDK`, not `D:\My Tools\BioShockSDK`): the setup stops at the
   effects database step when the path has a space.
3. Let the installer finish. It creates a Start Menu group with four entries: SDK Manager,
   UnrealEd, User Guide, and Uninstall.
4. Keep "Open the SDK Manager" selected on the last page. The SDK Manager opens.

The installer copies files only. It does not touch the game. The SDK Manager does the rest.

**NOTE:** A newer installer installs over an older one. Your generated files, your settings
and your maps in the SDK folder are kept. See "After an update" below.

## The SDK Manager

The SDK Manager is the control panel of the SDK. Start it from the Start Menu or from
`System\SDKManager.exe`. It shows one window.

| Part of the window | What it shows |
|---|---|
| SDK version | The version of the installed SDK. |
| Retail install | The game install that the SDK Manager uses, and how it found it. "Not set up" before the first setup. |
| Last setup | When the last setup or update ran, and with which version. |
| Content library | How many packages the content library holds, and how many of the game's maps it covers. |
| The table "Generated data" | One row for each kind of generated data: `LibraryGen` (the content library), `ShaderGen` (the shaders), `FxGen` (the effects database), `IniGen` (the game's `.ini` files), `RetailManifest` (the retail file check), `CodeContentGen` (the content that only the game's script packages carry, added to the library), `ClassesGen` (the game's class sources and the content they use, exported into `<Package>\`). The Status column says `current`, `needs regeneration` or `never run`. |
| The buttons | The actions. See the table below. |
| The progress area | The step that runs, its progress, and the time used. |
| The log | What the engine reports while a step runs. The "Hide log" button collapses it. |

| Button | What it does |
|---|---|
| Set up | Runs the first setup. After an update the button reads "Update" and runs only the steps whose data changed. When nothing changed, it reads "Up to date". |
| Verify retail install | Compares the game's files with the checksums that ship with the SDK. |
| Validate library | Compares the content library with the description that ships with the SDK. |
| Repair library | Makes the packages that are missing from the content library, then validates it. |
| Rebuild library | Deletes the content library and makes it again. This is the long step. |
| Compile scripts | Compiles a script package of yours with `ucc make`, or makes a new one with a starter class. `BioShockSDK.ini` is left as it is. See [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md). |
| Repair SDK install | Makes the files in `System\` again: the game's script packages, the gameplay and effects `.ini` files, and the three settings files (`BioShockSDK.ini`, `UnrealEd.ini`, `User.ini`) from their templates. The old settings files are kept next to the new ones as `<name>.broken-<date>`. The content library is not touched. |
| Launch UnrealEd | Starts the BioShock editor. Not available before the first setup. |
| Open guide | Opens this guide. |
| Open Editor.log | Opens the BioShock editor's log file. |
| Crash report bundle | Collects the files for a crash report. See [36-Troubleshooting.md](36-Troubleshooting.md). |
| Delete generated library | Deletes the content library and, if you confirm, the other generated files in `System\` and `Shaders\`. The exported sources under `<Package>\` and your own packages are kept. The game is not touched. |
| Change retail path | Points the SDK Manager at another game install. Every generated file is then made again at the next Set up. |
| Cancel | Stops the step that runs. A stopped setup continues where it stopped the next time. |

## The first setup

1. Start the SDK Manager. Press Set up.
2. The SDK Manager looks for the game. It reads the GOG and Steam registrations. If it finds
   the game, it shows the folder. If it does not, or if you want another copy, press Browse
   and select the game's install folder. This is the folder that holds `Builds\Release` and
   `Content`.
3. Press "Use this install". The setup runs. It does not need input until it is finished.
4. When the setup is finished, press Launch UnrealEd.

The setup runs these steps in this order.

| Step | What it makes |
|---|---|
| Verify retail install | Checks the checksum of every game file that the SDK reads: the script packages, the shaders, the configuration bundle, the bulk content index, the 16 bulk texture files and the 31 maps. About 4 GB is read; the step shows the file and the bytes done. A changed or patched file stops the setup. A changed bulk texture file matters as much as a changed script package: the content library would be built from wrong texture data. Verify or reinstall the game files, then run the setup again. When Windows has an OpenAL router installed (`OpenAL32.dll` from another game), this step also writes one file into the game's own folder, `Builds\Release\openal32.dll`, so that the game's audio start-up does not crash. The log says so. See [36-Troubleshooting.md](36-Troubleshooting.md), "Play Map". |
| Copy retail script packages | Copies the game's ten gameplay script packages into `System\`. The copies are read-only. |
| Unpack gameplay inis | Writes the game's 18 gameplay `.ini` files into `System\`. |
| Extract shaders | Writes the game's shaders into `Shaders\`. |
| Write engine and editor inis | Sets the bulk content path and the Play Map game folder. See "The `.ini` files" below. |
| Extract effects database | Reads the effects from the game's maps and writes four `.ini` files into `System\`. |
| Decook content library | Reads each of the game's 31 maps and writes the shared content into `Content\`. One package at a time; the log shows the map and the package count. Then reads the game's script packages and adds the content that only they carry (reticles, simple shapes, upgrade art) to the library. |
| Validate content library | Compares the result with the description that ships with the SDK. |
| Export retail sources | Writes the class sources of each of the game's ten script packages into `<Package>\Classes\`, and the content those classes use (textures, meshes, effects, collision data) as files into `<Package>\Import\`. This is what Compile scripts compiles a game package from; see [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md). A `<Package>\Classes\` folder that already holds files is kept. |
| Write setup state | Records the setup in `System\SDKState.ini`. |

**NOTE:** If the BioShock Remastered edition is installed and the 2007 edition is not, the
SDK Manager reports it and asks for the 2007 edition. The Remastered files have a different
format. The SDK cannot read them.

### When a step fails

The SDK Manager shows the step, the last lines of the engine's output, and three buttons.

| Button | Effect |
|---|---|
| Retry step | Runs the step again. For the content library, only the map that failed runs again. |
| Skip validation | Only on the validation step. Continues without the check. The status panel then shows that the library is not validated. |
| Cancel | Stops the setup. The finished steps are kept. The next Set up continues after them. |

The full output of every engine run is in `Logs\`. The file name holds the step and the map,
for example `Logs\ucc_decook_5-Arcadia.log`. The SDK Manager's own log is
`Logs\SDKManager.log`.

**NOTE:** The validation compares your content library with a description made from a
reference library. A difference in a package name's letter case, or an extra object, is
reported as a difference, but the package works. If the validation reports a small number of
packages and the BioShock editor opens them, press Skip validation.

## The SDK folder

| Folder or file | Contents |
|---|---|
| `System\` | The BioShock editor (`UnrealEd.exe`), the SDK Manager (`SDKManager.exe`), the script compiler (`UCC.exe`), the engine DLLs, the `.ini` files and the script packages: the SDK's `Core.u`, `Engine.u`, `Editor.u`, `UnrealEd.u` and the copies of the game's packages (`ShockGame.U`, `ShockAI.U`, `VengeanceShared.U`, `Scripting.U`, `Tyrion.U`, the `IG*` effects packages, `FMODAudio.U`). The BioShock editor runs from this folder. |
| `System\Editor.log` | The BioShock editor log. The BioShock editor writes it on every start. |
| `System\SDKState.ini` | The record of the last setup. The SDK Manager writes it. Do not edit it. |
| `Content\` | The content library. About 366 packages with the `.pkg` extension. Static meshes, textures, materials, sounds and effects from the game. Made by the SDK Manager. |
| `Shaders\` | The game's shaders. Made by the SDK Manager. |
| `ShockGame\`, `ShockAI\`, `VengeanceShared\`, ... (one folder per game package) | The game's class sources (`Classes\*.uc`) and the content they use (`Import\`), exported by the SDK Manager. Compile scripts compiles a game package from here. Yours to edit. |
| `<Package>\` (your own) | A script package of your own: `Classes\*.uc`, made by you or by New package in the SDK Manager. See [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md). |
| `Maps\` | Working maps (`.unr`). The SDK ships one example map. The BioShock editor writes its autosaves (`Auto1.unr` ...) here. |
| `Logs\` | The SDK Manager's logs. |
| `Manifests\` | The checksums and the library description that the SDK Manager verifies against. Do not edit them. |
| `UnrealEdGuide\` | This guide. |
| `ThirdPartyNotices\` | The licences of the third-party components. |

The game install has its own layout. The BioShock editor reads from it and the bake writes to it.

| Folder or file in the game install | Contents |
|---|---|
| `Builds\Release\Bioshock.exe` | The game. Play Map starts this file. |
| `Builds\Release\*.U` | The game's script packages. The SDK Manager copies the gameplay packages. Do not replace `Core.U` or `Engine.U` with the SDK copies. |
| `Content\Maps\*.bsm` | The game's maps. Play Map writes the quick-play map here. |
| `Content\BulkContent\Catalog.bdc` | The index of the bulk content. The BioShock editor reads it to show the game's textures. |
| `Content\BulkContent\*.blk` | The bulk content. The texture pixels of the game live here, not in the maps. |

**CAUTION:** The SDK Manager never writes into the game install. Play Map and the bake do.
Keep a backup of `Content\Maps` before you deploy a map to the game.

## The `.ini` files

The BioShock editor and the engine read their settings from `.ini` files in `System\`.

| File | What it holds | When it is created |
|---|---|---|
| `System\Default.ini` | The template for the engine settings. | Ships with the SDK. |
| `System\BioShockSDK.ini` | The live engine settings. The engine reads this file. | The SDK Manager copies `Default.ini` to `BioShockSDK.ini` during the setup. |
| `System\UnrealEd.ini` | The BioShock editor settings: window layout, browsers, the Play Map settings, the Bake settings. | The SDK Manager copies `DefUnrealEd.ini` to `UnrealEd.ini` during the setup. |
| `System\User.ini` | The user settings. | The SDK Manager copies `DefUser.ini` to `User.ini` during the setup. |

The SDK Manager sets two values. It changes no other line, so your own edits are kept.

```ini
; System\BioShockSDK.ini
[Engine.Engine]
BulkContentPath=C:\Games\BioShock\Content\BulkContent

; System\UnrealEd.ini
[Play]
GameDir=C:\Games\BioShock
```

| Key | Meaning |
|---|---|
| `BulkContentPath` | The game's bulk content folder. Without it, retail textures are blank and the log shows `BulkCatalog: no [Engine.Engine] BulkContentPath configured`. |
| `GameDir` | The install root for Play Map. This is the folder that holds `Builds\Release\Bioshock.exe` and `Content\Maps`. |

**NOTE:** The engine rewrites `BioShockSDK.ini` and `UnrealEd.ini` when it exits. Comment
lines in these two files are lost. Keep your notes elsewhere.

If an edit to one of these files stops the BioShock editor from starting, press Repair SDK
install in the SDK Manager. It replaces the three files from their templates and keeps the old
ones next to them as `<name>.broken-<date>`.

### The content library path

The engine finds packages through the `Paths` lines under `[Core.System]`. The SDK
ships with the content library on the path:

```ini
[Core.System]
Paths=../System/*.u
Paths=../Content/*.pkg
```

Do not remove the `../Content/*.pkg` line. Without it, a map cannot find the packages it
imports from the content library.

### The Play Map settings

Play Map bakes the open map into the game install and starts the game. The settings are
under `[Play]` in `System\UnrealEd.ini`.

| Key | Meaning |
|---|---|
| `GameDir` | Required. Set by the SDK Manager. The install root. |
| `MapName` | The base name of the quick-play map. Play Map writes `<GameDir>\Content\Maps\<MapName>.bsm` and overwrites it on every play. Default `EdPlay`. Do not use the name of a retail map. |
| `Args` | Extra arguments for the game. Default `-nointro`. Leave the value empty to pass none. |

See [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md) for what Play Map does.

### The Bake settings

File > Bake stores its last settings under `[Bake]` in `System\UnrealEd.ini`. You do not
need to edit them by hand. The dialog writes them.

| Key | Meaning |
|---|---|
| `Catalog` | The `Catalog.bdc` that the last bake merged into. |
| `DeployCatalog` | Optional. The `Catalog.bdc` of the install that you deploy to. When set, a new bake starts from this catalog, so each bake carries the records of the earlier bakes forward. Set it once to `<GameDir>\Content\BulkContent\Catalog.bdc`. |
| `Externalize` | `1` = write the textures to a `.blk` file. `0` = keep every texture inside the `.bsm`. |
| `ReuseDynamic` | `1` = do not rewrite textures that the game already has in its dynamic bulk file. |
| `LastDir` | The folder of the last bake output. |

## After an update

A new version of the SDK can change how the generated data is made. Each kind of
generated data carries a version number. After you install a new version:

1. The installer opens the SDK Manager.
2. The table "Generated data" shows `needs regeneration` on each row whose version changed,
   and `never run` on a row that the previous version did not have.
3. Press Update. Only those steps run. When every row shows `current`, the button reads
   "Up to date" and there is nothing to do. Launch UnrealEd waits until no row reads
   `never run`.

An update never touches your maps, your own script packages, your `.ini` edits, or the game.
When `ClassesGen` changed, a `<Package>\Classes\` folder that has no `Import\` folder next to
it (an export from an older version) is exported again and its files are overwritten; a folder
with both is kept, edits included.

## Repair, rebuild, delete

| Situation | Action |
|---|---|
| A package is missing from `Content\`, or the browsers do not show a package. | Press Repair library. The SDK Manager makes the missing packages and validates the library. |
| Packages in `Content\` are damaged, or Repair library does not correct the problem. | Press Rebuild library. The SDK Manager deletes the library and makes it again. |
| The BioShock editor does not start after an edit to `UnrealEd.ini` or `BioShockSDK.ini`, or a file in `System\` is missing. | Press Repair SDK install. The SDK Manager makes the files in `System\` again. The settings files are replaced from their templates; the old ones are kept as `<name>.broken-<date>`, so you can copy a setting back by hand. |
| You moved or reinstalled the game. | Press Change retail path. Select the new folder. Press Set up. |
| You want the disk space back. | Press Delete generated library. The BioShock editor cannot open maps until you run Set up again. |
| You uninstall the SDK. | The uninstaller asks whether to delete the generated files too. Your maps in `Maps\`, the exported sources under `<Package>\` and your own packages are kept in either case. |

## Start the BioShock editor

Start the BioShock editor in one of these ways:

- Press Launch UnrealEd in the SDK Manager.
- Use the Start Menu entry UnrealEd.
- Start `System\UnrealEd.exe`.

The BioShock editor must run with `System\` as its working directory. The Start Menu entry
sets it. A shortcut that you make must set `System\` as the "Start in" folder.

The first map that you open takes longer than later ones. The BioShock editor compiles the
game's shaders behind a progress bar and stores the result in `System\`. Later map loads
read the stored result.

The first start after an installation or an update opens the What's New window with the
release notes of the installed version. Help > What's New opens it again. At every other
start the Tip of the Day window opens until you clear Show tips at startup; Help > Tip of
the Day opens it again, and its Next Tip and What's New buttons switch between the two
pages.

## First-run checks

Do these checks after the first start. They confirm that the three parts are connected.

1. Open the Texture browser (View > Show Texture Browser).
2. In the browser, use File > Open. In the file dialog, open `Content\Gen_Decor.pkg` from the
   SDK folder. The browser shows the package's textures with their pixels.
3. Open a retail map with File > Open. Select a file from `<GameDir>\Content\Maps\`, for
   example `1-Welcome.bsm`. The map loads and the perspective viewport shows it.
4. Open View > Log and read the last lines. There must be no line that starts with
   `BulkCatalog: no`.

If step 2 shows blank textures, check `BulkContentPath`. If step 3 fails, see
[36-Troubleshooting.md](36-Troubleshooting.md).

**NOTE:** The `<Map>_int.bsm` files next to the main maps are voice-over companion maps.
The BioShock editor does not open them. Open the main map without `_int`.

## The log

The BioShock editor writes `System\Editor.log`. It overwrites the file on every start. The log
holds every warning and every message from the build steps and the bake. The first lines
name the SDK version and the setup that made the generated data.

- View > Log opens the log window inside the BioShock editor. The bottom bar also has a
  button, "Show Full Log Window".
- Search the log for `Warning` after a build, and for `Bake:` after a bake.
- The log file is plain text. Open it in any text editor while the BioShock editor runs, or
  press Open Editor.log in the SDK Manager.

## The command line

The bottom bar of the main window has a text box labelled "Command :". Type an editor
console command there and press Enter. The result goes to the log.

Commands that this guide uses:

| Command | Effect |
|---|---|
| `LIGHT BIOBAKE` | Runs the lighting build. Same as Build > Rebuild Lighting Only. |
| `PATHS DEFINE` | Runs the path build. Same as Build > Rebuild AI Paths. |
| `MAP SAVE FILE="C:\Maps\MyMap.bsm" BAKE=1` | Bakes the open map to the given file. Same as File > Bake with Externalize off. |
| `MODE SPEED=4` | Sets the camera speed to 4. Values 1, 4 and 16 match the toolbar button. |

## Performance notes

- The first map load compiles the shaders once. Later map loads read the stored result and
  are fast. A material that no map has used before compiles when it is first drawn.
- Large retail maps take up to a minute to open.
- Keep the perspective viewport small while you work on geometry. Enlarge it to check the
  look.

## Related documents

- [01-Introduction.md](01-Introduction.md)
- [03-Interface-Tour.md](03-Interface-Tour.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [19-Working-with-Retail-Maps.md](19-Working-with-Retail-Maps.md)
- [36-Troubleshooting.md](36-Troubleshooting.md)
