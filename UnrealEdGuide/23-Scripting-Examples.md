# Scripting Examples

This document shows real scripts from the game's own maps. It is the fourth of the four
scripting documents. Read it after [20-Scripting-Basics.md](20-Scripting-Basics.md),
[21-Scripting-Logic-and-Variables.md](21-Scripting-Logic-and-Variables.md) and
[22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md). Read it when you know
what a Script actor, a message and an action are. It shows how the pieces fit together in a
finished level. The first part lists the habits of the game's designers. The second part
walks through fourteen scripts, from a two-row light switch to Cohen's masterpiece.

## Before you start

- You have read [20-Scripting-Basics.md](20-Scripting-Basics.md) and
  [21-Scripting-Logic-and-Variables.md](21-Scripting-Logic-and-Variables.md).
- You can open the game's own maps. See [19-Working-with-Retail-Maps.md](19-Working-with-Retail-Maps.md).
- You know how to set a property on an actor. See [09-Actors-and-Properties.md](09-Actors-and-Properties.md).

## Where the examples come from

The examples are from four levels:

| Level | Map file | Used for |
| --- | --- | --- |
| The Lighthouse | `0-Lighthouse.bsm` | The simplest scripts and the arrival cinematic. |
| Welcome to Rapture | `1-Welcome.bsm` | The elevator, the reminder loop, the arithmetic condition. |
| Fort Frolic | `4-Recreation.bsm` | Level start, filters, the keypad, the lift template, Cohen. |
| Apollo Square | `6-Slums.bsm` | The only timer scripts in the game. |

To look at an original:

1. Open the map with File > Open... as described in
   [19-Working-with-Retail-Maps.md](19-Working-with-Retail-Maps.md).
2. Select the Script actor. A Script actor is a sprite in the viewports.
3. Open the Properties window and expand the Scripting category. `Label`, `TriggeredBy`,
   `scriptMessageClass`, `messageFilter`, `enabled` and `Actions` are there. Each row of
   `Actions` shows the action as a sentence. Expand a row to see its fields.

Where noted, this document simplifies the outlines. Long scripts keep only the rows that show
the pattern. Every property name and value in an outline is the one in the map.

## How to read the outlines

```
Script  Label=DoorScript  scriptMessageClass=MessageTriggerVolumeEnter  TriggeredBy="DoorTV"
  [0] ActionPlayEffect   EffectTag=DoorSound  ActorLabel=DoorMark
  [1] ActionIf   testsOr: BooleanStatement lhs="global_DoorsOpened" logicOp=LOGICOP_EQUALS rhs="0"
        trueActions:
          [0] ActionWait   Seconds=2
        elseActions:
          [0] ActionExitScript   targetScript=DoorScript
```

- The first line is the Script actor and the Scripting properties it carries. A
  script with no `TriggeredBy` is a callee: only another script can start it.
- `[n] ActionClass field=value` is row `n` of `Actions`. A field that the outline does not
  show keeps its class default. `ActionWait` waits 1 second, `ActionSendTriggerMessage` sends
  the script's own `Label`, `ActionHideOrShowActor` hides, a `BooleanStatement` tests
  `LOGICOP_EQUALS`.
- `testsOr:`, `trueActions:`, `elseActions:` are the arrays of an `ActionIf`;
  `loopActions:` is the array of an `ActionLoop`.
- `field <- ActionClass ...` means a nested action feeds the field. In the BioShock
  editor this is an entry in the action's Bindings list (`resolveInfoList`): `Action` set to
  the nested action, `PropertyName` set to the field. You add these entries by hand.
- A variable name typed into a string field, such as `lhs="global_DoorsOpened"`, appears as
  typed. The BioShock editor records the binding for it by itself.
- Enum values appear by name (`LT_Flicker`, `PL_High`, `LOGICOP_GREATEREQUAL`), as in the
  drop-downs.
- `-- text` is a comment. `...` marks rows that are left out.

## How the game's designers script

### Numbers

| | The Lighthouse | Welcome to Rapture | Fort Frolic |
| --- | --- | --- | --- |
| Script actors | 55 | 321 | 439 |
| TriggerVolumes | 32 | 73 | 82 |
| ScriptableMovers | 1 | 16 | 19 |
| Actions per script, median | 2 | 3 | 3 |
| Actions per script, largest | 49 | 101 | 86 |
| Scripts with no `TriggeredBy` (callees) | 9 | 81 | 108 |
| Scripts with `enabled=False` | 11 | 43 | 43 |

Most scripts are short. A level is built from many small scripts, not from a few large
ones. The large scripts are cinematics. The designers left retired versions in the map with
`enabled=False`. A disabled script ignores messages and refuses execution by other
scripts.

### Labels

- Labels are free text without spaces. They are case-insensitive: `player` and `Player`,
  `4-Recreation` and `4-recreation` are the same label.
- Suffixes tell you what an actor is. `...TV` marks a TriggerVolume (the designers also use
  it on TriggerRadius actors, such as `ExitWaterTV`). `...Trigger` marks other triggers and
  `...Script` a Script. `...Mark`, `...Marker` or `...Audio` marks a Marker that effects play
  on. `...BV` or `...Blocker` marks a BlockingVolume, and `...Spawner` an AI spawner.
- The same Label on several actors is normal and deliberate. Three lights share
  `StepLightsB`; two volumes share `StepLightsTV`; four picture frames share `frame`. An
  action determines whether it uses all matches or only the first match.
  Execute Script actions, `ActionClearContainer`, `ActionChangeSkinAtIndex` and `ActionChangeStaticMesh` use only the first match.
  Other actions also use only the first match. Check [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md) for each action.
  Use a unique Label when an action must reach one specific actor.
- The player pawn's Label is `Player`.
- The level's own Label is the map file name in lower case, for example `0-lighthouse`.

**NOTE:** Edit > Copy and Edit > Paste do not carry an actor's `Label`. Set it again after
you paste.

### How scripts start

| Pattern | `TriggeredBy` | `scriptMessageClass` | Count (Lighthouse / Welcome / Fort Frolic) |
| --- | --- | --- | --- |
| Level start | `"<map name>"` | `MessageLevelStarted` | 7 / 22 / 17 |
| After a save is loaded | `"<map name>"` | `MessageSavegameRestored` | 1 / 2 / 4 |
| Player enters a volume | `"<TV label>"` | `MessageTriggerVolumeEnter` | 31 / 94 / 49 |
| Player enters a radius | `"<TR label>"` | `MessageTriggerEnter` | 5 / 12 / 33 |
| Any message from a trigger or a script | `"<label>"` | `Message` | 1 / 35 / 74 |
| A reactive actor is used | `"<RA label>"` | `MessageRAReacted` | 1 / 6 / 41 |
| A pawn dies | `"<pawn label>"` or `"all"` | `MessagePawnDied` | 0 / 21 / 33 |
| Player picks something up | `"player"` | `MessageReceivedInventory` | 0 / 7 / 6 |
| A mover finishes or starts moving | `"<mover label>"` | `MessageMoverOpened`, `MessageMoverClosing` | 0 / 5 / 4 |
| Called by another script | empty | usually none | 9 / 81 / 108 |

