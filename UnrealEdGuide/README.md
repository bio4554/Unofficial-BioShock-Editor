# Unofficial BioShock SDK Guide

This guide tells you how to use the Unofficial BioShock SDK to make and change content for
BioShock (2007, PC): levels, and the script packages that hold the game's classes and
assets. The SDK is the BioShock editor (a modified UnrealEd, the Unreal Engine 2 level
editor, that reads and writes BioShock's file formats), the SDK Manager, the `UCC.exe`
command-line tool, and the content library and class sources that the SDK Manager makes
from your own copy of the game. The guide is for mappers and modders. It assumes that you
know a level editor such as Hammer, Radiant or a newer Unreal Engine editor. It does not
assume that you know UnrealEd, UnrealScript or BioShock's engine.

## Before you start

You need three things:

- The SDK, installed with its installer. The install folder (default `C:\BioShockSDK`) is
  the SDK folder. It holds `System\UnrealEd.exe`, `System\SDKManager.exe`, `System\UCC.exe`,
  the engine DLLs, the `.ini` files and the script packages.
- A retail install of BioShock (2007, PC). The disc, Steam and GOG releases of the original
  game are all this version. BioShock Remastered is not supported.
- The content library: the folder `Content\` in the SDK folder. The SDK Manager makes it
  from your own copy of the game at the first setup. See [02-Setup.md](02-Setup.md).

## How to read this guide

- Read documents 01 to 05 first. They explain what the SDK is, the setup, and the BioShock
  editor's windows and controls.
  Document 05 maps the concepts you know from Hammer, Radiant or a newer Unreal Engine to the
  BioShock editor.
- Read document 06 before you block out a map. It explains how the retail levels are built.
- Read documents 07 to 17 when you build a map: geometry, zones, actors, static meshes,
  skeletal meshes, doors, materials, lighting, water, sound and effects.
- Read document 18 before you test a map in the game, and document 19 before you open one of
  the game's own maps.
- Read documents 20 to 23 when you make events happen in a map: doors that open, effects,
  sequences, quests. Document 20 explains the scripting system; 21 to 23 are references.
- Read documents 24 to 32 when you put the game's own systems in a map: pickups and loot,
  machines, AI paths, spawning, patrols, Little Sisters, corpses, security, hacking. None of
  these systems has been extensively tested on custom maps; every document says so.
- Read documents 33 to 35 when you compile a script package of your own or one of the
  game's packages from its sources, import assets at compile time, or run a commandlet by
  hand.
- Use documents 36 to 38 as references: troubleshooting, the glossary, and the keyboard and
  mouse reference.

Documents 12 and 16 are not written yet. Each one says where its material is today.

Conventions in this guide:

- Menu paths use the form `File > Bake`.
- Property names, file names and console commands use `code` font.
- **CAUTION:** marks an action that can lose data or crash the game.
- **NOTE:** marks a useful fact.
- Distances are in units. The player's collision cylinder is 136 units tall and 68 units
  wide. Use it as the scale reference.

## Documents

| Document | Contents |
| --- | --- |
| [01-Introduction.md](01-Introduction.md) | What the SDK is, what you can do with it, the level and package workflows. |
| [02-Setup.md](02-Setup.md) | The installer, the SDK Manager and the first setup, the SDK folder, the `.ini` settings, the first start, and the log. |
| [03-Interface-Tour.md](03-Interface-Tour.md) | The main window, the toolbars, the browsers, and the Properties windows. |
| [04-Viewports-and-Navigation.md](04-Viewports-and-Navigation.md) | Viewport types, camera controls, view modes, and the grid. |
| [05-Concepts-for-Hammer-Radiant-and-UE-Users.md](05-Concepts-for-Hammer-Radiant-and-UE-Users.md) | How the BioShock editor's concepts map to Hammer, Radiant, and newer Unreal Engine editors. |
| [06-BioShock-Mapping-Guidelines.md](06-BioShock-Mapping-Guidelines.md) | How the retail levels are built: the BSP shell plus kit meshes, the 256-unit module, zoning, ambient and lighting presets, order of work. |
| [07-BSP-Geometry.md](07-BSP-Geometry.md) | The builder brush, CSG operations, brush editing, surface properties, and the geometry build. |
| [08-Zones-and-Portals.md](08-Zones-and-Portals.md) | Zones, zone portals, ZoneInfo, the sky zone, and volumes. |
| [09-Actors-and-Properties.md](09-Actors-and-Properties.md) | Placing, selecting, moving actors, and the Properties window. |
| [10-Static-Meshes.md](10-Static-Meshes.md) | The Static Mesh browser, the content library, placement, collision, and import. |
| [11-Skeletal-Meshes-and-Animation.md](11-Skeletal-Meshes-and-Animation.md) | Skeletal meshes and their animations: the Animation browser, preparing and exporting a model in Blender, the glTF import, replacing animations, placing an animated mesh in a map. |
| [12-Doors.md](12-Doors.md) | Not written yet. The game's doors, their keypads and buttons, and the AI path through them. |
| [13-Materials-and-Textures.md](13-Materials-and-Textures.md) | The Texture browser, BioShock's material classes, the Shader properties, and texture import. |
| [14-Lighting.md](14-Lighting.md) | BioShock's lighting system: light properties, the static bake, ambient light, and the lighting workflow. |
| [15-Water-and-Fluids.md](15-Water-and-Fluids.md) | Water materials, fluid volumes, and caustics. |
| [16-Sound-and-Music.md](16-Sound-and-Music.md) | Not written yet. Ambient sounds, actor sounds, music, audio diaries. |
| [17-Visual-Effects.md](17-Visual-Effects.md) | The ready-made effects in `FXClass.pkg`, which to place where, tuning a placed effect, decal projectors, building an effect from an empty Emitter. |
| [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md) | The Build menu, Save and Bake, deployment, and Play Map. |
| [19-Working-with-Retail-Maps.md](19-Working-with-Retail-Maps.md) | Opening, editing, and re-baking the game's own maps. |
| [20-Scripting-Basics.md](20-Scripting-Basics.md) | The level scripting system: Script actors, messages, Labels, triggers, the Actions list, a first script, and testing. |
| [21-Scripting-Logic-and-Variables.md](21-Scripting-Logic-and-Variables.md) | Conditions, loops, variables, bindings, timers, watchers, calling other scripts, and the general actions. |
| [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md) | The actions that drive the game's systems: doors, quests, facts, machines, the player, AI, effects, the environment. |
| [23-Scripting-Examples.md](23-Scripting-Examples.md) | Real scripts from the game's own maps, explained, and the patterns the game's designers used. |
| [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md) | Weapon, ammunition and item pickups, loot slots, the loot tables, and what an AI carries. |
| [25-Machines.md](25-Machines.md) | Vending machines and their stock, the Gatherer's Garden, the Health Station, the U-Invent, the Gene Bank, Power to the People, the Vita-Chamber. |
| [26-AI-Paths.md](26-AI-Paths.md) | PathNodes and the path build. |
| [27-Spawning-AI.md](27-Spawning-AI.md) | The spawning manager, spawn zones, the AI spawners, the archetypes in `Spawning.ini`, what the bake embeds and registers. |
| [28-Patrols.md](28-Patrols.md) | PatrolPoints, the PatrolList on the spawning manager, and the spawner's patrol fields. |
| [29-Little-Sisters-and-Gatherer-Vents.md](29-Little-Sisters-and-Gatherer-Vents.md) | The vent, the Big Daddy escort, what the path build and the bake do with a vent, and the ADAM the player gets. |
| [30-Corpses.md](30-Corpses.md) | Gatherable corpses: classes, placement height, poses, the ZoneInfo registration, player loot. |
| [31-Security.md](31-Security.md) | Camera, turret and security bot spawners, the alarm, the Bot Shutdown Panel, and the flight network. |
| [32-Hacking.md](32-Hacking.md) | Which actors can be hacked, `HackInfoName` and the sections of `Hacking.ini`, what a hack and a failure do. |
| [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md) | `UCC.exe`, the three kinds of script packages and their sources, the recompile rule, compiling a package of your own from the SDK Manager or a command prompt, compiling a game package from its exported sources, and the compiler's log. |
| [34-Importing-Assets-with-UCC.md](34-Importing-Assets-with-UCC.md) | Compile-time imports with `#exec`: textures, static meshes, skeletal meshes and animations, materials with a properties file, and editor-saved packages. |
| [35-UCC-Commandlet-Reference.md](35-UCC-Commandlet-Reference.md) | The supported commandlets: `help`, `make`, `batchexport`, `analyzecontent`, the load and class-flag tests, and the ones the SDK Manager runs (`editor.BioDecookLibrary`, `editor.BioExportPackage`). |
| [36-Troubleshooting.md](36-Troubleshooting.md) | Symptoms, causes, corrections, and known limits. |
| [37-Glossary.md](37-Glossary.md) | Terms used in this guide. |
| [38-Keyboard-and-Mouse-Reference.md](38-Keyboard-and-Mouse-Reference.md) | Keyboard shortcuts and mouse combinations. |

## Not covered

Script packages of your own are covered in documents 33 to 35. Skeletal meshes and animation
are covered in document 11 and static mesh collision in document 10; the game's ragdoll
physics and the editing of pose markers and notifies are not covered. The gameplay systems of documents 24 to 32
are documented from the game's own maps and from a few tests on custom maps. None of them
has been extensively tested on custom maps, and every one of those documents says so.
