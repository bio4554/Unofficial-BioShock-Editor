# Scripting Basics

This document explains BioShock's level scripting: a Script actor that holds an ordered list of
actions and runs them when a message arrives. There is no node graph. You edit every part of a
script in the Properties window. Level scripting is not UnrealScript: you write no code. To
compile UnrealScript classes of your own, see
[33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md). Read this document before
you make a door open, a light change, an enemy spawn or a sound play in response to the
player. It is the first of four scripting documents. [21-Scripting-Logic-and-Variables.md](21-Scripting-Logic-and-Variables.md) covers
conditions, loops and variables. [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)
lists the actions. [23-Scripting-Examples.md](23-Scripting-Examples.md) shows complete scripts from
the game's own maps.

## Before you start

- You can place actors and edit their properties in the Properties window. See
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- You can bake a map and start it in the game with Play Map. See
  [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).
- The map has a floor, a PlayerStart and a built geometry. Scripts run only in the game.

## How scripting works

### The Script actor

A Script is an actor. You place it in the map like a light. It draws as a sprite in the BioShock
editor and is invisible in the game. Its position has no meaning. A Script has one list of actions,
`Actions`, and a small set of properties that say which event starts the list. The Scripting
package in the SDK folder provides the Script class and the trigger classes; it is always
loaded.

### Messages

Every event in the game is a message. A trigger sends a message when the player enters it. A mover
sends a message when it starts and when it stops. The level sends a message when it starts. A script
sends a message when its timer expires, or when it runs the action `ActionSendTriggerMessage`. A
message has a class, for example `MessageTriggerEnter`, and it carries the Label of the actor that
sent it. Some message classes carry more fields, for example the Label of the pawn that entered the
trigger.

A Script listens to messages. When a message arrives that the Script accepts, the Script runs its
`Actions` list from the top.

### Labels

Every actor has a `Label` property in the Scripting category of the Properties window. A Label is a
name. It cannot contain spaces. Many scripts and actions refer to actors by Label.
An action that opens a door holds the door's Label in its `DoorLabel` field. A Script's
`TriggeredBy` field holds the Label of the trigger it listens to.

Rules for Labels:

- Labels are not case sensitive. `LobbyTrigger` and `lobbytrigger` are the same Label.
- The player's Label is `Player`. You do not set it.
- Several actors can have the same Label. The action determines how many matching actors it uses.
  Some actions use every match. Others use only the first match.
  Check the action's entry in [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md) before you use a shared Label.
  Use a unique Label when an action must reach one specific actor.
- Set a Label on every actor that a script must reach: triggers, doors, lights, movers, markers,
  scripts. An actor that no script names does not need a Label.
- `Label` is different from `Tag` in the Events category, and from the level name that the bake
  writes. Scripts use only `Label`.

If you know Radiant, a Label is a targetname. If you know Hammer, a Label is the entity name that
an output names as its target. The difference is the direction: in BioShock the receiver names the
sender.

### TriggeredBy

`TriggeredBy` is a text field in the Scripting category. It holds the Labels of the actors the
Script listens to, separated by commas: `DoorTrigger, LobbyTrigger`. Spaces after the commas are
allowed. A Script with an empty `TriggeredBy` listens to nothing. Another script can still start it
with `ActionNonBlockingExecuteScript` or `ActionBlockingExecuteScript`. The game's own maps hold
many such helper scripts.

In the game's own maps, the value `all` is used in `TriggeredBy` for messages whose sender is not
known in advance, for example `MessagePawnDied`, `MessageAISpawned`, `MessagePawnTookDamage`
and `MessageLevelStarted`. The Script then accepts that message class from any sender.

### scriptMessageClass and messageFilter

`scriptMessageClass` is the message class the Script accepts. Pick it from the drop-down. The
Script accepts the class and every subclass of it. The base class `Message` accepts every message
from the actors named in `TriggeredBy`; the game's own maps use it when a script listens to a
sender that only sends one kind of message.

`messageFilter` narrows the accepted messages further. It is optional. See "Message filters"
below.

A message is accepted when all three tests pass: the sender's Label is in `TriggeredBy`, the class
matches `scriptMessageClass`, and the filter matches.

### The Actions list

