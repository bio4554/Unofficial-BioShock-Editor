# UCC and Script Packages

This document tells you what `UCC.exe` is, which script packages the SDK has and where
their sources live, how to compile a script package of your own, how to compile one of the
game's packages from its exported sources, when the compiler compiles a package again, and
how to read the compiler's log. Read it before you write a class of your own, and before you
use the compile-time imports in [34-Importing-Assets-with-UCC.md](34-Importing-Assets-with-UCC.md).

This document does not teach the UnrealScript language. It tells you how to compile it.

**NOTE:** This guide uses "scripting" and "script" for the level scripting system of
documents 20 to 23: Script actors and their Actions lists, edited in the Properties window,
with no code. The class code that the game's packages are compiled from is a different thing.
This guide always calls it UnrealScript. A file of UnrealScript is a class file (`.uc`), and
a compiled set of classes is a script package (`.u`).

## Before you start

- The SDK Manager's setup has run. `System\` holds the SDK's packages and the copies
  of the game's packages, and the game's class sources are exported. See
  [02-Setup.md](02-Setup.md).
- You can edit a text file and save it with the `.uc` extension.
- For the command-prompt sections: you can open a command prompt and change to a folder.
  The SDK Manager does the same work without one.

## What UCC is

`System\UCC.exe` is the command-line tool of the SDK. It runs one command, called a
commandlet, and exits. The commandlet that you use most is `make`, the UnrealScript compiler.
Other commandlets export the contents of a package, test that a package loads, or make the
generated data that the SDK Manager builds. See
[35-UCC-Commandlet-Reference.md](35-UCC-Commandlet-Reference.md).

UCC reads the same `System\BioShockSDK.ini` as the BioShock editor. Run it with `System\` as
the working directory. Every path that you give to a commandlet, and every path in an import
directive, is relative to `System\`.

```
cd /d C:\BioShockSDK\System
ucc make
```

The SDK Manager's Compile scripts button runs `ucc make` for you. See *Compile from the SDK
Manager* below.

## The three kinds of script packages

| Kind | Packages | Where they come from | Sources |
| --- | --- | --- | --- |
| The SDK's | `Core.u`, `Engine.u`, `Editor.u`, `UnrealEd.u` | They ship with the installer. | None. They are never compiled. |
| The game's | `ShockGame.U`, `ShockAI.U`, `VengeanceShared.U`, `Scripting.U`, `Tyrion.U`, `FMODAudio.U`, `IGEffectsSystem.U`, `IGVisualEffectsSubsystem.U`, `IGSoundEffectsSubsystem.U`, `IGModEffectsSubsystem.U` | Read-only copies of the game's files from `<GameDir>\Builds\Release`. The SDK Manager copies them at setup. | Exported at setup into `<SDK folder>\<Package>\`. See *Compile a game package from its sources*. |
| Yours | `<Package>.u` | Compiled by you, from your own class files. | `<SDK folder>\<Package>\Classes\*.uc`, written by you. |

The `EditPackages` lines under `[Editor.EditorEngine]` in `System\BioShockSDK.ini` list the
packages that the BioShock editor loads at start. `ucc make` walks the same list, in order.
The SDK's template lists the fourteen packages of the first two kinds:

```ini
; System\BioShockSDK.ini
[Editor.EditorEngine]
EditPackages=Core
EditPackages=Engine
EditPackages=FMODAudio
EditPackages=Editor
EditPackages=UnrealEd
EditPackages=IGEffectsSystem
EditPackages=IGVisualEffectsSubsystem
EditPackages=IGSoundEffectsSubsystem
EditPackages=VengeanceShared
EditPackages=Scripting
EditPackages=Tyrion
EditPackages=ShockGame
EditPackages=ShockAI
EditPackages=IGModEffectsSubsystem
```

Your own package does not need a line. The SDK Manager lists it for the compile only, and in
the BioShock editor you open it from the Actor Class browser. Add a permanent line only when
the package must load every time the BioShock editor starts (see *Compile from a command
prompt*).

**CAUTION:** Do not delete, rename or replace the fourteen files of the first two kinds by
hand. When one is missing, the BioShock editor stops at start with
`Can't find edit package '<Pkg>'`, and `ucc make` stops with
`Can't find files matching <Pkg>\Classes\*.uc`. To recover, press Repair SDK install in the
SDK Manager: it copies the game's ten packages into `System\` again. The four SDK
packages come back with the installer.