The level-start pattern is the setup script of a level: `TriggeredBy` is the map name and
`scriptMessageClass` is `MessageLevelStarted`. The game sends that message on every load that
is not a checkpoint restore. `MessageSavegameRestored` scripts restart ambient state after a
load; the game's maps name them `..._Resume`.

`scriptMessageClass=Message`, the base class, accepts every message from the listed labels.
The designers use it for look-at triggers and for "any trigger message from that script".

The game's own maps use `TriggeredBy="all"` with messages whose sender you do not know
in advance: `MessagePawnDied`, `MessageAISpawned`, `MessagePawnTookDamage`,
`MessagePlayerResurrected`. A `messageFilter` then narrows the message.

`TriggeredBy` can list several labels: `"ButtonLift02, CallButtonTop02, CallButtonBottom02"`.

### Callee scripts

A script with an empty `TriggeredBy` never registers for a message. It runs only when another
script names it in `ActionNonBlockingExecuteScript` or `ActionBlockingExecuteScript`
(`targetScript=<Label>`). The non-blocking form starts the callee and continues. The blocking
form runs the callee's actions in place and continues after they finish. A callee that is
still running refuses a second start. The game queues a message that arrives at a running
script.

### Movers

The game never opens a `ScriptableMover` with a door action. The mover lists the Labels of
the scripts that may move it in its own `TriggeredBy`, and the script sends
`ActionSendTriggerMessage`. Every ScriptableMover in the three maps has
`InitialState=TriggerToggle` (Fort Frolic also uses `TriggerOpenTimed`). In the other
direction a mover sends `MessageMoverOpening`, `MessageMoverOpened`, `MessageMoverClosing`
and `MessageMoverClosed` under its own Label. See examples 8, 10 and 12.

### The self-disable idiom

There is no "trigger once" property on a Script. A script that must run once ends with:

```
ActionSetProperty   Object=<its own Label>  Property=enabled  NewValue="False"
```

More than forty Fort Frolic scripts end this way. The same row switches off a trigger: use
`Property=Disabled` on a TriggerVolume or TriggerRadius, or `Property=enabled` on a look-at
trigger or a Script.

### Variables

- No variable exists until a script creates it. `ActionVariableAssignIfNotExist` creates a
  variable with a start value and leaves it alone if it exists. `ActionVariableAssign` sets
  it. `ActionVariableIncrement` and `ActionVariableDecrement` count.
- A name that starts with `Global_` (any case) belongs to every script and survives level
  changes and saves. The game's maps use `global_` for almost every variable. A name without
  the prefix belongs to the script that made it; the designers use these only in templates
  and tunables blocks (`MoverKeyframe`, `Wave1KillQuota`, `LastHUDRegionDisplayed`).
- Values are text: `"True"`, `"False"`, `"0"`, `"39"`. A test on a number uses the ordered
  operators (`LOGICOP_GREATEREQUAL`, `LOGICOP_LESSEQUAL`); a test on text uses equal and
  not-equal. A switch on a variable is a run of `ActionIf` rows, one per value.

### Notes

`ActionScriptNote` does nothing at run time. Its `Note` text is the row's sentence. Fort
Frolic has sixty of them. The designers use them for usage instructions on templates
(`>> USAGE: Change the TriggeredBy field ...`). They also use them for section dividers
(`------------------`) and to mark a tuning value (`Following line tunes the amount of time
...`).

### The most used actions

Counts are for the three main example maps together, nested actions included.

| Action | Uses | What it is for |
| --- | --- | --- |
| `ActionWait` | 834 | Pacing. Every sequence is a chain of actions and waits. |
| `BooleanStatement` | 808 | The test inside an `ActionIf`. |
| `ActionIf` | 696 | Branch on a variable, a message value or a label. |
| `ActionSetProperty` | 660 | Write any property by name: `Disabled`, `enabled`, `bShowHudElements`, `MoveTime`, `Skins`. |
| `ActionPlayEffect` | 566 | Play a sound, particle, screen or music effect on a labelled actor. |
| `ActionNonBlockingExecuteScript` | 368 | Start a helper script and continue. |
| `ActionSetLightProperties` | 363 | Change a light's brightness, type and colour. |
| `ActionVariableAssign` | 308 | Set a variable. |
| `ActionVariableAssignIfNotExist` | 219 | Create a variable with a default. |
| `ActionStopEffect` | 218 | Stop an effect started with `ActionPlayEffect`. |
| `ActionHideOrShowActor` | 202 | Hide or show a labelled actor. |
| `ActionPlayAnimation` | 167 | Play an animation on a labelled mesh or AI. |
| `ActionPostMovementGoal` | 134 | Send an AI to a labelled destination. |
| `ActionSpawnAI` | 127 | Spawn an AI at a labelled spawner and give it a Label. |

The designers almost never use timers, watchers and `ActionFor`. They write polling as an
`ActionLoop` with an `ActionWait` inside.

## Example 1: A trigger turns on the stair lights

Where: the Lighthouse. The player walks up to the stairs. The lights flicker on with a sound.

```
Script  Label=StepLightsScript  scriptMessageClass=MessageTriggerVolumeEnter  TriggeredBy="StepLightsTV1"
  [0] ActionSetProperty   Object=StepLightsTV  Property=Disabled  NewValue="true"
  [1] ActionPlayEffect   EffectTag=LightsOnSound  ActorLabel=StepLightsMark  bIsGameCritical=True
  [2] ActionSetLightProperties   Object=StepLightsB  LightBrightness=(LightBrightness=1,ChangeProperty=True)  LightType=(LightType=LT_Flicker,ChangeProperty=True)
  [3] ActionWait   Seconds=0.75
  [4] ActionSetLightProperties   Object=StepLightsB  LightType=(LightType=LT_Steady,ChangeProperty=True)
```

Simplified: the original has row 0 twice. One row is enough, because `ActionSetProperty`
acts on every actor with the label.

The wiring:

- One TriggerVolume with `Label=StepLightsTV1` and `TriggerOnlyOnce=True`. It sends the
  message.
- Two more TriggerVolumes share `Label=StepLightsTV`. Row 0 switches both off by writing
  their `Disabled` property.
