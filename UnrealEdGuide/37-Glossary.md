# Glossary

This document defines the terms that the guide uses. Read it when a term in another
document is not clear. The other documents use these terms with exactly these meanings.

| Term | Meaning |
| --- | --- |
| `.blk` | A bulk content file. It holds the large texture data that is not stored inside a map or a package. `Catalog.bdc` indexes it. |
| `.bsm` | The baked map extension. The game loads these files. The BioShock editor opens them too. |
| `.pkg` | A content package file. Every content library package uses it. |
| `.t3d` | The text format for brushes and maps. The BioShock editor imports and exports it. |
| `.u` | A script package file in `System\`. |
| `.unr` | The saved map extension. File > Save writes it. |
| `#exec` | A directive in a `.uc` file that runs an import command when `ucc make` parses the class. It imports textures, static meshes and materials into the package. See [34-Importing-Assets-with-UCC.md](34-Importing-Assets-with-UCC.md). |
| action | One step of a script: an object in a Script actor's `Actions` list. Each action does one thing, for example open a door, wait, or test a condition. The Properties window shows each action as a sentence. |
| Actions list | The `Actions` property of a Script actor: the ordered list of actions that run when the script receives a message. |
| actor | Any object placed in a map: a light, a static mesh actor, a PlayerStart, a ZoneInfo, a volume. Actors have properties that you edit in the Properties window. |
| animation package | The object that holds a skeletal mesh's skeleton and clips. It lives in the mesh's group; every mesh of the group shares it. See [11-Skeletal-Meshes-and-Animation.md](11-Skeletal-Meshes-and-Animation.md). |
| ambient light | The light that a zone applies to every surface without a light source. The ZoneInfo sets it. |
| archetype | A named section of `System\Spawning.ini` that says what a spawned AI looks like: its class, mesh, skins, attachments and health. The spawning manager's `AIArchetypeNames` lists the ones a map uses. See [27-Spawning-AI.md](27-Spawning-AI.md). |
| bake (noun) | The output of File > Bake: a playable map with all referenced content embedded. |
| bake (verb) | To run File > Bake or Play Map. |
| baked map | A `.bsm` written by File > Bake. The game plays it. It embeds every object it needs. |
| bind pose | The rest pose of a skeletal mesh. An actor with no StartingPose draws in this pose. |
| clip | One animation of a skeletal mesh, named after the action it was exported from. Scripts and actor properties refer to a clip by its name. |
| binding | An entry in an action's `resolveInfoList` (the Bindings section). It feeds one field of the action from a variable or from a nested action when the action runs. |
| Booty | The class of a corpse that a Little Sister can gather ADAM from. See [30-Corpses.md](30-Corpses.md). |
| brush | A convex solid that you add to or subtract from the world. Brushes define the BSP. |
| BSP | Binary space partitioning: the world geometry that the CSG operations of the brushes produce. The BSP defines the zones, the collision of the world, and the lightmapped surfaces. |
| builder brush | The red brush. You shape it with the brush builders and then add or subtract it. It is not part of the world. |
| bulk content | `Catalog.bdc` and the `.blk` files in the game's `Content\BulkContent` folder. They hold the large texture data of the game. |
| `Catalog.bdc` | The index of the bulk content. It maps each texture to a `.blk` file, an offset and a size. An externalized bake writes a merged copy. |
| closure | The set of every object that a map references, directly or through another object. The bake embeds the closure. |
| commandlet | One command of `UCC.exe`, for example `make` or `batchexport`. `ucc <commandlet> <parameters>` runs it. See [35-UCC-Commandlet-Reference.md](35-UCC-Commandlet-Reference.md). |
| content library | The decooked packages in `<SDK folder>\Content\`. They hold the game's shared meshes, textures, materials and sounds. |
| corpse | A placed `Booty` actor. See [30-Corpses.md](30-Corpses.md). |
| CSG | Constructive solid geometry: the add, subtract, intersect and deintersect operations of the brushes. |
| cutout, masked | A material that discards pixels by an alpha mask, for example a fence or a grate. |
| deploy | To copy a baked map, its `.blk` and the merged catalog into the game's install. |
| directional light | A Light actor with LightEffect set to LE_Directionallight. It is a beam of parallel rays along the rotation of the actor. The beam is a cylinder, LightWidth wide and LightRadius deep. See [14-Lighting.md](14-Lighting.md). |
| Dynamic Light view mode | The view mode that shows the game's look: textures, static lighting, normal maps and specular. |
| editing format | The saved map format. It holds the map's own content and imports the rest from the content library. |
| `EditPackages` | The lines under `[Editor.EditorEngine]` in `System\BioShockSDK.ini` that list the script packages, in load order. The BioShock editor loads them at start; `ucc make` compiles the listed packages that have no `.u` file. |
| externalize | To write the large texture data of a bake into a `.blk` file instead of the `.bsm`. |
| fake backdrop | A surface flag that marks a surface as sky. The sky zone shows through it. Light rays pass through it. |
| FlyingPathNode | A navigation point in the air. Security bots move over them. You place them by hand. See [26-AI-Paths.md](26-AI-Paths.md). |
| game-critical action | An action with `bIsGameCritical` True. The game runs it at once, without waiting, when a level change or a save interrupts the script. |
| geometry build | Build > Rebuild Geometry Only. It runs the CSG, builds the BSP and computes the zones. |
| `Global_` variable | A script variable whose name starts with `Global_`. Every script can read it, and it survives a level change. |
| group | A named subdivision inside a package, for example `Gen_Decor.Furniture`. Objects are named `Package.Group.Name`. |
| `HackInfoName` | The property that names the section of `System\Hacking.ini` with an actor's hack puzzle. See [32-Hacking.md](32-Hacking.md). |
| HOM | Hall of mirrors: a view through a leak or a missing surface. Previous frames smear across the gap. |
| Label | The `Label` property of an actor, in the Scripting category. Scripts and actions name actors by Label. Several actors can share one Label. The action determines whether it uses all matches or only the first match. See [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md). A Label differs from `Tag` and from the level name. |
| level name | The name that the game uses for a map. It must equal the deployed file name without the extension. The Bake Map dialog sets it. |
| light shape gizmo | The wire shape that the viewports draw for a selected light. It shows the sphere, the cone or the beam that the light reaches. See [14-Lighting.md](14-Lighting.md). |
| lightmap | The per-surface texture that holds the baked light values of a BSP surface. |
| lightmap scale | The size in units of one lightmap texel on a BSP surface. A smaller value gives sharper shadows. Set in Surface Properties. |
| lighting build | Build > Rebuild Lighting Only. It runs the BioShock lighting bake. |
| lit, unlit | A lit surface receives light. An unlit actor ignores lights and shows its material at full brightness. |
| loot slot | A `LootSlot` object with a `LootSpec` that says what a pickup, a container slot or an AI holds. See [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md). |
| loot table | A section of `System\LootTables.ini`: a weighted list of items that a `LootTableSpecification` rolls on. See [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md). |
| luminance | The baked light value stored per static mesh vertex or per lightmap texel. The game multiplies it by the light colour. |
| machine | A `ShockMachine` actor that the player uses: a vending machine, the Gatherer's Garden, the Health Station, the U-Invent, the Gene Bank, Power to the People, the Bot Shutdown Panel. See [25-Machines.md](25-Machines.md). |
| material | An object that describes how a surface is drawn: its textures, colours, blending and animation. A texture is one kind of material. |
| message | The event that starts a script. A trigger, a mover, a timer, the level start or another script sends it. It carries the sender's Label and, for some classes, extra fields. |
| message class | The class of a message, for example `MessageTriggerVolumeEnter`. A Script actor's `scriptMessageClass` selects which class it listens to. |
| message filter | The `messageFilter` property of a Script actor: a message object whose filled-in fields must match the incoming message for the script to run. |
| mod package | A script package of your own: a folder `<SDK folder>\<Package>\Classes\` with `.uc` files, and the `System\<Package>.u` that the SDK Manager's Compile scripts button or `ucc make` writes from it. An `EditPackages` line is needed only when the package must load every time the BioShock editor starts. See [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md). |
| mover | A brush that moves between key positions, for example a door or a lift. |
| ortho view, orthographic view | A viewport that shows the map from the top, the front or the side without perspective. |
| package | A `.pkg` or `.u` file, or the container of objects inside a map. Objects live inside packages. |
| path build | Build > Rebuild AI Paths. It builds the path network from the PathNodes. |
| PathNode | An actor that marks a point of the AI path network. |
| perspective view | The 3D viewport. |
| Play Map | Build > Play Level, Ctrl+P, or the toolbar button. It bakes the map into the game's install and starts the game. |
| PlayerStart | The actor that marks where the player enters the map. |
| portal | See zone portal. |
| Properties window | The window that shows the properties of the selected actors (F4), surfaces (F5) or the level (F6). |
| ranged property | A numeric property with a fixed minimum and maximum, for example LightBrightness from 0 to 5. |
| realtime preview | A viewport state in which animated materials and effects play. Press P in a viewport to toggle it. |
| save | File > Save or File > Save As. Writes the editing format. |
| saved map | A `.unr` written by File > Save. It needs the content library to open. Keep it as the master copy. |
| script | In this guide, a level script: a Script actor's Actions list, edited in the Properties window with no code (documents 20 to 23). The class code of the game's packages is called UnrealScript, and a compiled `.u` file is a script package (documents 33 to 35). |
| Script actor | The actor that holds a script: a message class to listen to, a `TriggeredBy` list of Labels, and an `Actions` list. |
| ScriptableMover | A mover that scripts drive. It listens to messages named in its `TriggeredBy` and sends mover messages when it opens and closes. |
| security system | Cameras, turrets, security bots, the alarm and the Bot Shutdown Panel. See [31-Security.md](31-Security.md). |
| semisolid | A brush type that adds solid geometry but does not split the BSP of other brushes. Use it for detail such as pillars. |
| shader | The Shader material class. It combines a diffuse texture, a normal map, specular, emissive and opacity inputs into one lit material. |
| sheet | A flat, one-sided brush with no volume. Zone portals and some decals are sheets. |
| skeletal mesh | A mesh with bones that animates. Characters and animated props use it. See [11-Skeletal-Meshes-and-Animation.md](11-Skeletal-Meshes-and-Animation.md). |
| socket | A named point on a bone of a skeletal mesh where the game attaches things. In a glTF file, an Empty named `SOCKET_<name>` under a bone. |
| sky zone | The zone that holds the sky. A SkyZoneInfo marks it. Fake backdrop surfaces show it. |
| spawn zone | A `SpawnZoneInfo` of the spawning manager: a named logical zone that groups spawners, vents and corpses and holds the repopulation settings. Not a BSP zone. See [27-Spawning-AI.md](27-Spawning-AI.md). |
| spawner | An actor that creates an AI when the map starts or when a script asks: `AggressorSpawner`, `ProtectorSpawner`, `TurretSpawner`, `SecurityCameraSpawner`, `SecurityBotSpawner`. See [27-Spawning-AI.md](27-Spawning-AI.md) and [31-Security.md](31-Security.md). |
| spawning manager | The `SpawningManager` object on the LevelInfo that owns the spawn zones, the patrols, the archetype names and the AI loot. See [27-Spawning-AI.md](27-Spawning-AI.md). |
| special lit | A light or a surface with a SpecialLitChannel. A light and a receiver interact only when their channels match. |
| StartingPose | The property of a corpse or other animated-mesh actor that names the animation and pose marker it is placed in. The BioShock editor draws the actor in that pose. |
| static mesh | A mesh that does not animate. Most of the visible detail of a BioShock level is static meshes. |
| static mesh actor | An actor that places a static mesh in the map. |
| surface | One polygon face of the BSP. You select surfaces and set their material and alignment in Surface Properties. |
| the BioShock editor | This modified UnrealEd. It reads and writes BioShock's file formats. |
| the game | The retail BioShock (2007, PC) executable, `Bioshock.exe`. |
| SDK | The Unofficial BioShock SDK: the BioShock editor, the SDK Manager, `UCC.exe`, the engine files and this guide, installed together, plus the content library and the game's class sources that the SDK Manager makes from your copy of the game. |
| SDK Manager | The SDK's control panel (`System\SDKManager.exe`). It finds the game, makes the content library and the other generated files, updates them after a new version, and starts the BioShock editor. See [02-Setup.md](02-Setup.md). |
| the SDK folder | The folder where the installer put the SDK (default `C:\BioShockSDK`). It holds `System\UnrealEd.exe` and `Content\`. |
| texture | A bitmap material. Textures are the inputs of shaders and can be applied to surfaces directly. |
| transform gizmo | The colored handles on the pivot of the selected actors. Drag them to move, rotate or scale the selection, in world space or in the space of the selected actor. See [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md). |
| trigger | An actor that sends a message when something enters it: `TriggerRadius` (a sphere) or `TriggerVolume` (a brush shape). |
| TriggeredBy | The property, in the Scripting category, that lists the Labels a Script actor or a ScriptableMover listens to. Separate several Labels with commas. |
| `UCC.exe` | The command-line tool of the SDK, `System\UCC.exe`. It runs one commandlet and exits. It reads the same `System\BioShockSDK.ini` as the BioShock editor and runs from `System\`. |
| `ucc make` | The commandlet that compiles the script packages. It compiles every `EditPackages` package that has no `.u` file and writes `System\<Package>.u`. |
| units | Unreal units. The player's collision cylinder is 136 units tall and 68 units wide. Rotation uses 65536 units per full turn. |
| UnrealScript | The script language of the engine. The game's classes are written in it. Documents 33 to 35 tell you how to compile a package of your own; the guide does not teach the language. |
| variable | A named value that scripts create and read at run time with the variable actions. Its type follows its content: number, True/False, name or text. |
| vent | A `PlacedGathererVent` actor. The Little Sister comes out of it. See [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md). |
| view mode | The rendering mode of a viewport: Wireframe, Zone/Portal, Texture Usage, BSP Cuts, Textured, Lighting Only, Dynamic Light or Lit Untextured. |
| Vita-Chamber | A `ResurrectionStation` actor that revives the player. A script must activate it. See [25-Machines.md](25-Machines.md). |
| volume | An actor with a brush shape that marks a region, for example a blocking volume or a fluid volume. |
| watcher | An object that a script creates to test a condition every second and send a `MessageWatcher` when it becomes true. |
| zone | A region of the BSP separated from other regions by zone portals. Each zone has its own ambient light, fog and sound settings. |
| zone portal | A sheet brush with the portal flag. It splits the BSP into zones. |
| ZoneInfo | The actor that sets the properties of the zone it is placed in. A zone with no ZoneInfo uses the level defaults. |

## Related documents

- [01-Introduction.md](01-Introduction.md)
- [05-Concepts-for-Hammer-Radiant-and-UE-Users.md](05-Concepts-for-Hammer-Radiant-and-UE-Users.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [20-Scripting-Basics.md](20-Scripting-Basics.md)
- [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md)