## Where sources live

`ucc make` reads the source of a package from one place:

```
<SDK folder>\<Package>\Classes\*.uc
```

- The folder name equals the package name.
- Each `.uc` file holds one class. The file name equals the class name. `MyPistol.uc`
  declares `class MyPistol`.
- The `Paths` lines in the `.ini` file are not used to find sources. They find `.u` files.

For the game's packages the SDK Manager fills these folders at setup (the step *Export retail
sources*). For your own package you write the files. The SDK's four packages have no
source folder; to read what one of their classes does, export it with `ucc batchexport`
(see [35-UCC-Commandlet-Reference.md](35-UCC-Commandlet-Reference.md)).

## What `ucc make` compiles

`ucc make` compiles a listed package only when `System\<Package>.u` is missing. A package
whose `.u` file loads is used as it is, even when its source changed. This rule decides every
procedure in this document:

- To compile a package the first time, its `.u` file does not exist yet. Nothing to delete.
- To compile a package again after an edit, delete its `.u` file first. Without that step the
  run prints the package's heading, compiles nothing and reports
  `Success - 0 error(s), 0 warning(s)`. Your edit is not in the package.
- A full rebuild of a package is the same two steps. There is nothing else to delete.
- A package listed before the one you compile is loaded, not compiled, so the packages it
  depends on must be listed before it.

The SDK Manager's Compile button deletes the `.u` file for you. At a command prompt you delete
it yourself.

**CAUTION:** Never delete `Core.u`, `Engine.u`, `Editor.u` or `UnrealEd.u`. They have no
sources in the SDK folder and cannot be compiled again.

## Compile from the SDK Manager

The SDK Manager's Compile scripts button compiles a package without a command prompt and
without an edit to `BioShockSDK.ini`. The result is the same `.u` file as `ucc make` writes.

1. Close the BioShock editor. It holds the script packages open. Compile scripts refuses to
   run while the BioShock editor runs from the SDK folder.
