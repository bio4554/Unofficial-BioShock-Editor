# UCC Commandlet Reference

This document lists the `UCC.exe` commandlets that the SDK supports, with the syntax
and the output of each. Read it when you need to run one of them by hand.

## Before you start

- You know what `UCC.exe` is and how to run it from `System\`. See
  [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md).
- The SDK Manager's setup has run. See [02-Setup.md](02-Setup.md).

## The general form

```
ucc <commandlet> <parameters>
```

- Run UCC with `System\` as the working directory. Paths are relative to `System\`.
- Every run writes `System\UCC.log`. `make` also copies its console text to
  `System\StdOut.log`.
- A run ends with `Success - N error(s), M warning(s)` or `Failure - N error(s), M warning(s)`.
  The exit code is 0 on success and 1 on failure.
- A commandlet that stops on a fatal error prints the message, a `History:` line and
  `Exiting due to error`.

**NOTE:** `ucc help` lists more commandlets than this document. The others are stock engine
tools that the SDK does not support. Some of them write into `System\`. Do not use them.

## help

```
ucc help
ucc help <commandlet>
```

`ucc help` prints the SDK's build label and the list of commandlets. `ucc help <commandlet>`
prints the syntax and the parameters of one commandlet, for example `ucc help make` or
`ucc help batchexport`. A name that is not a commandlet prints `Commandlet <name> not found`.

## make

```
ucc make [-silentbuild]
```

Compiles every package in the `EditPackages` list that has no `.u` file. See
[33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md) for the full description. The
output is one heading per package, then `Analyzing...`, `Parsing <Class>`, `Compiling <Class>`
and `Importing Defaults for <Class>` for each compiled class, then the trailer. `-silentbuild`
leaves out the `Parsing` and `Compiling` lines.

## batchexport

```
ucc batchexport <package> <Class> <ext> <folder>
```

Writes every object of the given class in a package to one file each. `<Class>` is the class
of the objects, `<ext>` the file extension, `<folder>` the output folder. The folder is
created when it does not exist. Write the folder with backslashes.

| Command | Result |
| --- | --- |
| `ucc batchexport Engine.u Class uc C:\Export\Engine` | One `.uc` file per class of `Engine.u`: `Actor.uc`, `Light.uc`, and so on. |
| `ucc batchexport ShockGame.U Class uc C:\Export\ShockGame` | One `.uc` file per class of one of the game's packages. Each file holds the class's UnrealScript source with the comments removed and a `defaultproperties` block made from the class's defaults. |
| `ucc batchexport Engine.u Texture bmp C:\Export\EngineTex` | One `.bmp` file per texture of `Engine.u`. |

The output for a class export:

```
Loading package Engine.u...
Exported Class Engine.Engine to C:\Export\Engine\Engine.uc
Exported Class Engine.Actor to C:\Export\Engine\Actor.uc
...
Success - 0 error(s), 0 warning(s)
```

Use the class export to read what a class of the game does before you extend it. The SDK
Manager runs this export for every game package at setup, into `<SDK folder>\<Package>\Classes\`,
where `ucc make` compiles from; see *Compile a game package from its sources* in
[33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md).

## analyzecontent

```
ucc analyzecontent <map>
```

Prints the texture and static mesh statistics of a map: the number of textures per format
with their video memory, and the number of meshes with their triangle counts.

```
ucc analyzecontent ..\Maps\DepartmentStore.unr
```

```
Texture Stats

   24  RGBA textures consuming         167 KByte in video memory
   23  PAL8 textures consuming         456 KByte in video memory
  804  DXT1 textures consuming      101463 KByte in video memory
   66  DXT3 textures consuming       12273 KByte in video memory
   70  DXT5 textures consuming       13821 KByte in video memory

 1020 total textures consuming      128181 KByte in video memory


StaticMesh Stats

  224 static meshes with a total of  33120 triangles
  147 average triangles per mesh
    1 average sections per mesh