`Actions` is an ordered list. The Script runs the entries from the top to the bottom. Every entry
is one action, shown as a sentence, for example `Wait 2 seconds` or `Open door LobbyDoor`. Some
actions take time: `ActionWait` pauses the list for the given seconds, `ActionCinematicFadeView`
does not finish until the fade is complete, `ActionWaitForGoal` waits for an AI to reach its goal.
The next action starts when the current one finishes. Control actions such as `ActionIf` and
`ActionLoop` hold their own nested lists. See
[21-Scripting-Logic-and-Variables.md](21-Scripting-Logic-and-Variables.md).

A Script runs one message at a time. A message that arrives while the Script runs is queued. When
the list finishes, the Script takes the next queued message and runs the list again from the top.

### enabled

`enabled` is the master switch of a Script. A disabled Script ignores every message and refuses to
be started by another script. A script can change the switch at run time with `ActionSetProperty`:
set `Object` to the Script's Label, `Property` to `enabled` and `NewValue` to `true` or `false`. The
game's own maps make a script run one time by putting `Set <own Label>.enabled = False` as its last
action.

### If you know Hammer or Kismet

In Hammer, you wire a `trigger_multiple` output to a `logic_relay` input, and the relay fires its
own outputs with delays. In BioShock, the trigger's Label in `TriggeredBy` is the wire, the Script is
the relay, and the `Actions` list with `ActionWait` entries is the set of delayed outputs.

In UE4 Kismet or Blueprint, a script is one linear sequence node chain without the wires: the event
node is `scriptMessageClass` plus `TriggeredBy`, and the sequence is the `Actions` list.

## Your first script

This procedure places a trigger and a script that prints a line on the screen when the player walks
into the trigger.

1. In the Actor Class browser, select Trigger > TriggerRadius.
2. Right-click the floor where the player walks and click **Add TriggerRadius here**.
3. Press F4. In the Collision category, set `CollisionRadius` to 256 and `CollisionHeight` to 128.
   The default cylinder is 40 by 40 units, smaller than the player. The game's own maps use a
   radius of 256 to 500 and a height of 100 to 200.
4. Expand the Scripting category. Set `Label` to `HelloTrigger`. Press Enter.
5. In the Actor Class browser, select Script.
6. Right-click near the trigger and click **Add Script here**.
7. Press F4. Expand the Scripting category.
8. Set `TriggeredBy` to `HelloTrigger`. Press Enter.
9. Click `scriptMessageClass` and pick `MessageTriggerEnter` from the drop-down.
10. Click the `Actions` row. Click the **Add** button that appears at the right end of the row. A
    new row `[0]` appears and opens. Its first child row reads `New`.
11. Click the `New` row. Pick `ActionPrintClientMessage` in the drop-down, then click the **New**
    button at the right end of the row. The row now shows the action's fields.
12. Set `MessageText` to `Hello from HelloTrigger`. Press Enter. The `[0]` row now reads
    `Print 'Hello from HelloTrigger' to the HUD.`
13. Save the map. Run Build > Play Level (Ctrl+P).
14. In the game, walk into the trigger. The text prints on the screen.

The screen message confirms that the action ran. The Script needs no `Label` of its own for this example.
Set a `Label` when another script must start it, stop it or change its `enabled` switch.

If nothing prints, see "Testing a script" below.

## The Script actor's properties

The Script class hides the categories that have no meaning for it. The Properties window shows
`LightColor` and `Scripting`. The Scripting category holds these rows.

| Property | Meaning | Default |
| --- | --- | --- |
| `Actions` | The ordered list of actions. Click the row for the **Add** and **Empty** buttons. | empty |
| `enabled` | The master switch. A disabled Script ignores messages and cannot be started by another script. | True |
| `Label` | The Script's own name. Other scripts use it in `targetScript`, `scriptLabel` and in `ActionSetProperty`. Timer and trigger messages sent by this Script carry it. | None |
| `messageFilter` | An optional message object. Only messages whose fields match the filled-in fields of the filter are accepted. | None |
| `ShouldExecuteCriticalActionsImmediately` | When True the Script does not wait: it runs only the actions whose `bIsGameCritical` is True, all at once. The game's own maps never set it. Leave it False. | False |
| `scriptMessageClass` | The message class the Script accepts, with its subclasses. | None |
| `TriggeredBy` | Comma-separated Labels of the senders the Script listens to. Empty means the Script listens to nothing. | empty |