2. Press Compile scripts. The dialog lists your packages: every folder
   `<SDK folder>\<Package>\Classes\` that holds `.uc` files, with a status such as
   "not compiled yet", "compiled <date>" or "changed since the last compile". The game's
   packages and the SDK's are behind the checkbox "Show the game's and the SDK's
   packages".
3. No package yet? Press New package, type a name, and the SDK Manager makes
   `<Package>\Classes\` with one starter class (a placeable light) and opens the folder.
   Open folder opens the sources of the selected package.
4. Select your package and press Compile. The SDK Manager deletes the old
   `System\<Package>.u`, runs `ucc make` from `System\`, and shows the compiler's output in
   the log. A message reports `System\<Package>.u is written`.
5. Start the BioShock editor. Your package is not loaded at start: in the Actor Class browser,
   use File > Open Package and select `System\<Package>.u`, or open a map that uses the
   package. Either way its classes appear in the class tree and can be placed.

For the compile the SDK Manager appends your package's `EditPackages` line (and one for each
of your other compiled packages, so a package that extends one of them compiles), and removes
those lines again when `ucc make` has finished, whether it succeeded or failed.
`BioShockSDK.ini` is as it was, so the BioShock editor never stops at start over a package
that did not compile. A line that you added by hand is left alone.

When a compile fails, the Step failed dialog shows the compiler's last lines. The lines that
name a file and a line number are the errors (see *When a compile fails*). Correct the source
and press Retry step. Cancel leaves the package without a `.u` file; nothing else changes.

The dialog never compiles or deletes the SDK's four packages. A game package can be
selected: Compile then asks for a confirmation, because it deletes the copy of the game's
file. See *Compile a game package from its sources*.

## Compile from a command prompt

The example makes a package `MyMod` with two classes: a weapon that extends the game's
`Pistol`, and a light that extends the engine's `Light`. Do the steps in order.

1. Make the folder `<SDK folder>\MyMod\Classes\`.
2. Write `MyMod\Classes\MyPistol.uc`:

   ```
   class MyPistol extends Pistol;

   defaultproperties
   {
   }
   ```

3. Write `MyMod\Classes\MyLight.uc`:

   ```
   class MyLight extends Light placeable;

   defaultproperties
   {
       LightBrightness=1.0
   }
   ```

4. Open `System\BioShockSDK.ini`. Under `[Editor.EditorEngine]`, add one line after the last
   `EditPackages` line:

   ```ini
   EditPackages=IGModEffectsSubsystem
   EditPackages=MyMod
   ```

5. Open a command prompt. Change to `System\` and run `ucc make`:

   ```
   cd /d C:\BioShockSDK\System
   ucc make
   ```

6. Read the output. The compiler prints one heading per listed package. The packages that
   already have a `.u` file print only their heading. `MyMod` is compiled:

   ```
   ----------------------------Core - Release----------------------------
   ---------------------------Engine - Release---------------------------
   -------------------------FMODAudio - Release--------------------------
   ---------------------------Editor - Release---------------------------
   --------------------------UnrealEd - Release--------------------------
   ----------------------IGEffectsSystem - Release-----------------------
   ------------------IGVisualEffectsSubsystem - Release------------------
   ------------------IGSoundEffectsSubsystem - Release-------------------
   ----------------------VengeanceShared - Release-----------------------
   -------------------------Scripting - Release--------------------------
   ---------------------------Tyrion - Release---------------------------
   -------------------------ShockGame - Release--------------------------
   --------------------------ShockAI - Release---------------------------
   -------------------IGModEffectsSubsystem - Release--------------------
   ---------------------------MyMod - Release----------------------------
   Analyzing...
   Parsing MyLight
   Parsing MyPistol
   Compiling MyLight
   Compiling MyPistol
   Importing Defaults for MyLight
   Importing Defaults for MyPistol
   Success - 0 error(s), 0 warning(s)
   ```

7. Check that `System\MyMod.u` exists.
8. Start the BioShock editor. Because of the line from step 4, `MyMod` loads at start. Open
   the Actor Class browser. `MyPistol` is listed under `Pistol`, and `MyLight` under `Light`.
   Place `MyLight` like any other actor.

After you edit a `.uc` file: delete `System\MyMod.u` and run `ucc make` again (see *What
`ucc make` compiles*).

The rules behind the steps:

- Add your `EditPackages` line after every existing entry. A package must be listed after
  every package it extends. Listed first, the example stops with
  `Superclass Pistol of class MyPistol not found`.
- Add the line, run `ucc make`, and then start the BioShock editor, in that order. The
  BioShock editor stops at start when a listed package has no `.u` file. This is the cost of a
  permanent line, and the reason the SDK Manager adds none.
- When you remove a package, remove its `EditPackages` line too.
- Give your package a name that no file in `System\` and no package in `Content\` uses.

**NOTE:** Repair SDK install makes a new `System\BioShockSDK.ini` from the SDK's
template. The new file does not have your line. Add it again, or compile with the SDK
Manager, which needs no line.

## Compile a game package from its sources

Every game package carries the source of its classes and the content those classes use. The
SDK Manager exports both at setup (the step *Export retail sources*) into
`<SDK folder>\<Package>\`:

- `Classes\*.uc`: one file per class, the source next to a `defaultproperties` block made from
  the class's defaults. The classes the package embeds without source (designer classes,
  effects, weapon upgrades) come out as `Classes\<Group>\*.uc` with their defaults only.
- `Import\`: the content the package embeds, as files you can edit: textures as `.dds` or
  `.tga` under `Import\<Group>\`, static meshes as `.ase`, skeletal meshes with their
  skeletons and clips as `.glb`, the collision and animation metadata as text under
  `Import\Havok\`, and every object's properties as
  `Import\Props\<Class>\<Group>.<Name>.props`. The skeletal mesh bodies also travel as one
  package, `Import\<Package>_SkeletalMeshes.u`, which the generated classes load; the `.glb`
  rebuilds each skeleton's animation package at compile time.
- `Classes\<Package>Content.uc`, `<Package>MaterialContent.uc`, `<Package>StaticMeshContent.uc`,
  `<Package>HavokContent.uc`, `<Package>SkeletalMeshContent.uc` and `<Package>FXContent.uc`:
  generated classes whose `#exec` lines import the files above when the package compiles
  (see [34-Importing-Assets-with-UCC.md](34-Importing-Assets-with-UCC.md)). They are not game
  classes. Edit the files under `Import\`, not these.

You can edit the exported files. The *Export retail sources* step keeps your files only if both conditions are true:

- The `Classes\` folder contains `.uc` files.
- The same package folder contains an `Import\` folder next to `Classes\`.

**CAUTION:** If the `Import\` folder is missing, *Export retail sources* overwrites existing source files.
Older exports can have no `Import\` folder. Back up your edited source files before you run Set up.
Also back up these files before an Update that runs *Export retail sources* again.
A `ClassesGen` change causes this step to run again. See "After an update" in [02-Setup.md](02-Setup.md).

To export the files again:

1. Back up your edited files.
2. Delete the `Classes\` folder.
3. Run Set up.

To compile `ShockGame` from that folder in the SDK Manager: Compile scripts, tick "Show the
game's and the SDK's packages", select `ShockGame`, press Compile, and confirm. The SDK
Manager deletes `System\ShockGame.U`, the read-only copy of the game's file, and runs
`ucc make`. The game's packages are listed permanently, so the compiled package loads at the
next start of the BioShock editor.

At a command prompt the same compile is:

1. Delete `System\ShockGame.U`.
2. Run `ucc make` from `System\`. The run compiles `ShockGame`, then loads the packages listed
   after it (`ShockAI`, `IGModEffectsSubsystem`) against the new `System\ShockGame.u`.

```
-------------------------ShockGame - Release--------------------------
Analyzing...
Parsing HeadbobAnimSettings
...
Compiling ShockPlayer
...
Importing Defaults for ShockPlayer
--------------------------ShockAI - Release---------------------------
-------------------IGModEffectsSubsystem - Release--------------------
Success - 0 error(s), 31 warning(s)
```

The warnings are the game's own (`'Player' obscures 'Player' defined in base class`,
`'localized': Has no effect on arrays of type 'struct'`, unreferenced locals). An unedited
export compiles to the same code and the same defaults as the game's package.

To return to the game's package, press Repair SDK install in the SDK Manager, or delete
`System\ShockGame.u` and press Repair library. Both copy the game's packages into `System\`
again. An Update that copies the game's packages again does the same; compile the
package again after it.

The rules behind the steps:

- The exported source has no comments: the game's files carry the code with every comment
  blanked. Every class source comes from your own copy of the game's packages; the SDK
  ships none.
- The compiled package carries its content the way the game's does: everything the `#exec`
  lines import is saved inside it, under the same group names (`FX_tex`, `ShockDesignerClasses`,
  `WP_Pistol`, ...), and nothing is read from `Content\*.pkg` at load time. An unedited export
  compiles to the same set of embedded objects as the game's package.
