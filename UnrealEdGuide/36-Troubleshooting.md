# Troubleshooting

This document lists problems that mappers meet, with the cause and the correction for each.
It also lists the known limits of the BioShock editor. Read the section for the area where the problem
appears.

## Where to look first

1. Open `System\Editor.log`. The BioShock editor writes every warning and error there.
2. Open View > Log in the BioShock editor. The log window shows the same messages while you work.
3. Search the log for `Warning`, `Error`, `Critical` and `Bake: WARNING`.
4. For a setup problem, open the SDK Manager's logs in `Logs\` in the SDK folder.

SDK logging works. These logs record SDK operations, including builds and map bakes.
UCC writes compiler messages to `System\UCC.log`.
The SDK logs do not record scripts that run in the retail game.
No reliable method is known to enable retail runtime logging.
For runtime script problems, use [Testing a script](20-Scripting-Basics.md#testing-a-script).

## Report a crash

When the BioShock editor crashes, it shows a dialog with the SDK version, your
system, and a "History" line that names the functions that were active. This line is the
most useful part of the report.

1. Press Copy in the crash dialog. The text is now on the clipboard.
2. Start the SDK Manager and press Crash report bundle.
3. Paste the text into the box. Add what you did before the crash, and the name of the map.
4. Press Create bundle. The SDK Manager writes a `.zip` file into `Logs\` and opens the
   folder.

The bundle holds the crash text, `System\Editor.log`, the script compiler's log if there is
one, the SDK's version file, the setup record and the SDK Manager's log. It holds no
game files and no maps. Send the `.zip` with your report. If you cannot start the SDK
Manager, send the copied text and `System\Editor.log`.

**NOTE:** The log is overwritten on the next start of the BioShock editor. Make the bundle
before you start it again.

## Setup and the SDK Manager

| Symptom | Cause | Correction |
| --- | --- | --- |
| The SDK Manager does not find the game. | The game was not installed through GOG or Steam, or it is on another drive. | Press Browse and select the game's install folder: the folder that holds `Builds\Release` and `Content`. |
| The SDK Manager reports "BioShock Remastered was found". | Only the Remastered edition is installed. | Install the 2007 edition. The Remastered files have another format. |
| Verify retail install reports files that differ. | A game file was changed, patched, or is damaged. The report names the file. | Restore the game files from the store client (GOG: Verify / Repair; Steam: Verify integrity of game files). Run Set up again. |
| The setup stops at "Extract effects database" with `Can't find file 'editor.BioFxExtract\...'` for every map. | The SDK folder's path has a space in it. | Uninstall, install into a folder whose path has no spaces (`D:\BioShockSDK`), and run Set up again. Delete `SoundEffects.ini`, `SoundDefs.ini`, `VisualEffects.ini` and `ModEffects.ini` from the game's `Content\Maps` folder if the failed run left them there. |
| The setup stops at "Decook content library" with a map named in the message. | The engine could not read that map, or it ran out of memory. | Press Retry step. If it fails again, verify the game files and press Retry step. |
| Validate content library reports a few packages. | Your library differs from the reference library in small ways, for example the letter case of a name. | Press Skip validation. The packages work. If the BioShock editor cannot open a named package, press Repair library. |
| The setup takes much longer than an hour. | The game is on a slow drive, or an antivirus program scans each package. | Wait. Exclude the SDK folder from the antivirus scan. |
| The Set up button is disabled and reads "Up to date". | The generated data matches the installed version. | Nothing to do. Use Repair library or Rebuild library if a package is missing. |
| "The SDK Manager is already running." | A second copy was started. | Use the window that is open. |

## Startup and configuration

| Symptom | Cause | Correction |
| --- | --- | --- |
| The BioShock editor does not start, or it reports a missing DLL. | The SDK folder is incomplete, or the Visual C++ 2005 runtime is not installed. | Run the installer again. Start the BioShock editor from the SDK Manager or from the `System` folder. |
| The BioShock editor does not start after an edit to `System\UnrealEd.ini` or `System\BioShockSDK.ini`, or it closes at once without a message. | A settings file is damaged: a broken line, a section that is missing, or a value the editor cannot read. | Press Repair SDK install in the SDK Manager. It replaces `BioShockSDK.ini`, `UnrealEd.ini` and `User.ini` from their templates and keeps the old files as `<name>.broken-<date>`. Copy a setting you want back from the old file. |
| The browsers show no content packages. | The content library is missing, or `Paths` does not include it. | Press Repair library in the SDK Manager. Confirm `Paths=../Content/*.pkg` under `[Core.System]` in `System\Default.ini` and `System\BioShockSDK.ini`. |
| Retail textures are blank or black. | `BulkContentPath` is not set. | Press Set up or Update in the SDK Manager. It writes `BulkContentPath`. See [02-Setup.md](02-Setup.md). |
| The log reports that it cannot load `Catalog.bdc`. | `BulkContentPath` points to the wrong folder. The game was moved. | Press Change retail path in the SDK Manager and select the game's folder. |
| The BioShock editor forgets settings after a restart. | `System\UnrealEd.ini` is read-only. | Clear the read-only attribute of the `System` folder. |
| No sound in the editor. | Another program holds the sound output, the computer has no output device, or the BioShock editor was started with `-NOSOUND`. | Close the other program and start the BioShock editor again. The editor works without sound; the log records that the output could not be opened. |
| Retail textures are blank in the BioShock editor, but the SDK Manager says the setup is current. | The game was updated or moved after the setup. | Press Verify retail install. Then press Change retail path if the game moved. |

## Browsers and packages

| Symptom | Cause | Correction |
| --- | --- | --- |
| A package does not appear in a browser. | The package is not loaded. | Use the browser's File > Open and select the package from `Content\`. |
| A browser's Open dialog does not show a package from an older SDK (`.u`, `.utx`, `.usx`). | The dialogs filter for `*.pkg`, the content library extension. | Change the file type in the dialog to "Legacy Packages" or "All Files". Rename the file to `.pkg` to put it on the search path. |
| A package you saved from a browser is not found by maps. | The file is outside `Content\` or does not have the `.pkg` extension. Only `Content\*.pkg` is on the search path. | Move or rename the file, or save it again into `Content\` with the default `.pkg` extension. |
| The designer classes or door classes are not in the Actor Class browser. | The content library packages are not loaded at start. | Use File > Open Package in the Actor Class browser and load `Content\ShockDesignerClasses.pkg` or `Content\Doors.pkg`. |
| A retail map's prefab package is not available in a new map. | The prefab packages are embedded in the retail maps, not in the content library. | Open the retail map. Prefabs are not supported in the BioShock editor in any case. |
| A retail `_int.bsm` map does not open. | The `_int` companion maps hold voice-over data in a format the BioShock editor does not read. | Open the main map instead. |
| The Prefab browser does nothing. | Prefabs are not supported in the BioShock editor. | Do not use prefabs. Copy and paste actors instead. |

## Viewports and rendering

| Symptom | Cause | Correction |
| --- | --- | --- |
| The viewports stutter or freeze for seconds. | The BioShock editor compiles a shader the first time it sees a material. | Wait. The stutter stops after every material in view has compiled once. |
| The Depth Complexity view mode shows nothing useful. | The mode is not supported. | Use another view mode. |
| A static mesh looks unlit or black. | The lighting bake has not run since the mesh was placed. | Run Build > Rebuild Lighting Only. |
| A surface has no texture and shows a default pattern. | The material is missing, or its package is not loaded. | Load the package in the Texture browser, or apply a material from the content library. |
| Sprites or brush wireframes do not show in the 3D view. | The view mode hides them, or the show flags are off. | Press B for brushes, H for actors, or check the viewport's show flags. |
| A selected mesh does not highlight. | Selection highlight is off. | Press S in the viewport to toggle the selection highlight. |
| The BioShock editor's image does not match the game. | The BioShock editor draws the game's bloom, but not its colour grading or post-effect stack. | Check View > Post Processing in the viewport menu is on for bloom. Judge colour grading in the game. |
| A skeletal mesh actor stands in a T-pose or default pose. | Its StartingPose names no animation or pose marker, or the animation package did not load. | Set StartingPose > AnimationName and pick a PoseName from the drop-down. Check the log for `BioPose:` lines. |

## Geometry

| Symptom | Cause | Correction |
| --- | --- | --- |
| A brush does not affect the world. | The geometry build has not run. | Run Build > Rebuild Geometry Only. |
| You see a hall of mirrors through a wall. | A surface is missing, or overlapping semisolid brushes cut a hole. | Move the overlapping brushes apart. Run Tools > Check Map for Errors. Rebuild the geometry. See [07-BSP-Geometry.md](07-BSP-Geometry.md). |
| Actors fall through the floor in the game. | The floor is a static mesh with no collision, or the BSP has a hole. | Use BSP for floors, or a mesh from the content library. |
| A zone is missing or two rooms share a zone. | A zone portal does not seal the opening. | Extend the portal sheet to cover the opening. Rebuild the geometry. See [08-Zones-and-Portals.md](08-Zones-and-Portals.md). |
| Check Map for Errors reports problems. | The map has a brush or actor problem. | Read each line in the map-check window. Fix and rebuild. |

## Lighting

| Symptom | Cause | Correction |
| --- | --- | --- |
| A light has no effect in the game. | The light was edited after the last lighting build. | Run Build > Rebuild Lighting Only, then bake again. |
| A light has no effect in the BioShock editor. | The lighting bake has not run. There is no live preview of static light. | Run Build > Rebuild Lighting Only. |
| A mesh receives only some of the lights around it. | A mesh takes at most MaxLightsStatic lights. The default is 3. | Raise MaxLightsStatic on the mesh actor, reduce the number of lights that reach the mesh, or raise the brightness of the important lights. |
| A light does not light a mesh, but lights the BSP around it. | The light and the mesh have different SpecialLitChannel values. | Set both to the same value. |
| A spotlight shows no cone. | LightCone is 0, or LightEffect is not LE_Spotlight. | Set LightEffect to LE_Spotlight and LightCone to a value such as 128. |
| Shadows on BSP look coarse. | The surface's lightmap scale is too large. | Reduce the lightmap scale in Surface Properties. |
| Faint light does not show at all. | The bake stores 8-bit values. Light below 1/255 is lost. | Raise LightBrightness or reduce the distance. |
| Rebuild Changed Lighting Only takes as long as a full build. | The BioShock editor has no changed-only lighting mode. | Use Rebuild Lighting Only. |

See [14-Lighting.md](14-Lighting.md) for the lighting model.

## Materials

| Symptom | Cause | Correction |
| --- | --- | --- |
| A material is blank in the BioShock editor but fine in the game. | The texture's data is in the bulk content, and `BulkContentPath` is wrong. | Fix `BulkContentPath`. |
| A texture import fails. | The file format is not supported. | Import bmp, pcx, tga, dds or upt files only. |

## Skeletal meshes and animation

| Symptom | Cause | Correction |
| --- | --- | --- |
| Mesh import reports "no node with a mesh and a skin". | The file holds no rigged mesh. The mesh is not parented to the armature, or the export left the skin out. | Parent the mesh to the armature with an Armature modifier. Export with Skinning on. A plain mesh is a static mesh. Import it in the Static Mesh browser. |
| The imported mesh is in the wrong pose, or its limbs are apart from the body. | The mesh or the armature had a rotation or a scale that you did not apply. The file's rest pose then does not match its bind matrices. The log says so. | Apply the rotation and the scale of both objects in Blender. Export again. |
| A material slot of the imported mesh is empty. | No loaded material has the name of the glTF material, of its base colour texture, or of that texture's file. | Open the package that holds the material before you import, or set the slot in Mesh > Mesh properties afterwards. |
| A clip is missing from the list. | Blender did not export the action. It was not the active action of the armature and not on an NLA track. | Push the action down to an NLA track. Export again. |
| Animation import shows "its skeleton does not match the mesh". | The file's bones differ from the mesh's in number, names or order. | Export the animations from the rig the mesh was imported from. Do not rename or reorder bones between exports. |
| A clip plays with too many or too few frames. | The file's keys are not evenly spaced. The importer sampled the clip again at the Rate of the dialog. | Export with sampling of every frame on, at the scene's frame rate. |
| The mesh is far too large or too small. | One glTF unit is one Unreal unit. | Import again with a Scale. Compare with a mesh of the game in the Animation browser. |
| Animation append shows a message and does nothing. | A BioShock mesh's clips come from one file. | Use File > Animation import with a file that holds every clip. |

## Bake and deploy

| Symptom | Cause | Correction |
| --- | --- | --- |
| The bake writes no file. | The output folder is read-only or does not exist. | Select a writable staging folder. Read the log. |
| The game loads the map with missing textures. | The level name does not equal the deployed file name. | Set Level name in the Bake Map dialog to the deployed name. Bake again. |
| The game loads the map with missing textures after a second map's bake. | The second bake merged into the original catalog and dropped the first map's records. | Set `DeployCatalog` and the Source Catalog to the deployed catalog. See [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md). |
| The log shows `Bake: WARNING: ... reachspecs, none with size-class bits: the path network was built by an older editor`. | The path network was built by an older editor. | Run Build > Rebuild AI Paths. Bake again. |
| The baked map is very large. | Externalize was off. | Check Externalize in the Bake Map dialog. |
| The game crashes when it loads the map. | The map places a static mesh that has no collision proxy. | Use meshes from the content library only. |
| The game shows the original level, not your bake. | The copy went to the wrong folder, or the file name differs. | Copy the `.bsm` to `<game>\Content\Maps\` with the exact slot name. |
| Copy to the game's install fails. | The files are read-only. | Clear the read-only attribute. |

## Play Map

| Symptom | Cause | Correction |
| --- | --- | --- |
| `Play Map: set [Play] GameDir=<BioShock install root> in UnrealEd.ini first` | `GameDir` is empty. | Press Set up or Update in the SDK Manager. It writes `GameDir`. Or set `GameDir` under `[Play]` in `System\UnrealEd.ini`. |
| `Play Map: game executable not found: ...` | `GameDir` is not the install root. The game was moved. | Press Change retail path in the SDK Manager, or set `GameDir` to the folder that contains `Builds\Release\Bioshock.exe`. |
| `Play Map: bake did not produce ...` | The bake failed. | Run File > Bake on the same map and read the log. A read-only `Content\Maps` folder also causes this. |
| `Play Map: could not start ... (Windows error N)` | Windows refused to start the game. | Check the path, the permissions and any antivirus block. |
| Play Map starts the game on an old version of the map. | The bake failed, and an old file remained. | Play Map deletes the old file first. If the old map still starts, check `MapName` in `[Play]`. |
| Play Map overwrote a retail level. | `MapName` was set to a retail map name. | Set `MapName` to a unique name. Restore the retail map from a backup. |
| The game shows its splash screen and crashes three to four seconds later, before the menu, whatever map is given. | The game's audio start-up probes OpenAL. When Windows has an OpenAL router installed (`OpenAL32.dll` and `wrap_oal.dll`, left behind by other games), the probe crashes the game on some machines. This is the retail game, not the SDK. | The SDK Manager writes a guard file into the game's folder on Set up, Change retail path and Verify retail install: `Builds\Release\openal32.dll`, a copy of the game's own `xinput1_3.dll`. The game then finds no OpenAL and uses DirectSound. If the file is missing, run Verify retail install, or copy `xinput1_3.dll` to that name by hand. Do not use an empty file: Windows shows an "invalid DLL" box at every start. Delete the file to undo. |

## In-game

| Symptom | Cause | Correction |
| --- | --- | --- |
| The player does not start where you expect. | There is no PlayerStart, or it is inside a wall. | Add a PlayerStart actor in open space. Bake again. |
| AI does not move. | The path network is missing or outdated. | Place PathNodes. Run Build > Rebuild AI Paths. Bake again. |
| Security bots do not move. | The map has no FlyingPathNodes, or they have no links. | Place FlyingPathNodes by hand and run Build > Rebuild AI Paths. See [26-AI-Paths.md](26-AI-Paths.md). |
| Ceiling crawlers do not move. | The BioShock editor does not generate a ceiling net. | This is a known limit. |
| Water looks wrong or does not react. | Water needs the fluid volume and the water materials from the content library. | See [15-Water-and-Fluids.md](15-Water-and-Fluids.md). |
| A light is on in the BioShock editor but off in the game. | The light's LightType is LT_None, or the light was edited after the last lighting build. | Set LightType. Run the lighting build. Bake again. |

## Spawning and gameplay systems

See documents 24 to 32. None of these systems has been extensively tested on custom maps.

| Symptom | Cause | Correction |
| --- | --- | --- |
| No AI appears in the game. | The map has no spawning manager, or the spawner is not in the baked map. | Create the spawning manager. See [27-Spawning-AI.md](27-Spawning-AI.md). Read the `Bake: registered` lines in `System\Editor.log`. |
| An AI appears without a mesh, or is invisible and unarmed. | No archetype matches it: `AIArchetypeNames` holds no section whose `AIType` fits the class, or the spawner uses a base class of the `ShockAI` package. | List a matching archetype. Use the `Spawned...` classes of `ShockAIClasses.pkg`. See document 27. |
| The bake log shows `Bake: WARNING: AIArchetypeNames entry '...' has no [...] section`. | The name is misspelled. | Correct it to a section name of `System\Spawning.ini`. |
| A splicer spawns but never walks its patrol. | The patrol points have no links, or the spawner's patrol name differs from the `PatrolName`. | See [28-Patrols.md](28-Patrols.md). |
| The Little Sister never comes out of the vent. | No Big Daddy in the vent's spawn zone, `Gatherer` missing from `AIArchetypeNames`, the vent registered in no zone, or the path build not run since the vent was placed. | See [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md). |
| The Little Sister comes out and goes back without gathering. | No corpse is registered in the zone: the `SpawnZones` list is empty or on the wrong ZoneInfo. | See [30-Corpses.md](30-Corpses.md). Read the bake log for the `whose SpawnZones list is empty` warning. |
| A pickup shows its prompt but nothing happens on use. | The pickup has no `LootSlot`. | See [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md). |
| A vending machine opens with nothing for sale. | `VendingTableName` is misspelled or names a table the game does not have. | See [25-Machines.md](25-Machines.md). |
| The player's death returns to the main menu. | No Vita-Chamber is activated. | Activate one with `ActionActivateResurrectionStation`. See document 25. |
| The hack board is a plain default board with a 10-dollar entry fee. | `HackInfoName` names no section of `System\Hacking.ini`. | See [32-Hacking.md](32-Hacking.md). |
| The alarm sounds but no bots come. | No flying path nodes 3000 to 6000 units away and out of sight, or the nodes are not linked. | See [31-Security.md](31-Security.md) and [26-AI-Paths.md](26-AI-Paths.md). |
| A camera on a custom map has no light cone. | The BioShock editor does not create the camera's spotlight actor. | Known limit. Not tested. |

## Scripting

See [20-Scripting-Basics.md](20-Scripting-Basics.md) for how scripts are wired and tested.

| Symptom | Cause | Correction |
| --- | --- | --- |
| A script never runs. | Its `TriggeredBy` does not match the `Label` of the trigger, or `scriptMessageClass` is not the message the trigger sends, or `enabled` is False. | Compare the two names letter by letter. Check the message class against the table in document 20. Set `enabled` to True. |
| A trigger does not fire for a creature. | `TriggeredOnlyByPawns` is False, or `triggeredByFilter` names other Labels. | Set the trigger's fields as described in document 20. |
| A script runs once and then never again. | The script disables itself, or the trigger is a one-time trigger. | Read the script's last actions for an `ActionSetProperty` that sets `enabled` to False. |
| An action that reads a variable has no expected result. | The variable might not exist when the action runs. | Check that an earlier action creates the variable with `ActionVariableAssign` or `ActionVariableAssignIfNotExist`. See document 21. |
| An action does nothing. | The Label in the action's field might match no actor. | Pick the Label from the drop-down on the field, or set the Label on the target actor. |
| A copied actor lost its Label. | Edit > Copy does not carry the `Label` field. | Set `Label` again on the pasted actor. |
| A condition on a variable is always false. | The variable name was typed into a field that does not accept identifiers, or the binding was lost. | Open the action's Bindings list and check that an entry names the field. See document 21. |

## UnrealScript compiler

See [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md) for how packages are
compiled. The messages are in `System\UCC.log` and on the console.

| Symptom | Cause | Correction |
| --- | --- | --- |
| The BioShock editor stops at start with `Can't find edit package '<Pkg>'`. | An `EditPackages` line names a package that has no `.u` file in `System\`: your own package is not compiled yet, or one of the game's packages was deleted. | Run `ucc make` before you start the BioShock editor, or remove the line. For one of the game's packages, copy `<Pkg>.U` back from `<GameDir>\Builds\Release` into `System\`, or press Repair library in the SDK Manager. |
| `Superclass X of class Y not found` | The class after `extends` is not in a package listed before yours: the name is misspelled, your package is listed too early, or the class lives in a content library package (`Content\*.pkg`) and carries no script. | Check the spelling. Put your `EditPackages` line after every other entry. Extend a class from a script package. |
| `Script vs. class name mismatch (A/B)` | The file `A.uc` declares `class B`. | Rename the file or the class so that both names are equal. |
| `<file>(<line>) : Error, Type mismatch in '='` | The line assigns a value of one type to a variable of another type, for example a string to an `int`. | Correct the line. |
| `'Name': Bad command or expression` | The line uses a name that is not declared in the class or its parents. | Declare the variable or function, or correct the spelling. |
| `Missing ';' in 'Class'` | The `class ... extends ...` line has no semicolon at its end. | Add the `;`. |
| `Can't find files matching <Pkg>\Classes\*.uc` | A listed package has no `.u` file and no `<Pkg>\Classes` folder with sources. The packages earlier in the list are already saved. | Create the sources, or remove the `EditPackages` line. For one of the game's packages, copy the file back as described above. |
| You edited a class, `ucc make` reports success, and nothing changed. | `System\<Pkg>.u` still exists. `ucc make` uses it as it is and compiles nothing. | Delete `System\<Pkg>.u` and run `ucc make` again. |
| `ExecWarning, Could not import non .lwo format: <file>` | `#exec STATICMESH IMPORT` accepts `.lwo` files only. | Use `#exec NEW StaticMesh File=... Name=... Package=...` for an `.ase` file. See document 34. |
| `Import texture X from Y failed` | The file was not found, or its format is not supported. The `FILE=` path is relative to `System\`. | Write the path as `..\<Package>\...`. Import bmp, pcx, tga or dds files only. |

## Known limits

- Scripts do not run inside the BioShock editor. Test them with Play Map. The BioShock
  editor does not check that a `TriggeredBy` or a Label field names an existing actor.
- The Depth Complexity view mode is not supported.
- The transform gizmo has no Alt-drag duplicate. The Ctrl mouse-button combinations still
  move, rotate and scale actors.
  See [38-Keyboard-and-Mouse-Reference.md](38-Keyboard-and-Mouse-Reference.md).
- The BioShock editor's post-processing draws the game's bloom (View > Post Processing in the
  viewport menu) but not its colour grading or post-effect stack.
- Shader compilation causes stutter the first time a material is seen.
- A corpse whose in-game pose comes from ragdoll settling shows its authored marker pose, not
  the settled one. Models attached to a posed actor are not posed.
- Prefabs are not supported.
- Karma actors (Add Karma Actor) do not apply to BioShock. The game uses Havok physics.
- Terrain and the Particle Editor are untested with BioShock content.
- The Impersonator and Music browsers are not used.
- A baked map is larger than a retail map unless Externalize is on.
- The `_int.bsm` companion maps do not open.
- FlyingPathNode nets and ceiling nets are not generated by the path build.
- A static mesh with no collision proxy crashes the game when a map places it. Every mesh
  in the content library has one. Meshes made in the BioShock editor have none.
- Rebuild Changed Lighting Only runs the full lighting bake.

## Related documents

- [02-Setup.md](02-Setup.md)
- [07-BSP-Geometry.md](07-BSP-Geometry.md)
- [08-Zones-and-Portals.md](08-Zones-and-Portals.md)
- [13-Materials-and-Textures.md](13-Materials-and-Textures.md)
- [14-Lighting.md](14-Lighting.md)
- [15-Water-and-Fluids.md](15-Water-and-Fluids.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [38-Keyboard-and-Mouse-Reference.md](38-Keyboard-and-Mouse-Reference.md)
- [11-Skeletal-Meshes-and-Animation.md](11-Skeletal-Meshes-and-Animation.md)