Every other actor shows `Label` and `TriggeredBy` in a Scripting category too. On a trigger, a
door or a light you set only `Label`. `TriggeredBy` has a meaning on a Script and on a
ScriptableMover.

## Event sources

### TriggerRadius

A TriggerRadius is a cylinder. It sends `MessageTriggerEnter` when an actor enters the cylinder and
`MessageTriggerExit` when the actor leaves. Place it from the Actor Class browser under Trigger. It
draws as a trigger sprite and is invisible in the game. Its collision cylinder is in the Collision
category. Its own settings are in a category named `None`.

| Property | Meaning | Default |
| --- | --- | --- |
| `CollisionRadius`, `CollisionHeight` | The trigger cylinder. Height is the half height. | 40, 40 |
| `TriggeredOnlyByPawns` | Only pawns trigger it: the player and AI. | True |
| `triggeredByFilter` | A list of Labels. When it holds entries, only actors with those Labels trigger it. The game's own maps put `Player` here. | empty |
| `Disabled` | The trigger sends nothing. A script can change it with `ActionSetProperty`, `Property` = `Disabled`. | False |
| `MaxEnterCount`, `MaxExitCount` | How many times the trigger sends its message. `-1` means no limit. `1` means one time. | -1, -1 |
| `AllowTheDeadToTriggerOnEnter`, `AllowTheDeadToTriggerOnExit` | Dead pawns count too. | False |
| `RequireClearTrace` | A trace from the trigger to the actor must reach the actor. | False |
| `RequireIsVisible` | The actor must be able to see the trigger. | False |

### TriggerVolume

A TriggerVolume is a brush volume. It sends `MessageTriggerVolumeEnter` when a pawn enters the
volume and `MessageTriggerVolumeExit` when the pawn leaves. Shape the builder brush, right-click
the Volume button in the button bar and select TriggerVolume. See
[08-Zones-and-Portals.md](08-Zones-and-Portals.md). The volume draws with a magenta wireframe. Its
settings are in the TriggerVolume category.

| Property | Meaning | Default |
| --- | --- | --- |
| `TriggerOnlyByLabel` | A list of Labels. When it holds entries, only pawns with those Labels trigger the volume. The game's own maps put `Player` here on most volumes. | empty |
| `TriggerOnlyByClass` | A list of pawn classes that trigger the volume. | empty |
| `TriggerOnlyOnce` | The volume sends its enter message one time and its exit message one time. | False |
| `OnlyTriggerForLivingPawns` | Dead pawns do not trigger the volume. | True |
| `Disabled` | The volume sends nothing. A script can change it with `ActionSetProperty`, `Property` = `Disabled`. | False |
| `DropPriorityOnFirstEnterIfTriggerOnlyOnce` | After the first enter, the volume stops being the pawn's physics volume. | True |

The game's own maps use TriggerVolume far more than TriggerRadius. Most of them set
`TriggerOnlyByLabel` to `Player` and `TriggerOnlyOnce` to True.

### ScriptableMover

A ScriptableMover is a mover that scripts drive. Shape the builder brush, right-click the Add Mover
button in the button bar and select ScriptableMover. Set its keyframes as for any mover: see
"Movers" in [09-Actors-and-Properties.md](09-Actors-and-Properties.md).

The mover listens to messages, like a Script. Its `TriggeredBy` holds the Labels of the scripts
that move it. When one of those scripts runs `ActionSendTriggerMessage`, the mover receives a
`MessageTrigger` and behaves as if it was triggered. Set `InitialState` to `TriggerToggle` so that
each message moves it to the other keyframe. The game's own maps use `TriggerToggle` on almost every
ScriptableMover, and `TriggerOpenTimed` with `StayOpenTime` = 0 on a few.

| Property | Meaning | Default |
| --- | --- | --- |
| `TriggeredBy` | Labels of the scripts whose `ActionSendTriggerMessage` moves the mover. | empty |
| `triggerMessageType` | The message class the mover listens to. Leave it. | MessageTrigger |
| `bDisableScriptLogs` | Suppresses mover diagnostics in the script source. Does not enable retail runtime logging. | False |
| `InitialState` | `TriggerToggle`, `TriggerOpenTimed` and the other mover states. | |
| `MoveTime`, `StayOpenTime`, `bTriggerOnceOnly` | As for any mover. | |

