# Introduction

This document tells you what the SDK is, what you can do with it, what you need before you
start, and how the rest of this guide is organized. Read it first.

## What the SDK is

The Unofficial BioShock SDK is a set of tools for making and changing content for BioShock
(2007, PC). The game runs on a modified Unreal Engine 2 and shipped with no tools. The SDK
gives you:

| Part | What it is |
|---|---|
| The BioShock editor | `System\UnrealEd.exe`. A modified UnrealEd, the Unreal Engine 2 level editor, that reads and writes BioShock's file formats. It draws with a renderer written for the SDK that runs the game's shaders, so a perspective viewport shows close to what the game shows. |
| The SDK Manager | `System\SDKManager.exe`. The control panel of the SDK. It finds the game, makes the content library and the other generated files from your own copy of the game, keeps them current after an update, compiles script packages, repairs the install, and starts the BioShock editor. |
| UCC | `System\UCC.exe`. The command-line tool: the UnrealScript compiler and the other commandlets. The SDK Manager runs it for you; you can also run it yourself. |
| The content library | `Content\`. The game's meshes, textures, materials and sounds as packages the BioShock editor opens. The SDK Manager makes it from your copy of the game at the first setup. It is not part of the installer. |
| The game's sources | `<Package>\Classes\` and `<Package>\Import\` for each of the game's script packages: the class code and the content those classes use, exported from your copy of the game at setup, ready to edit and compile. |
| This guide | `UnrealEdGuide\`. |

## What you can do

- Create new levels.
- Edit the game's levels.
- Script events in a level: doors, effects, sequences, quests, with the level scripting
  system, without code.
- Place the game's systems in a level: pickups, machines, AI spawning, patrols, security,
  hacking.
- Create new script packages: classes of your own, in UnrealScript, that extend the game's.
- Edit the game's script packages, compiled from their exported sources.
- Import your own assets, textures, static meshes and materials, into a script package.
- Bake a level into the format the game loads, and play it in the game.

## What you cannot do

The SDK is not a game. You play your levels on your own copy of BioShock.

- BioShock Remastered is not supported. The SDK reads and writes the 2007 formats only.
- Native code is not supported. A class that needs a DLL cannot be built.
- The game's Havok content (animation, skeleton and collision data) is experimental and not
  documented.

See [36-Troubleshooting.md](36-Troubleshooting.md) for the full list of known limits.

## What you need

| Item | Description |
|---|---|
| The SDK folder | The folder where the installer put the SDK. Default `C:\BioShockSDK`. It holds `System\UnrealEd.exe`, `System\SDKManager.exe`, `System\UCC.exe`, the engine DLLs, the `.ini` files and the `System\*.u` script packages. |
| The game | A retail install of BioShock (2007, PC). Example: `C:\Games\BioShock`. The disc, Steam and GOG releases of the original game are all correct. BioShock Remastered is not supported. |
| The content library | The folder `Content\` inside the SDK folder, filled with the decooked content packages (`Gen_Decor.pkg`, `Gen_Doors.pkg`, `Gen_Water.pkg`, ...). The SDK Manager makes it from your own copy of the game at the first setup. |

See [02-Setup.md](02-Setup.md) for the settings that connect these three parts.

## The level workflow

A level goes through these steps. The steps are the same for a new level and for an edited
level of the game.

1. Author the geometry with brushes. See [07-BSP-Geometry.md](07-BSP-Geometry.md).
2. Place static meshes, lights, zone infos, volumes and other actors. See
   [09-Actors-and-Properties.md](09-Actors-and-Properties.md),
   [10-Static-Meshes.md](10-Static-Meshes.md) and [14-Lighting.md](14-Lighting.md).
3. Run the geometry build (Build > Rebuild Geometry Only).
4. Run the lighting build (Build > Rebuild Lighting Only).
5. Run the path build (Build > Rebuild AI Paths).
6. Save the map (File > Save). A saved map is your working file.
7. Bake the map (File > Bake). A baked map is the file that the game loads.
8. Copy the baked map into the game, or press Play Map (Build > Play Level, Ctrl+P) to
   bake and start the game in one step.

**NOTE:** A saved map is a `.unr` file and a baked map is a `.bsm` file. They are not the
same thing. Keep working in saved maps. Bake only to play.

See [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md) for steps 3 to 8.

## The package workflow

A script package goes through these steps. See [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md).

1. Write the class files in `<Package>\Classes\`, or edit the exported files of one of the
   game's packages.
2. Compile the package: press Compile scripts in the SDK Manager, or run `ucc make`.
3. Use the classes in the BioShock editor, or copy the package into the game.

## The guide map

| Document | Contents |
|---|---|
| [01-Introduction.md](01-Introduction.md) | This document. |
| [02-Setup.md](02-Setup.md) | The installer, the SDK Manager, the first setup, the SDK folder, `.ini` settings, first start, the log. |
| [03-Interface-Tour.md](03-Interface-Tour.md) | The main window, toolbars, browsers and Properties windows. |
| [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md) | Viewport types, camera controls, view modes, grid. |
| [05-Concepts-for-Hammer-Radiant-and-UE-Users.md](05-Concepts-for-Hammer-Radiant-and-UE-Users.md) | How the BioShock editor's ideas map to Hammer, Radiant and newer Unreal Engine editors. |
| [06-BioShock-Mapping-Guidelines.md](06-BioShock-Mapping-Guidelines.md) | How the retail levels are built, kit sizes, presets, order of work. |
| [07-BSP-Geometry.md](07-BSP-Geometry.md) | Brushes, CSG, surfaces, the geometry build. |
| [08-Zones-and-Portals.md](08-Zones-and-Portals.md) | Zones, zone portals, ZoneInfo, sky zones, fog and ambient light. |
| [09-Actors-and-Properties.md](09-Actors-and-Properties.md) | Placing, moving and editing actors. |
| [10-Static-Meshes.md](10-Static-Meshes.md) | The static mesh browser, the content library, collision. |
| [11-Skeletal-Meshes-and-Animation.md](11-Skeletal-Meshes-and-Animation.md) | Skeletal meshes and their animations, the glTF import, placing an animated mesh. |
| [12-Doors.md](12-Doors.md) | Not written yet. Doors, keypads and buttons. |
| [13-Materials-and-Textures.md](13-Materials-and-Textures.md) | Textures, shaders, material classes, import. |
| [14-Lighting.md](14-Lighting.md) | BioShock's lighting system and the lighting build. |
| [15-Water-and-Fluids.md](15-Water-and-Fluids.md) | Water volumes and water materials. |
| [16-Sound-and-Music.md](16-Sound-and-Music.md) | Not written yet. Sounds, music, audio diaries. |
| [17-Visual-Effects.md](17-Visual-Effects.md) | Not written yet. Effects and effect actors. |
| [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md) | The Build menu, save, bake, deploy, Play Map. |
| [19-Working-with-Retail-Maps.md](19-Working-with-Retail-Maps.md) | Opening, editing and re-baking the game's own maps. |
| [20-Scripting-Basics.md](20-Scripting-Basics.md) | Script actors, messages, Labels, triggers, the Actions list, a first script. |
| [21-Scripting-Logic-and-Variables.md](21-Scripting-Logic-and-Variables.md) | Conditions, loops, variables, bindings, timers, the general actions. |
| [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md) | The actions that drive the game's systems. |
| [23-Scripting-Examples.md](23-Scripting-Examples.md) | Real scripts from the game's own maps, explained. |
| [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md) | Pickups, loot slots, loot tables, AI loot. |
| [25-Machines.md](25-Machines.md) | Vending machines, the Gatherer's Garden, the Health Station, the U-Invent, the Gene Bank, Power to the People, the Vita-Chamber. |
| [26-AI-Paths.md](26-AI-Paths.md) | PathNodes and the path build. |
| [27-Spawning-AI.md](27-Spawning-AI.md) | The spawning manager, spawn zones, spawners and archetypes. |
| [28-Patrols.md](28-Patrols.md) | Patrol points and patrol lists. |
| [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md) | Vents, the Big Daddy escort, the ADAM payout. |
| [30-Corpses.md](30-Corpses.md) | Gatherable corpses and their registration. |
| [31-Security.md](31-Security.md) | Cameras, turrets, security bots, the alarm, the Bot Shutdown Panel. |
| [32-Hacking.md](32-Hacking.md) | Hackable actors, `HackInfoName`, the puzzle sections. |
| [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md) | `UCC.exe`, the script packages and their sources, compiling a package of your own, compiling a game package from its sources, the compiler's log. |
| [34-Importing-Assets-with-UCC.md](34-Importing-Assets-with-UCC.md) | Compile-time imports of textures, static meshes and materials with `#exec`. |
| [35-UCC-Commandlet-Reference.md](35-UCC-Commandlet-Reference.md) | The supported `UCC.exe` commandlets. |
| [36-Troubleshooting.md](36-Troubleshooting.md) | Symptoms, causes and known limits. |
| [37-Glossary.md](37-Glossary.md) | Terms used in this guide. |
| [38-Keyboard-and-Mouse-Reference.md](38-Keyboard-and-Mouse-Reference.md) | All shortcuts and mouse combinations. |