- Content the package uses but does not embed (a mesh from `SimpleShapes`, a texture from
  `FX_tex` that another package carries) stays a reference, resolved from the content library.
- A package listed before the one you compile is loaded, not compiled. To compile
  `VengeanceShared` and `ShockGame` both, delete both `.U` files; `ucc make` compiles them in
  list order. In the SDK Manager, compile `VengeanceShared` first, then `ShockGame`.

**CAUTION:** A compiled game package is made for the SDK. The game itself does load compiled
`ShockGame.U` and `ShockAI.U` (verified in play, saved games included), but a copy into
`<GameDir>\Builds\Release` replaces the game's own file: keep the original next to it. See
[18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md) for what a baked map carries.

## When a compile fails

An error stops the run. The package with the error is not saved, and the packages later in
the list are not processed. The packages earlier in the list are already saved.

A compile error names the file and the line:

```
C:\BioShockSDK\MyMod\Classes\MyBad.uc(5) : Error, Type mismatch in '='
Compile aborted due to errors.
Failure - 1 error(s), 0 warning(s)
```

The line format is `<file>(<line>) : Error, <text>`. A warning uses the same format with
`Warning`. Warnings do not stop the run.

A missing superclass stops the run before any class is compiled:

```
---------------------------MyMod - Release----------------------------
Analyzing...
Superclass Pistl of class MyBad not found

History: UMakeCommandlet::Main

Exiting due to error
```