The mover also sends messages. A Script with `TriggeredBy` = the mover's Label and
`scriptMessageClass` = `MessageMoverOpened` runs when the mover reaches its last keyframe. The
other classes are `MessageMoverOpening`, `MessageMoverClosing`, `MessageMoverClosed`,
`MessageMoverBump` and `MessageMoverPlayerBump`.

**NOTE:** Do not use `ActionOpenDoor` on a mover. `ActionOpenDoor`, `ActionCloseDoor`,
`ActionLockDoor` and `ActionUnlockDoor` work on the game's door classes from the content library.
A mover is driven with `ActionSendTriggerMessage`.

### Level start

The level sends `MessageLevelStarted` when the map starts, and `MessageSavegameRestored` when the
player loads a saved game in the map. The sender is the level itself. In the game's own maps, a
level-start script has `scriptMessageClass` = `MessageLevelStarted` and `TriggeredBy` = the level
name, for example `0-Lighthouse` or `4-recreation`. The level name is the file name of the baked
map without the extension, unless you type another one in the Bake Map dialog. See
[18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).

**NOTE:** Play Map bakes the map under the `MapName` from `UnrealEd.ini`, `EdPlay` by default. A
level-start script with `TriggeredBy` = `MyMap` does not run under Play Map. Use `TriggeredBy` =
`all`, as some of the game's own level-start scripts do, or set `TriggeredBy` to the Play Map name
while you test.

The game's own maps use level-start scripts to set variables, to preset lights and to open the
entry doors. They use `MessageSavegameRestored` scripts to restart ambient effects after a load.

### Timers

`ActionStartTimer` starts a timer on the Script that runs the action. `Seconds` sets the delay.
When the timer expires, that Script sends `MessageTimerExpired` under its own Label.
The receiver can be the same Script or another Script.
Set the receiver's `scriptMessageClass` to `MessageTimerExpired`. Set its `TriggeredBy` to the timer owner's Label.
`ActionStopTimer` cancels timers on Script actors with the specified `scriptLabel`.

Retail maps use timers, but they are rare. `HammerLock` and `HammerUnLock` use separate scripts.
`PlasmidJuggler` receives its own timer message. See
[21-Scripting-Logic-and-Variables.md](21-Scripting-Logic-and-Variables.md).

### Another script

A script starts another script in two ways:

- `ActionNonBlockingExecuteScript` with `targetScript` = the other Script's Label starts it and
  continues at once. `ActionBlockingExecuteScript` starts it and waits until it finishes. The other
  Script needs no `TriggeredBy` and no `scriptMessageClass`. It must be enabled. A Script that is
  already running refuses a second start.
- `ActionSendTriggerMessage` sends `MessageTrigger` under the running Script's Label. Every Script
  and ScriptableMover with that Label in `TriggeredBy` receives it. Set `Instigator` to `Player` to
  mark the player as the cause; the game's own maps do so for movers.

The game's own maps start most helper scripts with `ActionNonBlockingExecuteScript`. They use
`ActionSendTriggerMessage` for movers.

### Which message class to use

| Event | `scriptMessageClass` | `TriggeredBy` |
| --- | --- | --- |
| A pawn enters or leaves a TriggerRadius | `MessageTriggerEnter`, `MessageTriggerExit` | The trigger's Label |
| A pawn enters or leaves a TriggerVolume | `MessageTriggerVolumeEnter`, `MessageTriggerVolumeExit` | The volume's Label |
| A ScriptableMover starts or stops moving | `MessageMoverOpening`, `MessageMoverOpened`, `MessageMoverClosing`, `MessageMoverClosed` | The mover's Label |
| A pawn or the player bumps a ScriptableMover | `MessageMoverBump`, `MessageMoverPlayerBump` | The mover's Label |
| The level starts | `MessageLevelStarted` | The level name, or `all` |
| A saved game is loaded | `MessageSavegameRestored` | The level name |
| A Script's timer expires | `MessageTimerExpired` | That Script's Label |
| A Script runs `ActionSendTriggerMessage` | `MessageTrigger` | That Script's Label |
| Any message from one sender | `Message` | The sender's Label |
| The player picks up an item | `MessageReceivedInventory` | `Player` |
| A pawn dies | `MessagePawnDied` | The pawn's Label, or `all` |
| The player uses a reactive object such as a button or a lever | `MessageRAReacted` | The object's Label |
| The player finishes hacking an object | `MessagePlayerFinishedHacking` | The object's Label |
| The player types a code into a keypad | `MessageDoorKeypadUsed` | The keypad's Label |