## How to read this guide

- Menu paths are written with a ">" between levels: File > Bake.
- Property names, file names, class names and console commands are in code font:
  `LightRadius`, `UnrealEd.ini`, `Light`, `LIGHT BIOBAKE`.
- Keyboard shortcuts are written as Ctrl+P. "Left drag" means hold the left mouse button
  and move the mouse.
- Distances are in units. The player's collision cylinder is 136 units tall and 68 units
  wide. Use it as the scale reference. Rotations in the Properties window use 65536 per
  full turn, so 16384 is 90 degrees.
- **CAUTION:** marks a step that can lose data or crash the game.
- **NOTE:** marks a useful fact.

Procedures are numbered lists. Do the steps in order.

If you know Hammer, Radiant or a newer Unreal Engine editor, read
[05-Concepts-for-Hammer-Radiant-and-UE-Users.md](05-Concepts-for-Hammer-Radiant-and-UE-Users.md)
before you build your first level.

## What this guide does not cover

- The exact rules of AI behaviour in the game: repopulation, combat, where the alarm puts
  its bots. Documents 24 to 32 tell you what to place and set for each system, and what
  has not been tested on custom maps.
- The UnrealScript language. [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md)
  tells you how to compile a package, not how to write one.
- The game's Havok content: animation, skeleton and collision data. Support for it is
  experimental and not documented.

## Related documents

- [02-Setup.md](02-Setup.md)
- [03-Interface-Tour.md](03-Interface-Tour.md)
- [05-Concepts-for-Hammer-Radiant-and-UE-Users.md](05-Concepts-for-Hammer-Radiant-and-UE-Users.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md)
- [36-Troubleshooting.md](36-Troubleshooting.md)