- Three Light actors share `Label=StepLightsB`. Rows 2 and 4 change all three.
- One Marker with `Label=StepLightsMark`. The sound plays there.
- `StepLightsTV2` feeds a second copy of the script, with the same Label.

This is the flicker-on idiom of the game: `LT_Flicker` at brightness 1, a wait of 0.35 to
0.75 seconds, then `LT_Steady`. Each sub-struct of `ActionSetLightProperties` has its own
`ChangeProperty` flag; the game writes only the flagged ones. The level-start script
`LHLightSetup` sets these lights to `LT_None` first, so they are dark until the trigger.

## Example 2: Two triggers that arm each other

Where: the Lighthouse. The player leaves the water and re-enters it. Each crossing plays the
right screen effects and swaps which trigger is live.

```
Script  Label=ExitWaterScript  scriptMessageClass=MessageTriggerEnter  TriggeredBy="ExitWaterTV"
  [0] ActionStopEffect   EffectTag=DripsOnCamera  ActorLabel=Player
  [1] ActionPlayEffect   EffectTag=StopCam  ActorLabel=Player  bIsGameCritical=True
  [2] ActionStopEffect   EffectTag=inocean  ActorLabel=Player
  [3] ActionStopEffect   EffectTag=choking  ActorLabel=Player
  [4] ActionPlayEffect   EffectTag=slow  ActorLabel=Player
  [5] ActionSetProperty   Object=ReEnterWaterTV  Property=Disabled  NewValue="false"
  [6] ActionSetProperty   Object=ExitWaterTV  Property=Disabled  NewValue="true"

Script  Label=ReEnterWaterScript  scriptMessageClass=MessageTriggerEnter  TriggeredBy="ReEnterWaterTV"
  [0] ActionPlayEffect   EffectTag=DripsOnCamera  ActorLabel=Player
  [1] ActionPlayEffect   EffectTag=Floating  ActorLabel=Player  bIsGameCritical=True
  [2] ActionPlayEffect   EffectTag=inocean  ActorLabel=Player
  [3] ActionStopEffect   EffectTag=slow  ActorLabel=Player
  [4] ActionSetProperty   Object=ReEnterWaterTV  Property=Disabled  NewValue="true"
  [5] ActionSetProperty   Object=ExitWaterTV  Property=Disabled  NewValue="false"
```

The wiring:

- Two TriggerRadius actors share `Label=ExitWaterTV` and two share `Label=ReEnterWaterTV`
  (`CollisionRadius` 350 and 500, `CollisionHeight` 200). A TriggerRadius sends
  `MessageTriggerEnter`, not `MessageTriggerVolumeEnter`.
- The re-enter pair starts with `Disabled=True`. Each script switches the other pair on and
  its own pair off.

This is a re-armable two-state trigger without a loop or a variable. Screen effects always
target `ActorLabel=Player`.

## Example 3: The level-start setup script

Where: Fort Frolic. Runs every time the level loads.

```
Script  Label=SetPressureScript  scriptMessageClass=MessageLevelStarted  TriggeredBy="4-recreation"
  [0] ActionVariableAssignIfNotExist   lhs=global_RecStartedFirstTime  rhs="True"
  [1] ActionVariableAssignIfNotExist   lhs=global_RecPlayerIsSlow  rhs="False"
  [2] ActionIf   testsOr: BooleanStatement lhs="global_RecStartedFirstTime" logicOp=LOGICOP_EQUALS rhs="True"
        trueActions:
          [0] ActionVariableAssign   lhs=global_BathysphereUnlocked  rhs="0"
          [1] ActionVariableAssign   lhs=global_TheaterQuest  rhs="0"
          [2] ActionVariableAssign   lhs=global_FrameQuest  rhs="0"
          ...   -- 16 more flags set to "0", then 5 more ActionVariableAssignIfNotExist rows
          [24] ActionChangeCollision   Target=BasementGrateBlocker  BlockNonZeroExtentTraces=SetToTrue
          [25] ActionSetSpawnerRepopulationState   SpawnerLabel=HallCrawlerSpawnerB1a
          [28] ActionNonBlockingExecuteScript   targetScript=AtriumLightsOffScript
          [29] ActionChangePressure   RegionName=RecPressure  DesiredPressure=PL_Low
          [31] ActionHideOrShowActor   ActorLabel=MasterpieceCurtain  HideActor=False
          [32] ActionFreezeHavokActor   Target=FrozenTrash
          ...   -- 8 more frozen props
  [3] ActionIf   testsOr: BooleanStatement lhs="global_PlayerHasCohenKey" logicOp=LOGICOP_EQUALS rhs="True"
        trueActions:
          [0] ActionPlayEffect   EffectTag=QuestLightOn  ActorLabel=CohenLock
  [4] ActionPlayEffect   EffectTag=RecDust  ActorLabel=PlayerHead
  [5] ActionWait   Seconds=5
  [6] ActionOpenDoor   DoorLabel=Level_EntryDoors  StayOpen=True
```

The wiring: `TriggeredBy` is the map name. The script needs nothing else. The level sends
`MessageLevelStarted` under its own Label.

How to build it:

1. Give every global flag a default with `ActionVariableAssignIfNotExist`.
2. Test a "first time" flag with an `ActionIf`. Type the variable name into `lhs` and the
   literal into `rhs`. The BioShock editor records the binding for `lhs` by itself.
3. Inside the branch, set the rest of the flags with `ActionVariableAssign`. Then put the
   level into its start state: collision, spawners, pressure, hidden props, frozen props.
4. Another script flips the flag. In Fort Frolic, `FirstTVScript` sets
   `global_RecStartedFirstTime` to `"False"` when the player passes the first volume. On the
   next visit the script skips the branch and the level keeps its state.

The Fort Frolic script `OutburstGlobals` uses the same shape as a tunables block: a list of
script-local values (`Wave1KillQuota`, `betweenSpawnDelay`) followed by the self-disable row.

## Example 4: A message filter

Where: Welcome to Rapture. The player approaches the bathroom stall for the first time.

```
Script  Label=ApproachedStall  scriptMessageClass=MessageTriggerVolumeEnter  TriggeredBy="BrickWallTV"
        messageFilter=MessageTriggerVolumeEnter  Instigator=Player
  [0] ActionDisableOrEnableConcept   ConceptName=UseCredits  Enable=True
  [1] actionSetQuestHint
  [2] ActionSetProperty   Object=Restaurant_ReminderPrompt  Property=enabled  NewValue="False"
  [3] ActionVariableAssign   lhs=global_SeenStall  rhs="True"
```

The wiring: the TriggerVolume `BrickWallTV` sends a message for every pawn that enters
it. The filter accepts only the message whose `Instigator` field is `Player`.

How to build it:

1. Set `scriptMessageClass` first. The `messageFilter` row needs the class.
2. Click `New` on `messageFilter` and pick the same class.
3. Fill in only the fields you want to match. A filter compares the exact text of every
   field that is not empty, `None` or `0`. Empty fields match anything.

The game's maps use a filter in these forms:

| Message | Filter fields | Example |
| --- | --- | --- |
| `MessageTriggerVolumeEnter` | `Instigator=Player` | This example. |
| `MessageRAReacted` | `Reason=Touch` | The picture frames of Cohen's quest accept "use", not "look". |
| `MessagePawnDied` | `PawnLabel=Friend` or `PawnClass=SpawnedMeleeThug` | Saved values in Examples 9 and 14. The message wiring in Example 9 is unverified. |
| `MessageReceivedInventory` | `ActualClass=LiquorItems` | `BloodAlcoholLevel`, with `TriggeredBy="player"`: the player drank. |

**NOTE:** The usual way to restrict a volume to the player is on the volume itself:
`TriggerOnlyByLabel(0)=Player`. Most TriggerVolumes in Welcome to Rapture and Fort Frolic
have it. Use a `messageFilter` when the message carries a field the volume cannot test.

## Example 5: A hacked safe springs an ambush, once

Where: Fort Frolic, Cohen's wall safe.

```
Script  Label=CohensSafeScript  scriptMessageClass=MessagePlayerFinishedHacking  TriggeredBy="CohensSafe"
  [0] ActionSpawnAI   AITypeToSpawn=SpawnedAssassin  SpawnLocationLabel=CohenSafeLeftSpawner  SpawnedAILabel=CohenSafeLeftAI  SpawnedAIPatrol=CohenPatrol  OverriddenAIArchetypeNames(0)=RecreationBabyJaneTeleportBIRD
  [1] ActionMuteAI   AILabel=CohenSafeLeftAI  bShouldMuteAI=True
  [2] ActionPlayEffect   EffectTag=4_Laugh  ActorLabel=LaughMarker
  [3] ActionAttackTarget   AILabel=CohenSafeLeftAI  TargetLabel=Player
  [4] ActionSetProperty   Object=CohensSafeScript  Property=enabled  NewValue="False"
```

The wiring: the wall safe carries `Label=CohensSafe` and sends
`MessagePlayerFinishedHacking` when the hack ends. A spawner Marker carries
`Label=CohenSafeLeftSpawner`. A Marker `LaughMarker` plays the laugh.

Two habits to copy:

- The spawned AI has no Label until `ActionSpawnAI` gives it one in `SpawnedAILabel`. Every
  later row addresses the AI by that label.
- The last row is the self-disable idiom. The script runs once.

## Example 6: A reminder loop

Where: Welcome to Rapture. Every two minutes the game reminds the player to go to the
Medical Pavilion, until the player does.

```
Script  Label=Reminder_ExitPromptGoMedical            -- callee: another script starts it
  [0] ActionLoop
        loopActions:
          [0] ActionWait   Seconds=120
          [1] ActionIf   testsOr: BooleanStatement lhs="global_TimeToGoToMedical" logicOp=LOGICOP_NOTEQUAL rhs="True"
                trueActions:
                  [0] ActionShowTrainingMessage   MessageName=WelcomeGoToMedical
          [2] ActionIf   testsOr: BooleanStatement lhs="global_TimeToGoToMedical" logicOp=LOGICOP_EQUALS rhs="True"
                trueActions:
                  [0] ActionExitLoop
```

The wiring: the script has no `TriggeredBy`. A level-start script or a trigger script
starts it with `ActionNonBlockingExecuteScript targetScript=Reminder_ExitPromptGoMedical`.
Another script sets `global_TimeToGoToMedical` to `"True"` when the player arrives.

How to build it: a loop body is the `loopActions` array. The only way out is `ActionExitLoop`
or `ActionExitScript`, so always put a wait in the body. Set `bIsGameCritical=False` on the
`ActionLoop`, as the Fort Frolic loops `VandalarmLoopScript` and `DrunkManager` do. A level
change tries to finish the critical actions of a running script at once, and a loop cannot
finish. `VandalarmLoopScript` has the same shape with a two-second wait and a counter
(`ActionVariableIncrement Target=global_RecVandalarmCount`). Its `OrStatement` leaves the
loop when the count reaches 3 or `global_RecVandalarmActive` becomes `"False"`.

## Example 7: A timer

Where: Apollo Square. After the player takes a hammering, the game gives five minutes of
safety.

```
Script  Label=HammerLock            -- callee
  [0] ActionVariableAssign   lhs=global_HammerLock  rhs="1"
  [1] ActionStopTimer   scriptLabel=HammerLock
  [2] ActionScriptNote   Note="Following line tunes the amount of time the player is safe from being hammered."
  [3] ActionStartTimer   Seconds=300

Script  Label=HammerUnLock  scriptMessageClass=MessageTimerExpired  TriggeredBy="HammerLock"
  [0] ActionVariableAssign   lhs=global_HammerLock  rhs="0"
```

This example uses two scripts. `ActionStartTimer` starts the timer of the Script that runs the action.
When the timer expires, that Script sends `MessageTimerExpired` under its own Label.
`HammerUnLock` receives it because its `TriggeredBy` names `HammerLock` and its message class is `MessageTimerExpired`.
`ActionStopTimer scriptLabel=HammerLock` cancels the existing timer before `HammerLock` starts a new one.
A second call to `HammerLock` restarts the five minutes.

A timer does not require a second Script. The same map has `PlasmidJuggler`, whose `TriggeredBy` is its own Label.
Its message class is `MessageTimerExpired`. It starts a 40-second timer and receives its own expiry message.
Each run starts the timer again. It is the only repeating timer in the
game. The sixteen maps together use timers four times; the designers prefer loops.

## Example 8: The arrival at the Lighthouse

Where: the Lighthouse. The plane crash and the swim to the surface. Shortened from 34 rows.