### The message classes

The `scriptMessageClass` drop-down lists every message class. The classes below are the ones a
mapper uses, grouped by package. The fields named are the ones a message filter can match.

Scripting package:

| Class | Sent by | Fields |
| --- | --- | --- |
| `MessageTrigger` | A Script's `ActionSendTriggerMessage`. Base class of the two below. | `Instigator` |
| `MessageTriggerEnter`, `MessageTriggerExit` | A TriggerRadius | `Instigator`: the Label of the pawn |
| `MessageTriggerVolumeEnter`, `MessageTriggerVolumeExit` | A TriggerVolume | `Instigator`: the Label of the pawn |
| `MessageMoverOpening`, `MessageMoverOpened`, `MessageMoverClosing`, `MessageMoverClosed` | A ScriptableMover | none |
| `MessageMoverBump`, `MessageMoverPlayerBump` | A ScriptableMover that is bumped | `bumperLabel`, `playerLabel` |
| `MessageTimerExpired` | A Script whose timer expires | none |
| `MessageWatcher` | A Script's watcher. See [21-Scripting-Logic-and-Variables.md](21-Scripting-Logic-and-Variables.md). | `watcherName` |

Engine package:

| Class | Sent by | Fields |
| --- | --- | --- |
| `MessageLevelStarted` | The level, when the map starts | none |
| `MessageSavegameRestored` | The level, when a saved game loads in the map | none |
| `MessagePressureChanged` | The level, when a pressure region changes. In the game's own maps, filters on `RegionName` and `NewPressure` are used. | `RegionName`, `NewPressure`, `OldPressure` |
| `MessageZoneEntered`, `MessageZoneExited` | A ZoneInfo, when a pawn enters or leaves the zone | none |
| `MessageAnimNotify` | An animated actor, from an animation notify | none |

VengeanceShared package:

| Class | Sent by | Fields |
| --- | --- | --- |
| `MessageRAReacted` | A reactive actor when the player uses or touches it. In the game's own maps, `Reason` = `Touch` filters it to the use action. | `RA`, `Reason` |
| `MessageReactiveActorAcquiredState` | A reactive actor when it enters a state | `actorClass`, `ActorLabel`, `StateName`, `UnAcquired` |

ShockGame package, the game objects. Every field of these classes can be filtered:

| Class | Sent by |
| --- | --- |
| `MessagePawnDied`, `MessagePawnTookDamage`, `MessagePawnAquiredState`, `MessagePawnShattered` | A pawn. Fields include `PawnLabel`, `PawnClass`, `StateName`. |
| `MessageReceivedInventory` | The player, when an item enters the inventory. Fields include `ActualClass`, `Amount`. |
| `MessagePlayerStartedHacking`, `MessagePlayerFinishedHacking` | A hackable object. |
| `MessageDoorOpen`, `MessageDoorButtonPressed`, `MessageDoorKeypadUsed` | A door, a door button, a keypad. `Keycode` holds the typed code. |
| `MessageFuseBlown`, `MessageFuseReplaced` | A fuse box. |
| `MessagePlayerUsedObject`, `MessagePlayerFinishedUsingObject`, `MessagePlayerStartedUsingMachine`, `MessagePlayerFinishedUsingMachine` | An object or a machine the player uses. |
| `MessagePlayerFiredWeapon`, `MessagePlayerUsedAbility`, `MessageTelekinesisStartedPullingActor` | The player. |
| `MessagePlayerStartedHarvesting`, `MessagePlayerFinishedHarvesting`, `MessagePlayerSavedGatherer`, `MessagePlayerPacifiedGatherer` | The player, at a Little Sister. |
| `MessageObjectPhotographed`, `MessagePlayerCraftedItem`, `MessagePlayerResurrected`, `MessageDeathPrevented`, `MessagePlayerChangedMapUIRegion` | The player. |