```

## editor.LoadPackage

```
ucc editor.LoadPackage <file> [<file> ...]
```

Loads each package and reports the result. Use it to test that a package you compiled or
saved loads outside the BioShock editor.

```
ucc editor.LoadPackage MyMod.u
```

```
Loading MyMod.u
Success - 0 error(s), 0 warning(s)
```

## editor.ClassFlag

```
ucc editor.ClassFlag <file> [<file> ...]
```

Prints one line per class in each package, with the class's flags. Use it to check that a
class of yours is placeable.

```
ucc editor.ClassFlag MyMod.u
```

```
MyLight   : CLASS_Config,CLASS_Placeable,CLASS_NoCacheExport
MyPistol  : CLASS_Config,CLASS_Localized,CLASS_NoCacheExport
MyTextures:
Success - 0 error(s), 0 warning(s)
```

## editor.BioFxExtract

```
ucc editor.BioFxExtract <maps folder> <output folder> [<map.bsm> ...]
```

Reads the effects from the game's maps and writes the four effects `.ini` files
(`SoundEffects.ini`, `SoundDefs.ini`, `VisualEffects.ini`, `ModEffects.ini`) into the output
folder. Without map names, it reads every `.bsm` in the maps folder. The SDK Manager runs it
for you in the step "Extract effects database", with `.` as the output folder, which is
`System\`. The manual form exists to repeat that step:

```
ucc editor.BioFxExtract C:\Games\BioShock\Content\Maps . 1-Welcome.bsm 1-Medical.bsm
```

The output shows one `BioFxExtract: map N/M <file>` line per map and ends with
`BioFxExtract: N maps, N errors, N conflicts`. Without parameters, it prints its usage line and
stops.

## editor.BioDecookLibrary

```
ucc editor.BioDecookLibrary - <maps folder> <output folder> [<map.bsm> ...]
ucc editor.BioDecookLibrary --validate <library folder> <manifest>
ucc editor.BioDecookLibrary --write-manifest <library folder> <manifest>
```

The commandlet behind the content library. The SDK Manager runs it for you in the steps
"Decook content library" and "Validate content library". The manual forms exist to repeat one
step:

| Form | What it does |
| --- | --- |
| `ucc editor.BioDecookLibrary - C:\Games\BioShock\Content\Maps ..\Content 1-Welcome.bsm` | Reads one map and writes its shared content into `..\Content`. The first parameter `-` stands for no map list file. The SDK Manager runs one such command per map. |
| `ucc editor.BioDecookLibrary --validate ..\Content ..\Manifests\library_manifest.txt` | Compares the content library with the description that ships with the SDK. Prints one `BioDecookLibrary: validate N/M <package>` line per package and ends with `BioDecookLibrary: validate N packages, N failures`. |
| `ucc editor.BioDecookLibrary --write-manifest ..\Content <file>` | Writes a description of your content library to the given file. |

`BulkContentPath` in `System\BioShockSDK.ini` must be set. Without parameters, the commandlet
prints its usage lines and stops.

## editor.BioExportPackage

```
ucc editor.BioExportPackage <package> <folder> [--into <Package>] [--nohosts]
```

Writes a package's classes and content as source files: `Classes\*.uc` (and
`Classes\<Group>\*.uc` for the classes it embeds), the content under `Import\` (textures,
property files, `.ase` meshes, `.glb` files for the skeletal meshes with their skeletons and
clips, the collision and animation metadata as text, one carved package for the skeletal mesh
bodies) and the generated `<Package>*Content.uc` classes whose `#exec` lines import it all at
`ucc make`. The SDK Manager runs it for you in step 8, *Export retail sources*, once per game
package (see [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md)).

| Form | What it does |
| --- | --- |
| `ucc editor.BioExportPackage ShockGame.U ..\ShockGame` | Exports the game's `ShockGame` package into `..\ShockGame` (the folder `ucc make` compiles from). Give the folder the way `ucc make` will see it from `System\`: the `#exec` lines in the generated classes name their files relative to `System\`. |
| `ucc editor.BioExportPackage ..\Content\FX_tex.pkg ..\FX_tex` | Exports a content-library package the same way. `ucc make FX_tex` (with `FX_tex` in `EditPackages`) then rebuilds the package from those files. |
| `ucc editor.BioExportPackage ..\Content\FX_tex.pkg ..\MyMod --into MyMod` | The same files, but the generated classes import them into the `MyMod` package, so `MyMod.u` carries that content. |
| `--nohosts` | Writes the files only, no `<Package>*Content.uc` classes. |

The run prints one summary line, `BioExportPackage <package> -> <folder>: N classes with text,
N class shells, ...`. An existing folder is written over; edit your copies after the export.

**NOTE:** `editor.BioFxExtract`, `editor.BioDecookLibrary` and `editor.BioExportPackage` need every
`EditPackages` package, like the BioShock editor. When one of the game's packages is missing from `System\`, they stop
with `Can't find edit package '<Pkg>'`. See the caution in
[33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md).

## Related documents

- [02-Setup.md](02-Setup.md)
- [36-Troubleshooting.md](36-Troubleshooting.md)
- [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md)
- [34-Importing-Assets-with-UCC.md](34-Importing-Assets-with-UCC.md)