```
Script  Label=UnderWaterAnimation  scriptMessageClass=MessageLevelStarted  TriggeredBy="0-Lighthouse"
  [0] ActionEnableOrDisableLevelSaving   DisableLevelSaving=True
  [1] ActionSetOrUnsetInputContext   Context=NullInput
  [3] ActionPlayMovie   MovieName=PlaneMovie
  [4] ActionChangePawnPhysics   Target=Player  DisablePhysics=True
  [5] ActionCinematicFadeView   fadeAlphaStart=1  Duration=1  bIsGameCritical=True
  [6] ActionNonBlockingExecuteScript   targetScript=ViewFadeScript
  [9] ActionPlayEffect   EffectTag=UnderWaterHDR  ActorLabel=Player  bIsGameCritical=True
  [10] ActionNonBlockingExecuteScript   targetScript=RisingEventsScript
  [11] ActionNonBlockingExecuteScript   targetScript=AirPlaneAnim
  [17] ActionPlayEffect   EffectTag=LightHouseRise  ActorLabel=Player  bIsGameCritical=True
  [18] ActionSendTriggerMessage            -- starts the FallingDebris mover
  [19] ActionWait   Seconds=10
  [21] ActionWait   Seconds=19
  [22] ActionChangePressure   RegionName=AboveWater  DesiredPressure=PL_High
  [23] ActionPlayEffect   EffectTag=WaterOnCamera  ActorLabel=Player  bIsGameCritical=True
  [25] ActionStopEffect   EffectTag=UnderWaterHDR  ActorLabel=Player
  [29] ActionSetOrUnsetInputContext   Context=NullInput  Unset=True
  [30] ActionChangePawnPhysics   Target=Player
  [32] ActionNonBlockingExecuteScript   targetScript=SwimmingScript
  [33] ActionEnableOrDisableLevelSaving
```

The wiring:

- `ViewFadeScript`, `RisingEventsScript`, `AirPlaneAnim` and `SwimmingScript` have no
  `TriggeredBy`. They run in parallel with this script, each on its own.
- One ScriptableMover, `Label=FallingDebris`, has `TriggeredBy="UnderWaterAnimation"`,
  `InitialState=TriggerToggle`, `MoveTime=30`, `bTriggerOnceOnly=True`, `bUseTriggered=True`
  and `bHidden=True`. Row 18 moves it. `Instigator` is not set, so the message goes out
  under the script's own Label, which is what the mover listens for.

The game brackets every large set-piece the same way. `ActionEnableOrDisableLevelSaving
DisableLevelSaving=True` opens it and `ActionEnableOrDisableLevelSaving` closes it.
`ActionSetOrUnsetInputContext Context=NullInput` takes the controls, and the same action
with `Unset=True` gives them back. Rows that must still happen if the player leaves the
level early carry `bIsGameCritical=True`.

## Example 9: A quest step with facts and a shared subroutine

Where: Fort Frolic. One of Cohen's four "friends" dies.

**CAUTION:** This outline preserves the saved retail values. Its death-message wiring is unverified.
Do not copy it as a complete working example. See the explanation below the outline.

```
Script  Label=Friend1Killed  scriptMessageClass=MessagePawnDied  TriggeredBy="Friend1"
        messageFilter=MessagePawnDied  PawnLabel=Friend
  [0] ActionVariableAssign   lhs=global_RecCobbAttackActive  rhs="False"
  [1] ActionReplaceQuest   ReplacementQuestName=KillCobbUpdateA  QuestName=KillCobb  ShowHUDFeedBack=True  CopyObjectivesCompleted=True
  [2] ActionChangeQuestArrowActor   ArrowActor=global_RecFriend1Name  ArrowActorLevelLabel=4-recreation  QuestName=KillCobbUpdateA
  [3] ActionVariableAssign   lhs=global_Friend1Killed  rhs="1"
  [4] ActionStopEffect   EffectTag=MolotovInHand  ActorLabel=Friend
  [5] ActionIf   testsOr: BooleanStatement lhs="global_Friend1Clue" logicOp=LOGICOP_EQUALS rhs="given"
        trueActions:
          [0] ActionCompleteQuest   QuestName=CobbHint
  [6] ActionBlockingExecuteScript   targetScript=AnyFriendKilled
  [7] ActionWait   Seconds=2.5
  [8] ActionGiveItemsToPlayer   ItemClass=Rec_Co_DoneA
  [9] ActionSendTriggerMessage   Instigator=Player
  [10] ActionIf   testsOr: BooleanStatement lhs="global_Friend1Photographed" logicOp=LOGICOP_EQUALS rhs="0"
        trueActions:
          [0] ActionNonBlockingExecuteScript   targetScript=Friend1DisableQuestGlow

Script  Label=AnyFriendKilled            -- callee, shared by Friend1Killed to Friend4Killed
  [0] ActionVariableIncrement   Target=global_NumFriendsKilled
  [1] ActionIf   testsOr: BooleanStatement lhs="global_NumFriendsKilled" logicOp=LOGICOP_EQUALS rhs="4"
        trueActions:
          [0] ActionRetractFact   Slot_1=TimeToFindCronies
        elseActions:
          [0] ActionAssertFact   Slot_1=TimeToFindCronies
  [2] ActionAssertFact   Slot_1=TimeToPhotoCronies
```

The retail map stores `TriggeredBy="Friend1"` and `messageFilter.PawnLabel=Friend` on `Friend1Killed` (`Script_47`).
The local `MessagePawnDied` constructor copies `ThePawn.Label` directly into `PawnLabel`.
For a pawn with `Label=Friend1`, this produces `PawnLabel=Friend1`, not `Friend`.
The saved filter value does not establish that the incoming message contains `Friend`.
How the original level reaches this action list has not been verified.