See the UnrealScript compiler section of [36-Troubleshooting.md](36-Troubleshooting.md) for the
common messages, their causes and their corrections.

## What you can extend

- You can extend any class that carries UnrealScript source in a listed package: `Pistol` in
  `ShockGame`, `Light` in `Engine`, and the others. To change such a class itself, compile its
  package from the exported sources (*Compile a game package from its sources*).
- You cannot extend a class that lives in a content library package (`Content\*.pkg`), for
  example the designer class `BigDaddyBouncer`. Those classes carry no UnrealScript source.
  The run stops with `Superclass BigDaddyBouncer of class MyBad not found`.
- Do not declare `native` classes or `native` functions in your package. A native class needs
  a DLL that you cannot build.

Skeletal meshes and animation are covered in
[11-Skeletal-Meshes-and-Animation.md](11-Skeletal-Meshes-and-Animation.md) and their
compile-time import in [34-Importing-Assets-with-UCC.md](34-Importing-Assets-with-UCC.md).
Ragdoll physics and the editing of pose markers and notifies are not documented.

## The logs

Every UCC run writes `System\UCC.log` and overwrites the previous one. The file starts with
the SDK's build label and the setup state, then the commandlet's messages, each with a
prefix such as `Log:`, `Warning:` or `Error:`:

```
Log: Log file open, 09/08/26 14:33:44
...
Init: Build label: Unofficial BioShock SDK Build 0.1.2
...
Init: SDK state: set up 2026-09-08T17:55:21Z by SDK Setup 0.1.2 from Steam 2007, ...
Init: Object subsystem initialized
Log: Executing Class Editor.MakeCommandlet
```

`ucc make` also copies its console text to `System\StdOut.log`. The file ends with the
trailer of the run:

```
--------------------------ShockAI - Release---------------------------
-------------------IGModEffectsSubsystem - Release--------------------
---------------------------MyMod - Release----------------------------
Success - 0 error(s), 0 warning(s)
Log file closed, 09/08/26 14:33:46
```

The trailer is `Success - N error(s), M warning(s)` or `Failure - N error(s), M warning(s)`.
The exit code of `UCC.exe` is 0 on success and 1 on failure.

`ucc make -silentbuild` leaves out the `Parsing` and `Compiling` lines. `ucc help make` lists
the options.

When the SDK Manager runs the compiler, the same output is shown in its log pane and kept
under `Logs\`.

**NOTE:** The SDK Manager's Crash report bundle includes `System\UCC.log` when it exists. See
[36-Troubleshooting.md](36-Troubleshooting.md).

## Use a compiled package in the game

**NOTE:** A map that places your classes carries them when it is saved and baked, so the game
needs no extra file. See [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).

A package of your own can also be copied as `<Package>.U` next to the game's script packages
in `<GameDir>\Builds\Release`. A compiled game package replaces the game's file of the same
name there; keep the original next to it, as the caution in *Compile a game package from its
sources* says. The game reads the values that are compiled into a package; there is nothing
to edit in the game's `.ini` files.

## Related documents

- [02-Setup.md](02-Setup.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [36-Troubleshooting.md](36-Troubleshooting.md)
- [37-Glossary.md](37-Glossary.md)
- [34-Importing-Assets-with-UCC.md](34-Importing-Assets-with-UCC.md)
- [35-UCC-Commandlet-Reference.md](35-UCC-Commandlet-Reference.md)