ShockAI package: `MessageAISpawned`, `MessageAIAttackingTarget`, `MessageAIRecognizedPlayer`,
`MessageAIWeaponFired`, `MessageSecurityAlarmStarted`, `MessageSecurityAlarmStopped`,
`MessageGathererEnteredVent`, `MessageGathererExitedVent`, `MessageGathererSaved`,
`MessageGathererScoopedUp`, `MessageGathererStunned`, `MessagePlayerHitGathererVent`,
`MessageAssassinTeleportedOut`. Every field of these can be filtered. AI setup is not covered in
this guide.

**NOTE:** The drop-down also lists the abstract base classes `Message`, `MessageMover`,
`MessageTriggerVolume` and `MessageSomethingDoneToItem`. `Message` is useful as the catch-all
class. The others accept their subclasses in the same way.

## Message filters

A message filter restricts a Script to the messages whose fields hold given values. Without a
filter, a Script with `scriptMessageClass` = `MessagePawnDied` and `TriggeredBy` = `all` runs when
any pawn dies. With a filter whose `PawnLabel` is `Cohen`, it runs only when the pawn labelled
`Cohen` dies.

To set a filter:

1. Set `scriptMessageClass` first.
2. Click the `messageFilter` row and expand it. Its child row reads `New`.
3. Click the `New` row. Pick the same class as `scriptMessageClass` in the drop-down, then click
   the **New** button.
4. The row expands into the message's fields. Fill in only the fields that must match. Leave the
   others empty.

The filter compares the exact text of every filled-in field with the incoming message. A field
that is empty, `None` or `0` is not compared. A value of `0` therefore cannot be matched. The
`messageFilter` row itself shows the object path of the filter, not a sentence; expand it to see
the fields.

Example. A TriggerVolume labelled `BrickWallTV` sends `MessageTriggerVolumeEnter` for every pawn
that enters. To run a script only for the player, one of the game's own maps uses a Script with
`scriptMessageClass` = `MessageTriggerVolumeEnter`, `TriggeredBy` = `BrickWallTV` and a
`MessageTriggerVolumeEnter` filter with `Instigator` = `Player`. The more common way in the game's
own maps is to set `TriggerOnlyByLabel` to `Player` on the volume itself. Both work.

## Editing the Actions list

The Actions list uses the standard array controls of the Properties window. The buttons appear on
the right end of a row when you click that row.

| Row | Buttons | Result |
| --- | --- | --- |
| `Actions` | **Add** | Appends an empty entry, opens it and selects its `New` row. |
| `Actions` | **Empty** | Removes every entry. No confirmation. |
| `[i]`, an entry | **Insert** | Inserts an empty entry before this one. |
| `[i]`, an entry | **Delete** | Removes this entry. No confirmation. |
| `[i]`, an entry | **Clear** | Sets the entry to `None`. The row shows the `New` line again. The old action stays in the map file but is not run. |
| `[i]`, an entry | **...** | Does nothing for actions. |
| `New`, inside an empty entry | **New** | Creates the action of the class picked in the drop-down. |

The entry rows show the action as a sentence in the value column. The sentence updates as soon as
you change a field. Click the plus sign, or double-click the row, to expand an action into its
fields. The fields appear directly under the row, without category headers. Press Enter to commit
a field and move to the next row. Press Esc to revert.

Rows that every action has:

- `bIsGameCritical`. True means the action must still run when a level change cuts the script
  short. `ActionWait`, `ActionPlayEffect`, `ActionCinematicFadeView` and
  `ActionPrintClientMessage` default to False. See
  [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md).
- A `Bindings` section at the end, an array named `resolveInfoList`. It feeds a field from a
  variable or from a nested action. A script that uses only typed values needs no bindings. See
  [21-Scripting-Logic-and-Variables.md](21-Scripting-Logic-and-Variables.md).

Fields whose name ends in `Label`, such as `ActorLabel`, `DoorLabel`, `AILabel` and
`TargetLabel`, show a drop-down of every Label set in the map. Fields named `targetScript` list
the Labels of the Script actors. You can also type a name that is not in the list, for an actor
that a script spawns later. The list is built when you click the row.

Nested lists such as `trueActions` in `ActionIf` and `loopActions` in `ActionLoop` use the same
buttons and rows.

An entry that is `None` is skipped when the script runs. Delete empty entries before you bake.