For a custom map, use consistent names. Set the pawn's `Label` to `Friend1`.
Set the receiving script's `TriggeredBy` to `Friend1`. Set its `scriptMessageClass` to `MessagePawnDied`.
Set the filter's `PawnLabel` to `Friend1`, or leave `messageFilter` empty if no additional filter is needed.
Test the death event in the game as described in [Testing a script](20-Scripting-Basics.md#testing-a-script).

Four scripts, `Friend1Killed` to `Friend4Killed`, share `AnyFriendKilled`.

What the actions do when this list runs:

- Row 6 calls the tally script blocking: `Friend1Killed` waits until `AnyFriendKilled` has
  counted. Use the blocking form when the caller needs the result.
- Row 2 points the HUD arrow at a label held in a variable. Row 5 compares a variable with
  a word, `"given"`; variables hold text, so this is a plain equality test.
- `ActionAssertFact` and `ActionRetractFact` set and clear a fact that the hint system reads.
- Row 8 gives the player a quest-log item. Row 9 sends a trigger message for any mover that
  lists `Friend1Killed`.

## Example 10: The elevator

Where: Welcome to Rapture. The lift after the player replaces the fuse. This is the complete mover
protocol: script to mover, mover back to script, script to door movers.

```
Script  Label=ElevatorActivated            -- callee: runs when the fuse is replaced
  [0] ActionSetLightProperties   Object=ElevatorButtonLight  LightColor=(LightColor=(B=255,G=255,R=255,A=0),ChangeProperty=True)
  [1] ActionPlayEffect   EffectTag=FuseReplacedSound  ActorLabel=Player
  [2] ActionSetProperty   Object=Moveme  Property=enabled  NewValue="true"
  [3] ActionSetProperty   Object=ButtonFloor  Property=bShowHudElements  NewValue="false"
  [4] ActionSendTriggerMessage   Instigator=Player
  [5] ActionWait   Seconds=3

Script  Label=Moveme  scriptMessageClass=MessageTriggerVolumeEnter  TriggeredBy="WrenchBlockageClearedTV"
  [0] ActionVariableAssignIfNotExist   lhs=global_WelcomeElevatorActivated  rhs="false"
  [1] ActionIf   testsOr: BooleanStatement lhs="global_WelcomeElevatorActivated" logicOp=LOGICOP_EQUALS rhs="false"
        trueActions:
          [0] ActionPlayEffect   EffectTag=ElevatorActivatedAudio  ActorLabel=Player
          [1] ActionSetProperty   Object=Lift  Property=MoveTime  NewValue="39"
          [2] ActionSendTriggerMessage   Instigator=Player
          [3] ActionChangeCollision   Target=ElevatorUpBV  CollideActors=SetToFalse  CollideWorld=SetToTrue
          [4] ActionSetProperty   Object=Button  Property=bShowHudElements  NewValue="false"
          [5] ActionVariableAssign   lhs=global_WelcomeElevatorActivated  rhs="true"

Script  Label=ElevatorDoorOpen  scriptMessageClass=MessageMoverOpened  TriggeredBy="Lift"
  [0] ActionPlayEffect   EffectTag=ElevatorDing  ActorLabel=ElevatorUpDoor
  [1] ActionSendTriggerMessage   Instigator=Player

Script  Label=ElevatorDoorClose  scriptMessageClass=MessageMoverClosing  TriggeredBy="Lift"
  [0] ActionSendTriggerMessage   Instigator=Player
```

The wiring:

| Actor | Label | Scripting > TriggeredBy | Other properties |
| --- | --- | --- | --- |
| ScriptableMover (the car) | `Lift` | `"MoveMe, ElevatorActivated"` | `InitialState=TriggerToggle`, `MoveTime=25` |
| ScriptableMover (door leaf) | `ElevatorUpDoor` | `"ElevatorDoorOpen, ElevatorDoorClose"` | `InitialState=TriggerToggle`, `MoveTime=0.5` |
| ScriptableMover (second leaf) | `ElevatorUpDoor` | same | same |

The round trip:

1. `ElevatorActivated` or `Moveme` sends a trigger message. `Lift` lists both labels, so it
   toggles to its other keyframe.
2. `Lift` reaches the top and sends `MessageMoverOpened` under its own Label.
   `ElevatorDoorOpen` listens to `Lift` for that class, plays the ding and sends its own
   trigger message. Both door leaves list `ElevatorDoorOpen`, so both open.
3. When `Lift` starts down it sends `MessageMoverClosing`. `ElevatorDoorClose` sends its
   trigger message and the leaves toggle shut.

How to build it: on the mover, set `Label` and set `TriggeredBy` to the Labels of the
scripts that may move it. Set `InitialState=TriggerToggle` and `MoveTime`, and build the
keyframes as for any mover. In the script, use `ActionSendTriggerMessage`. Do not use
`ActionOpenDoor` on a mover; that action is for door actors such as `ElevatorDoor` and
`HighRentDoor`. `ActionSetProperty` can write `MoveTime` at run time: row 1 of `Moveme` sets
it to 39 for the long first ride. `ActionGetProperty` can read it (example 12).

## Example 11: A keypad door

Where: Fort Frolic, the plasmid store. The code is 7774.

```
Script  Label=PlasmidStoreKeycode  scriptMessageClass=MessageDoorKeypadUsed  TriggeredBy="PlasmidStoreKeypad"
  [0] ActionDoorKeypadUsed   DoorKeypadControlLabel=PlasmidStoreKeypad
        Success <- BooleanStatement  lhs <- ActionGetMessageValue Property=Keycode  logicOp=LOGICOP_EQUALS  rhs="7774"
  [1] ActionIf   testsOr: BooleanStatement  lhs <- ActionGetMessageValue Property=Keycode  logicOp=LOGICOP_EQUALS  rhs="7774"
        trueActions:
          [0] ActionUnlockDoor   DoorLabel=PlasmidStoreDoor
          [1] ActionOpenDoor   DoorLabel=PlasmidStoreDoor
```

The wiring: the keypad actor carries `Label=PlasmidStoreKeypad` and sends
`MessageDoorKeypadUsed` with the typed code in its `Keycode` field. The door is a
`HighRentDoor` with `Label=PlasmidStoreDoor` and `bLocked=True`.

How to build it. This example needs the Bindings list, twice:

1. Add `ActionDoorKeypadUsed`. It tells the keypad whether the code was right. In its
   Bindings list, Add an entry, set `PropertyName` to `Success`, expand `Action`, pick
   `BooleanStatement`, click `New`.
2. In that `BooleanStatement`, set `rhs` to `7774`. In its own Bindings list, Add an entry,
   set `PropertyName` to `lhs`, expand `Action`, pick `ActionGetMessageValue`, click `New`,
   and set its `Property` to `Keycode`.
3. Add the `ActionIf`. Build the same `BooleanStatement` in `testsOr`, with its own nested
   `ActionGetMessageValue`. Put `ActionUnlockDoor` and `ActionOpenDoor` in `trueActions`.

`ActionGetMessageValue` reads a field of the message that started the script. It works only
in the script that received the message, not in a callee.

## Example 12: The reusable lift template

Where: Fort Frolic. A lift with a call button at each landing and a button in the car. The
script tracks where the car is. Shortened: the `elseActions` branch mirrors the `trueActions`
branch.

```
Script  Label=Moveme  scriptMessageClass=MessageRAReacted  TriggeredBy="ButtonLift02, CallButtonTop02, CallButtonBottom02"
  [0] ActionScriptNote   Note=">> USAGE: Change the TriggeredBy field to use the 3 labels of your lift's buttons."
  [2] ActionVariableAssignIfNotExist   lhs=MoverKeyframe  rhs="0"
  [3] ActionScriptNote   Note=">> Top is 0, Bottom is 1"
  [5] ActionIf   testsOr: BooleanStatement lhs="MoverKeyframe" logicOp=LOGICOP_EQUALS rhs="0"
        trueActions:
          [0] ActionIf   testsOr: OrStatement
                  lhs <- BooleanStatement  lhs <- ActionGetMessageValue Property=RA  logicOp=LOGICOP_EQUALS  rhs="CallButtonBottom02"
                  rhs <- BooleanStatement  lhs <- ActionGetMessageValue Property=RA  logicOp=LOGICOP_EQUALS  rhs="ButtonLift02"
                trueActions:
                  [0] ActionSetProperty   Object=ButtonLift02  Property=bShowHudElements  NewValue="False"
                  [1] ActionVariableAssign   lhs=MoverKeyframe  rhs="1"
                  [2] ActionCloseDoor   DoorLabel=LiftDoorUpper02  ForceClose=True
                  [3] ActionLockDoor   DoorLabel=LiftDoorUpper02
                  [4] ActionWait
                  [5] ActionSendTriggerMessage   Instigator=Player
                  [6] ActionWait   Seconds <- ActionGetProperty Object=Lift Property=MoveTime
                  [7] ActionWait
                  [8] ActionUnlockDoor   DoorLabel=LiftDoorLower02
                  [9] ActionOpenDoor   DoorLabel=LiftDoorLower02  StayOpen=True
                  [10] ActionWait
                  [11] ActionSetProperty   Object=ButtonLift02  Property=bShowHudElements  NewValue="True"
          [1] ActionScriptNote   Note=">> Then, if the button used by the player wasn't the bottom button (lift is already there), move it up"
        elseActions:
          [0] ActionIf   -- the mirror image: CallButtonTop02 / ButtonLift02, MoverKeyframe set to "0", lower door closed, upper door opened
          [1] ActionScriptNote   Note=">> Else, the lift is at the bottom so move the lift up if the button used wasn't the bottom button"
```

The wiring:

- Three reactive buttons carry the three Labels in `TriggeredBy`. Each sends
  `MessageRAReacted` with its own Label in the message's `RA` field.
- Two `ElevatorDoor` actors: `LiftDoorUpper02` and `LiftDoorLower02`, the lower one with
  `bLocked=True`.
- A ScriptableMover `Lift` with `MoveTime=16`.

`MoverKeyframe` has no `Global_` prefix. It belongs to this script, which is right for a
template that you copy several times: each copy keeps its own position. The nested
`OrStatement` asks "which button did the player press" by reading the message's `RA` field
once per button. Row 6 waits exactly as long as the ride takes, by reading the mover's `MoveTime`
into `Seconds`. The door order is: close and lock the door you leave, send the trigger
message, wait, then unlock and open the far door with `StayOpen=True`.

How to build it. This example needs the Bindings list at three levels:

1. In `testsOr` of the inner `ActionIf`, add an `OrStatement`. In its Bindings list, add two
   entries: `PropertyName=lhs` and `PropertyName=rhs`, each with a `BooleanStatement` as the
   nested `Action`.
2. In each `BooleanStatement`, type the button label into `rhs`. Add one Bindings entry with
   `PropertyName=lhs` and `ActionGetMessageValue` as the nested `Action`; set its `Property`
   to `RA`.
3. On the `ActionWait` of row 6, add one Bindings entry with `PropertyName=Seconds` and
   `ActionGetProperty` as the nested `Action`; set `Object=Lift` and `Property=MoveTime`.
4. The outer test, `lhs="MoverKeyframe"`, needs no hand-made entry. Type the name and the
   BioShock editor records it.

**NOTE:** In Fort Frolic the mover `Lift` lists `TriggeredBy="MoveMe2"`, and no script with
that Label exists in the map. This copy is the template; the elevator of example 10 is a
live one. Check the mover's `TriggeredBy` against the script's `Label` when you copy the
template.

## Example 13: Cohen's masterpiece

Where: Fort Frolic. Cohen appears on the stage of the atrium. Shortened from 82 rows.

```
Script  Label=CohenEntrance  scriptMessageClass=Message  TriggeredBy="CohenLookTrigger"
  [0] ActionSetProperty   Object=CohenEntrance  Property=enabled  NewValue="False"
  [1] ActionSetProperty   Object=CohenLookTrigger  Property=enabled  NewValue="False"
  [3] ActionVariableAssignIfNotExist   lhs=global_RecCohenDeployed  rhs="True"
  [6] ActionAssertFact   Slot_1=TimeToGoToDeck
  [10] ActionNonBlockingExecuteScript   targetScript=MoveBathyPlug
  [18] ActionPlayEffect   EffectTag=SmokePoof  ActorLabel=CohenSmoke
  [19] ActionTeleportPawnToLocation   PawnLabel=Cohen  MarkerLabel=CohenAppearsMarker
  [21] ActionSetMovableSpotlightState   SpotlightLabel=AtriumSpotlight  SpotlightOn=True
  [22] ActionSetMovableSpotlightTarget   SpotlightLabel=AtriumSpotlight  TargetActorLabel=Cohen
  [26] ActionIf   testsOr: BooleanStatement lhs="global_CohenIsDead" logicOp=LOGICOP_EQUALS rhs="0"
        trueActions:
          [0] ActionAISpeech   AILabel=Cohen  SpeechEventLabel=4_Co_Done
  [27] ActionPostMovementGoal   Target=Cohen  DestinationLabel=CohenGoalPoint  goalName="CohenGoal1"  Priority=75  DesiredFocusLabel=CohenSpeechMarker
  [28] ActionPlayEffect   EffectTag=4_AwardMusic  ActorLabel=CohenSpeechMarker
  ...   -- applause and confetti effects, with waits
  [36] ActionPlayAnimation   TargetLabel=Cohen  Animation=Cohen_Confetti  TweenTime=0.2  EndBehavior=EndBehavior_EaseOut
  [37] ActionWaitForGoal   Target=Cohen  goalName="CohenGoal1"
  [40] ActionSetLightProperties   Object=AtriumStageLight  LightBrightness=(LightBrightness=1.5,ChangeProperty=True)  LightType=(LightType=LT_Pulse,ChangeProperty=True)
  [42] ActionPlayAnimation   TargetLabel=Cohen  Animation=Cohen_MaskOff  TweenTime=0.2  EndBehavior=EndBehavior_EaseOut  bWaitForCompletion=True
  [43] ActionPostMovementGoal   Target=Cohen  DestinationLabel=CohenGoalPoint  goalName="CohenGoal2"  Priority=75  DesiredFocusLabel=frame
  [45] ActionStartAIHeadTracking   AILabel=Cohen  HeadTrackTargetLabel=frame
  [49] ActionWaitForGoal   Target=Cohen  goalName="CohenGoal2"
  ...   -- goals 3 to 7, each speech line guarded by the same test on global_CohenIsDead
  [67] ActionOpenDoor   DoorLabel=secondary_doors_into_atrium  StayOpen=True
  [71] ActionIf   testsOr: BooleanStatement lhs="global_RecFinalQuestReceived" logicOp=LOGICOP_EQUALS rhs="False"
        trueActions:
          [0] ActionVariableAssign   lhs=global_RecFinalQuestReceived  rhs="True"
          [1] ActionGiveItemsToPlayer   ItemClass=Rec_At_AtlasRun
          [2] ActionWaitForQuestLogToFinish   QuestLog=Rec_At_AtlasRun
          [3] ActionInitiateQuest   QuestName=LeaveRec
  [81] ActionPostMovementGoal   Target=Cohen  DestinationLabel=CohenGoalPoint  bShouldNeverSucceed=True  goalName="CohenGoal7"  Priority=76  DesiredFocusLabel=frame
```

The wiring: `CohenLookTrigger` is a look-at trigger. The script accepts any message
from it (`scriptMessageClass=Message`) and switches the trigger and itself off in the first
rows. Cohen is an AI with `Label=Cohen`. `CohenGoalPoint`, `CohenAppearsMarker` and
`CohenSpeechMarker` are Markers. `frame` is the label shared by the four picture frames.

The shape of a scripted AI sequence in the game:

1. Disable the trigger and the script. Set the flags.
2. Place the actor with `ActionTeleportPawnToLocation` behind a smoke effect.
3. Move with `ActionPostMovementGoal ... goalName="..." Priority=75` and wait with
   `ActionWaitForGoal`. `DesiredFocusLabel` tells the AI where to look while it walks.
4. Speak with `ActionAISpeech`. An `ActionIf` on `global_CohenIsDead` guards every line,
   so the sequence survives if the player kills Cohen early. The
   script `CohenDies` (`MessagePawnDied` from `Cohen`, filter `PawnLabel=Cohen`) starts with
   `ActionExitScript targetScript=CohenEntrance`.
5. Animate with `ActionPlayAnimation ... EndBehavior=EndBehavior_EaseOut`; set
   `bWaitForCompletion=True` where the next row must wait.
6. Hand over: give the quest item, wait for the quest log, start the next quest. A goal
   with `bShouldNeverSucceed=True` (row 81) keeps the AI at its mark.

## Example 14: An arithmetic condition

Where: Welcome to Rapture. Eight enemies have died, and the player killed half of them or
fewer with a plasmid. The game then shows the training message about switching to plasmids.

```
Script  Label=TrainingPlasmidFrequency  scriptMessageClass=MessagePawnDied  TriggeredBy="all"
        messageFilter=MessagePawnDied  PawnClass=SpawnedMeleeThug
  [0] ActionIf   testsOr: AndStatement
          lhs <- BooleanStatement  lhs="global_TallyEnemiesKilled"  logicOp=LOGICOP_EQUALS  rhs="8"
          rhs <- BooleanStatement  lhs="global_TallyEnemiesPlasmided"  logicOp=LOGICOP_LESSEQUAL
                   rhs <- ArithmeticStatement  lhs="global_TallyEnemiesKilled"  ArithmeticOp=ARITHMETICOP_DIVIDE  rhs="2"
        trueActions:
          [0] ActionShowTrainingMessage   MessageName=WeaponToPlasmid
  [1] ActionIf   -- the same test with rhs="14"
```

The wiring: `TriggeredBy="all"` with a filter on `PawnClass`. Other scripts keep the two
tallies up to date.

How to build it. This example needs the Bindings list at every level:

1. In `testsOr`, add an `AndStatement`. In its Bindings list add two entries,
   `PropertyName=lhs` and `PropertyName=rhs`, each with a `BooleanStatement` as the nested
   `Action`.
2. In the first `BooleanStatement`, type `global_TallyEnemiesKilled` into `lhs` and `8` into
   `rhs`. The BioShock editor records the variable binding by itself.
3. In the second `BooleanStatement`, type `global_TallyEnemiesPlasmided` into `lhs` and set
   `logicOp` to `LOGICOP_LESSEQUAL`. A nested action computes its `rhs`: add a Bindings entry
   with `PropertyName=rhs` and `ArithmeticStatement` as the nested `Action`.
4. In the `ArithmeticStatement`, type `global_TallyEnemiesKilled` into `lhs`, set
   `ArithmeticOp` to `ARITHMETICOP_DIVIDE`, and type `2` into `rhs`.

`ArithmeticStatement` appears in the game's maps only as an operand inside a condition;
the designers count with `ActionVariableIncrement`. You build a `NotStatement` the same way.
Fort Frolic's `Present_LookedAt` tests `NotStatement rhs <- BooleanStatement lhs <-
ActionGetLevelLabel logicOp=LOGICOP_EQUALS rhs="7-gauntlet"`. One script copied into several
maps then behaves differently in one of them.

## Patterns to copy

1. One trigger, one script. Name the volume `<Thing>TV` and the script `<Thing>Script`.
   Set `scriptMessageClass` to the message the actor sends: `MessageTriggerVolumeEnter` for
   a TriggerVolume, `MessageTriggerEnter` for a TriggerRadius, `MessageRAReacted` for a
   reactive actor, `Message` for anything.
2. Level setup is a script with `TriggeredBy="<map name>"` and `MessageLevelStarted`.
   Default every global with `ActionVariableAssignIfNotExist`.
3. A script that must run once ends with `ActionSetProperty Object=<own Label>
   Property=enabled NewValue="False"`. A trigger that must fire once has
   `TriggerOnlyOnce=True`.
4. Give the same Label to every actor that must change together.
5. Effects: `ActionPlayEffect EffectTag=<tag> ActorLabel=<label>`; screen effects on
   `Player`, world effects on a Marker. Lights: `LT_Flicker`, wait, `LT_Steady`, with
   `ChangeProperty=True` on every sub-struct you change.
6. Doors: `ActionUnlockDoor` then `ActionOpenDoor DoorLabel=... StayOpen=True`. Movers:
   `ActionSendTriggerMessage`, with the script's Label in the mover's `TriggeredBy`.
7. Helper scripts have no `TriggeredBy`. Start them with `ActionNonBlockingExecuteScript`
   for parallel work and `ActionBlockingExecuteScript` when the caller must wait.
8. Polling is `ActionLoop` with an `ActionWait` and an `ActionExitLoop`. Set
   `bIsGameCritical=False` on the loop.
9. Variables are text. A variable in a field: type its name. A nested statement or value
   action: add a Bindings entry with `PropertyName` set to the field and the nested action
   in `Action`.
10. Bracket a cinematic with level-saving off and on, and input context `NullInput` set and
    unset. Mark the rows that the game must not skip with `bIsGameCritical=True`.
11. Scripts run only in the game. Test with Play Map. Check each action's visible result.
    Use temporary screen messages to check execution. See
    [Testing a script](20-Scripting-Basics.md#testing-a-script) for the procedure and the retail logging limits.

## Related documents

- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [19-Working-with-Retail-Maps.md](19-Working-with-Retail-Maps.md)
- [20-Scripting-Basics.md](20-Scripting-Basics.md)
- [21-Scripting-Logic-and-Variables.md](21-Scripting-Logic-and-Variables.md)
- [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)