With several actors selected, the array rows read `...` and cannot be changed. Select one Script
at a time.

**NOTE:** Edit > Copy and Edit > Paste of an actor do not carry its `Label`. The pasted Script
has its actions and its `TriggeredBy`, but no `Label`. Set the `Label` again after a paste, on
Scripts and on triggers.

## Testing a script

Scripts do not run inside the BioShock editor. Play Map starts the retail game.

### SDK logs and retail runtime logs

SDK logging works. The editor writes `System\Editor.log`. UCC writes `System\UCC.log`.
The SDK Manager writes files under `Logs\`.
These paths are relative to the SDK folder. Use these logs for editor, build, bake, compile and setup problems.
They do not record scripts that run in the retail game.

No reliable method is known to enable runtime logging in the supported retail builds.
There is no verified retail log location or set of logging options for this procedure.
The script source contains messages such as `ACCEPTED message`, `IGNORING message` and `ERROR:`.
These messages are not available as a retail debugging interface. Missing log messages do not prove that a script failed.

Custom UnrealScript classes can write diagnostic values to `.ini` files through config writes.
This requires custom code and is an experimental workaround. The SDK does not provide an automatic retail log through this method.

### Test with visible results

1. Add a temporary `ActionPrintClientMessage` at the start of the action list.
2. Set `MessageText` to a unique message, for example `LobbyScript started`.
3. Add another message after the action that you want to test.
4. Set its `MessageText` to a different message, for example `LobbyScript reached step 2`.
5. Save the map.
6. Run Build > Play Level (Ctrl+P). See [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).
7. Activate the trigger in the game.
8. Check the screen messages and the action's result, for example whether the target door opens.
9. Remove the temporary messages after the test.

First test that screen messages are visible in your map. You can also use an action with a known visible result.
For example, use a temporary action that opens a test door.
If the first message appears, the script reached that action.
If the next message is missing, check the actions between the two messages, including waits and conditions.
A message after an action confirms that execution continued. It does not confirm that the action changed its target.

If no visible result occurs, check the possible causes below.

| Mistake | Symptom | Correction |
| --- | --- | --- |
| `TriggeredBy` is empty | The trigger does not start the Script. | Type the sender's Label. |
| Label typo | The trigger does not start the Script. | Compare the trigger's `Label` and the Script's `TriggeredBy` letter by letter. Case does not matter; spelling does. |
| Wrong message class | The trigger does not start the Script. | For a TriggerRadius use `MessageTriggerEnter`; for a TriggerVolume use `MessageTriggerVolumeEnter`. See the table above. |
| Script disabled | The Script does not run. | Set `enabled` to True. |
| Trigger not touching the player | The trigger does not start the Script. | Enlarge `CollisionRadius` and `CollisionHeight` on a TriggerRadius. Place a TriggerVolume where the player walks. Check `Disabled`. |
| Trigger fired one time only | The first walk works, later walks do not. | `TriggerOnlyOnce` on a TriggerVolume, or `MaxEnterCount` = 1 on a TriggerRadius. |
| An action names a Label that no actor has | The target does not change, or the game reports an error. | Set the Label on the target actor. Pick it from the drop-down of the field. |
| Level-start script does nothing under Play Map | The first test message does not appear. | `TriggeredBy` must be the Play Map name or `all`. See "Level start". |

## Known limits

- The BioShock editor does not validate scripts. It does not check that a Label exists, that
  `TriggeredBy` names an actor, or that a message class fits the sender. Test with Play Map.
  Check visible results as described in "Testing a script".
- Scripts do not run in the BioShock editor. There is no play-in-editor.
- The `scriptMessageClass` drop-down lists every message class, including the abstract ones. Pick
  the class from the tables in this document.
- The `messageFilter` row shows an object path, not a sentence. Expand it to see the fields.
- **Empty** and **Delete** on an array remove entries without a confirmation.
- The `...` button on an action row does nothing.

## Related documents

- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [08-Zones-and-Portals.md](08-Zones-and-Portals.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [21-Scripting-Logic-and-Variables.md](21-Scripting-Logic-and-Variables.md)
- [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)
- [23-Scripting-Examples.md](23-Scripting-Examples.md)
- [36-Troubleshooting.md](36-Troubleshooting.md)
