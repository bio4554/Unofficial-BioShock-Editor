# Scripting Action Reference

This document is a reference of the actions that drive the game's systems. It covers doors and
keypads, quests, facts, training messages, machines and security, and containers and inventory.
It also covers plasmids and research, the player and the HUD, hand animation, actor animation and
damage, the environment, and the AI. Read it when you build a script and need to know which
action does a job. Each entry says what the fields mean and what the action needs in the map to
work.

The generic actions (control flow, waits, variables, conditions, effects, spawning, lights,
properties, movers) are in
[21-Scripting-Logic-and-Variables.md](21-Scripting-Logic-and-Variables.md). This document lists
every game-system action once.

## Before you start

- You know how to place a Script actor, fill its `Actions` list and start it from a trigger. See
  [20-Scripting-Basics.md](20-Scripting-Basics.md).
- You know how to bind a field of an action to a variable or to a nested action (the Bindings
  section of an action). See
  [21-Scripting-Logic-and-Variables.md](21-Scripting-Logic-and-Variables.md).
- You know the Properties window. See [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- Scripts do not run inside the BioShock editor. Test with Play Map and check visible results.
  See [Testing a script](20-Scripting-Basics.md#testing-a-script).

## How to read an entry

Every entry has the same layout:

**`ActionName`** — What the action does. Fields: `FieldName` (type, default). Row: `the sentence
shown in the Actions row`.

- **Common.** marks an action that is among the most used in the game's own maps.
- **Fields** lists the fields a mapper sets. The lists leave out internal fields. A default
  appears only when it matters. Types are: name (one word, no spaces), string, bool, int, float, class, object,
  enum, list. A field whose name ends in `Label` is always a name; its type is not repeated.
- **Waits** means the script pauses at this action until the action completes. All other actions
  finish at once.
- **Returns** means the action produces a value (yes/no, a number or a name) for a binding on
  another action's field. An example is the `lhs` of a `BooleanStatement` (document 21).
- **Must exist** means the game stops with an error dialog when the named actor is not in the map.
  Other actions can continue without a visible error or do nothing.
- **Row** is the sentence template; `<Field>` stands for the field's value. A field bound to a
  variable or a nested action shows the variable's name or the nested sentence instead.

The action drop-down groups actions by category. The categories are Door, Quests, Facts, Training,
Machines, Inventory, Container, Plasmids, Player, HUD and Input. They continue with Level, Actor,
Pawn, AI, Animation, Environment, Volumes, Lights, AudioVisual, Security and others. The class
name may show with a prefix, for example
`ShockGame.ActionOpenDoor` or `ShockAI.ActionSpawnAI`. The prefix is not part of the name you look
for here.

## How targets are named

Actions find actors in the map by **Label**, not by object name and not by a direct reference. A
Label works like a Radiant `targetname`.

- Every actor has a `Label` in the Scripting category of its Properties window: one word, no spaces.
- A field whose name ends in `Label` shows a drop-down of every Label set on an actor in the
  map. Examples: `DoorLabel`, `TargetLabel`, `AILabel`, `PawnLabel`, `ContainerLabel`,
  `GathererLabel`, and so on. You can still type a name that is not in the list, for an actor
  that a script spawns later. The BioShock editor builds the list when you click the row.
- Several actors may share one Label. Most actions act on **every** actor with the Label. Where an
  action acts on the first match only, the entry says so.
- The **player** has no Label field on quest, inventory, plasmid and HUD actions: those actions
  always act on the local player. Actions that take a pawn Label accept `Player` for the player's
  pawn; the game's own maps use `PawnLabel=Player`.
- A Label field left at `None` gives, on the AI actions, a row that reads `<Field> is not set!`.
  Actor and animation actions default the field to `UNSPECIFIED` and stop the game when you do
  not change it.
- A few fields hold an actor reference instead of a Label (`Spawner`, `NextProtectorVent`,
  `NextGathererBooty`, `AssociatedGathererVent`). Select the actor in a viewport and click the Use
  button of the field.
- Other drop-downs exist on these fields. `ConceptName` lists the concept names of every
  TrainingScript. `MessageName` of a `TrainingMessageTrigger` lists the training messages.
  `Slot_1` of `ActionTestFact` lists the predefined facts. `MapName` lists the bathysphere
  destinations. `Target` of the AI goal actions lists the labels of pawns and squads. The pose
  `AnimationName` of `ActionSpawnAI` has a drop-down too. A field typed as a class, for example
  `ItemClass`, shows a drop-down of the loaded classes. You type every other name field as
  text.

**NOTE:** Edit > Copy and Paste of an actor does not carry its Label. Set the Label again after a
paste.

## Doors and keypads

A door is a `ShockDoor` actor (the game's own maps use subclasses such as `ElevatorDoor` and
`HighRentDoor`). A door has its own open, close, lock and broken logic; the actions below tell the
door what to do and the door animates itself. In the game's own maps a door starts with
`bLocked=True` on the actor and a script unlocks it. A keypad or a button that controls a door is a
separate control actor (`DoorKeypadControl`, `DoorButtonControl`). A lift or any other
`ScriptableMover` is **not** a door: drive it with `ActionSendTriggerMessage` (document 21).

**`ActionOpenDoor`** — Opens every door with the Label. Fields: `DoorLabel`, `StayOpen` (bool, off;
on = the door opens and holds). Row: `Open door with label <DoorLabel>.`

**`ActionCloseDoor`** — Closes every door with the Label. Fields: `DoorLabel`, `ForceClose` (bool,
off). With `ForceClose` on, the door slams shut even when something blocks it. With it off, the
door gets permission to close and closes when its own logic allows. Row: `Close door with label
<DoorLabel>`.

**`ActionLockDoor`** — Locks every door with the Label. Fields: `DoorLabel`. Row: `Lock door with
label <DoorLabel>.`

**`ActionUnlockDoor`** — Unlocks every door with the Label. Fields: `DoorLabel`. Row: `Unlock door
with label <DoorLabel>.`

**`ActionSetDoorBrokenState`** — Sets the first door with the Label to broken or not broken. Fields:
`DoorLabel`, `IsBroken` (bool). Row: `Sets <DoorLabel> to broken.` or `Sets <DoorLabel> to not
broken.` The door must exist. **NOTE:** The error text of this action names `ActionLockDoor`. The
message is about the door you named here.

**`ActionGetIsDoorIdle`** — Returns yes/no: whether the first door with the Label is idle (not
moving). Fields: `DoorLabel`. Row: `Get whether the door is idle.` The door must exist.

**`ActionOpenDoorUsingButton`** — Opens a door through the first `DoorButtonControl` with the Label,
as if the player had used the button. Fields: `DoorButtonControlLabel`. Row: `Open door using a button that
controls it.` The control must exist.

**`ActionDoorKeypadUsed`** — Tells a `DoorKeypadControl` whether the player entered the keycode
correctly.
Use it in the script that answers the keypad's message. Fields: `DoorKeypadControlLabel`, `Success`
(bool). Row: `Tell DoorKeypadControl whether keycode was successfully entered or not.` The control
must exist.

**`ActionKeypadContainerUsed`** — The same for a `KeypadContainer` (a keypad-locked container).
Category Container. Fields: `KeypadContainerLabel`, `Success` (bool). Row: `Tell KeypadContainer
whether keycode was successfully entered or not.` The container must exist.

See also `ActionGathererCrawlThroughDoor` in the AI section: it targets a `ThreeStateDoor`.

## Quests and objectives

A quest is a goal that the player sees in the HUD and the map screen. It has a friendly name, an
arrow actor that the HUD arrow points at, a number of objectives, and hints. The quest manager's
configuration defines quests by name (`QuestManager.QuestNames` in the game's configuration); the
map does not. A quest is hidden until a script initiates it. The quest actions change the local
player's quest state and, by default, show HUD feedback (`New Goal`, `Goal Updated`, `Goal Failed`).
In the game's own maps `ActionInitiateQuest`, `ActionCompleteQuest`, `ActionCompleteQuestObjective`,
`ActionReplaceQuest` and `ActionChangeQuestArrowActor` are the usual quest wiring.

All quest actions share two fields, inherited from the base `ActionQuestBase` (not in the
drop-down). They are `QuestName` (name; type it, there is no drop-down) and `ShowHUDFeedBack`
(bool, on).

**NOTE:** The actions that read a quest (`ActionCompleteQuestObjective`,
`ActionHasQuestBeenAssigned`, `ActionHasQuestBeenCompleted`, `ActionGetNumQuestObjectivesCompleted`)
do not check the name. Check the spelling
against the quest configuration.

**`ActionInitiateQuest`** — Starts the quest and, when it is new and feedback is on, shows `New
Goal: <friendly name>` and makes it the active quest. Fields: `SetAsActiveQuest` (bool, on). Row:
`Initiate Quest '<QuestName>'`.

**`ActionCompleteQuest`** — Completes the quest. Row: `Complete Quest '<QuestName>'`.

**`ActionCompleteQuestObjective`** — Adds completed objectives to a quest that is visible and not
complete. Fields: `NumberOfObjectivesCompleted` (int, 1). Row: `Complete Objective for Quest
'<QuestName>'`.

**`ActionUnCompleteQuestObjective`** — Takes an objective back; feedback shows `Objective
Un-Completed`. Row: `Un-Complete Objective for Quest '<QuestName>'`.

**`ActionFailQuest`** — Fails the quest; feedback shows `Goal Failed`. Row: `Fail Quest
'<QuestName>'`.

**`ActionReplaceQuest`** — Replaces one quest with another; feedback shows `Goal Updated` when the
old quest is visible and not complete. Fields: `ReplacementQuestName` (name),
`CopyObjectivesCompleted` (bool, on). Row: `Replace Quest '<QuestName>' with
'<ReplacementQuestName>'`.

**`ActionToggleQuestVisibility`** — Shows a hidden quest or hides a shown one. Row: `Toggle Quest
Visibility for '<QuestName>'`.

**`ActionChangeQuestArrowActor`** — Points the quest's HUD arrow at another actor. The arrow actor
is a Label. When the level label is empty, the action uses the current level. Fields: `ArrowActor` (name),
`ArrowActorLevelLabel`. Row: `Change Quest <QuestName> ArrowActor to <ArrowActor> on level
<ArrowActorLevelLabel>`.

**`ActionHasQuestBeenAssigned`** — Returns yes/no: the quest is not hidden. Row: `Has quest
'<QuestName>' has been assigned.`

**`ActionHasQuestBeenCompleted`** — Returns yes/no: the quest is completed. Row: `Get the number of
completed objectives for quest '<QuestName>'.` **NOTE:** The row and the drop-down name of this
action are the same as the next one. The action itself returns yes/no for completion.

**`ActionGetNumQuestObjectivesCompleted`** — Returns a number: how many objectives of the quest are
complete. Row: `Get the number of completed objectives for quest '<QuestName>'.`

**`actionSetQuestHint`** — Makes a hint the quest's current hint and marks it unseen. The hint name
must be one of the quest's hints. Fields: `QuestName` (name),
`HintName` (name). Row: `Show Hint <HintName> for Quest <QuestName>`.

**`ActionWaitForQuestLogToFinish`** — Waits until the audio of a quest log (an audio diary) stops
playing, or until the timeout. Fields: `QuestLog` (class of the log), `TimeoutSeconds` (float). Row:
`Wait up to <TimeoutSeconds> seconds for QuestLog <QuestLog> to finish playing`. `QuestLog` must be
set. The game's own maps use it right after `ActionGiveItemsToPlayer` with the diary's item class.

## Facts

A fact is a small record of something that happened, kept in the game's fact database. A fact has
three slots: `Slot_1` is a name, `Slot_2` and `Slot_3` are strings and may be empty. The game
asserts facts by itself while the player plays (`ApproachedLockedDoor`, `ClipEmpty`,
`EquippedPlasmid`, `FiredWeapon`, `HackedMachine`, `PlayerHealthLow`, `SavedGatherer`,
`SecurityCameraSeesPlayer`, `TelekinesisUsed`, and others). The adaptive training system reads them.
A script can assert or retract its own facts and test them. The database records when a fact was
first and last asserted and how many times. Facts asserted by a script survive a level change.

All fact actions share the fields `Slot_1` (name), `Slot_2` (string), `Slot_3` (string), inherited
from the base `ActionFact` (not in the drop-down). The row shows the fact as `(<Slot_1>, <Slot_2>,
<Slot_3>)` with empty slots left out.

**`ActionAssertFact`** — Creates the fact, or re-asserts it (the count goes up and the
last-asserted time changes). Row: `Assert fact (<slots>) is true`.

**`ActionRetractFact`** — Removes every fact that matches the slots. Row: `Retract fact (<slots>)`.

**`ActionTestFact`** — Returns yes/no: a matching fact exists. This is a condition: it can sit in
the `testsOr` of an `ActionIf` and in the `testsAnd` of a training condition. Its `Slot_1` has a
drop-down of the predefined fact names. Row: `Is fact (<slots>) true`.

**`ActionFactDuration`** — Returns a number: seconds since the fact was last asserted (0 when there
is no match). Row: `How long since fact (<slots>) was last asserted`.

**`ActionFactTotalDuration`** — Returns a number: seconds since the fact was first asserted (0 when
no match). Row: `How long fact (<slots>) has been true`.

**`ActionFactTimesAsserted`** — Returns a number: how many assertions of the fact since its last
retraction (0 when no match). Row: `Number of times fact (<slots>) was asserted since last
retract`.

## Training messages and concepts

A training message is a tutorial text that the game shows on the HUD. The training configuration
(`training.ini`) defines messages and loading-screen tips by name; the map does not. A concept
is one tutorial topic ("hacking", "using plasmids") with a knowledge level between -1 and 1. A
`TrainingScript` actor (a Script subclass) holds concepts. Each concept holds conditions that
watch facts and adjust the knowledge level. It also holds message triggers that show a message
when the level crosses a threshold. The game ticks a TrainingScript every frame.

Three actions are structural. The drop-down offers them only in the right place. A
`TrainingConcept` goes at the top level of a TrainingScript; a `TrainingCondition` and a
`TrainingMessageTrigger` go only inside a concept.

**`TrainingConcept`** — One tutorial topic. Fields: `ConceptName` (name), `KnowledgeLevel` (float,
the start level), `Conditions` (list of `TrainingCondition`), `MessageTriggers` (list of
`TrainingMessageTrigger`), `enabled` (bool, on). Row: `Concept <ConceptName> with initial knowledge
of <KnowledgeLevel>`.

**`TrainingCondition`** — "If all tests are true, then run the actions; or, with no actions, change
the concept's knowledge by the weight." The game queues a condition that becomes true and runs it
in priority order. Fields: `testsAnd` (list of conditions such as `BooleanStatement` or
`ActionTestFact`), `ThenAction` (list of actions), `Weight` (float), `TickDelay` (int, 10),
`Priority` (int). The tests run every `TickDelay` ticks. Row: `If <test> AND <test> Then modify weight by <Weight>` or `Then
<action>, <action>` or `Then do N actions`.

**`TrainingMessageTrigger`** — "When the knowledge reaches the level, and the tests hold, show the
message." `ShownActions` run when the message appears. `NotShownActions` run when the training
manager refused or cleared it. Fields: `KnowledgeLevel` (float), `testsAnd` (list of conditions),
`MessageName` (name, drop-down of the training messages), `ShownActions` (list), `NotShownActions`
(list). Row: `When knowledge of concept reaches <KnowledgeLevel> AND <test> Then show message
<MessageName> and <action>, <action>`.

The other training actions are ordinary and go in any script.

**`ActionShowTrainingMessage`** — Shows a training message now. Returns yes/no: the game queued or
showed the message. Fields: `MessageName` (name; type it). Row: `Show Message <MessageName>`.

**`ActionClearTrainingMessage`** — Removes a queued or shown message. Returns yes/no: the clear
succeeded. Fields: `MessageName` (name). Row: `Clear Training Message <MessageName>`.

**`ActionEnableOrDisableTrainingMessages`** — Turns the non-interrupt training messages on or off;
off also clears the queue. Fields: `EnableTrainingMessages` (bool). Row: `Enable non-interrupt
training messages so they will be displayed.` or `Disable all non-interrupt training messages from
being displayed, and clear all queued messages.`

**`ActionDisableOrEnableConcept`** — Enables or disables a concept. The action searches every
TrainingScript of the map for it. Fields: `ConceptName` (name, drop-down), `Enable` (bool). Row: `Enable
concept<ConceptName>` or `Disable concept<ConceptName>` (no space, as shown).

**`ActionGetConceptKnowledge`** — Returns a number: the concept's knowledge level. Returns nothing
when the concept is not found. Fields: `ConceptName` (name, drop-down). Row: `Knowledge level of
<ConceptName>`.

**`ActionModifyConceptKnowledge`** — Adds the weight to the knowledge level, kept within -1 to 1.
Fields: `ConceptName` (name), `Weight` (float). Row: `Modify knowledge level of <ConceptName> by
<Weight>`.

**`ActionSetConceptKnowledge`** — Sets the knowledge level. Fields: `ConceptName` (name),
`KnowledgeLevel` (float). Row: `Set knowledge level of <ConceptName> to <KnowledgeLevel>`.

**`ActionSetTipPriority`** — Sets the queue priority of a loading-screen tip. Fields: `TipName`
(name), `Priority` (enum: `QUEUE_No`, `QUEUE_Low`, `QUEUE_Normal`, `QUEUE_High`, `QUEUE_Interrupt`).
Row: `Set <TipName> Priority to <Priority>`.

## Machines, hacking and security

A machine is a `ShockMachine` actor. Examples: a vending machine (`DispenserMachine`), a Gene Bank
(`PlasmidEquipStation`), a research station, a security station, a weapon upgrade station, or a
Vita-Chamber (`BaseResurrectionStation`). The security system of a level is one manager. It runs
the alarm and sends security bots at a target. It shuts down when the player hacks a security
station.
Turrets, cameras and bots are AI actors with Labels like any other pawn.

**`ActionDisableOrEnableMachine`** — Enables or disables every machine with the Label; with no
Label, every machine of the class. Fields: `MachineLabel`, `MachineClass` (class, `ShockMachine` or
a subclass), `Enable` (bool). Row: `Enable Machine` or `Disable Machine`, then ` of class
<MachineClass>` and ` with label <MachineLabel>` when set.

**`ActionActivateResurrectionStation`** — Makes every Vita-Chamber with the Label the player's
respawn point, or deactivates it. Fields: `ResurrectionStationLabel`, `ActivateStation` (bool, on).
Row: `Activate resurrection station with label <ResurrectionStationLabel>.` or `Deactivate ...`.

**`ActionDisableOrEnableResurrectionStation`** — Makes a Vita-Chamber usable or not. Fields:
`StationLabel`, `Enable` (bool). Row: `Enable resurrection station with label <StationLabel>.` or
`Disable ...`.

**`ActionEvaluateHack`** — Returns a number: the buy-out cost of hacking the named class, for this
player. Category Hacking. Fields: `HackedClassName` (name). Row: `Evaluate Hacking
<HackedClassName>`.

**`ActionShowBathysphereUI`** — Opens the bathysphere travel menu. Category Level. Fields:
`BathysphereSystem` (name, `BioshockBathyspheres`). Row: `Open bathysphere travel UI`.

**`ActionUnlockBathysphereDestination`** — Unlocks one map in the travel menu. Category Level.
Fields: `MapName` (name, drop-down of the system's destinations), `BathysphereSystem` (name,
`BioshockBathyspheres`). Row: `Allow bathysphere travel to <MapName>`.

**`ActionHackSecuritySystem`** — Shuts the security system down for a time, as if the player had
hacked a security station. Fields: `ShutdownTime` (float, 30). Row: `Shutdown security for
<ShutdownTime> seconds.`

**`ActionUnHackSecuritySystem`** — Ends the shutdown. Row: `Reenable a hacked security system.`

**`ActionHackTurret`** — Makes every turret with the Label friendly to the player, or hostile again.
Fields: `TurretLabel`, `SetHacked` (bool, on). Row: `Hack all turrets with label '<TurretLabel>'.`
or `Unhack ...`.

**`ActionReorientTurret`** — Turns the standby direction of every turret with the Label toward an
actor. Fields: `TurretLabel`, `TowardsActorLabel`. Row: `Reorient <TurretLabel> towards
<TowardsActorLabel>.` Does nothing when the target is missing.

**`ActionStartSecurityAlarm`** — Starts a security alarm against a pawn and spawns security bots.
Category AI. Fields: `TargetLabel` (a pawn), `SecurityBotClass` (class), `NumSecurityBotsToSpawn`
(int, 1), `bForceNewSecurityTarget` (bool), `bInfiniteAlarm` (bool). Row: `Start a Security Alarm
with the Target being a ShockPawn with the label <TargetLabel>` or `TargetLabel is not set!`. Does
nothing when the class is empty or the count is 0.

**`ActionStopSecurityAlarm`** — Stops a running alarm. Row: `Stop any running security alarm`.

**`ActionIsSecurityAlarmOn`** — Returns yes/no: an alarm is on, and (when set) its target has the
Label. Fields: `AlarmTargetLabel`. Row: `Get whether the alarm is on.` or `Get whether the alarm is
on and targeting <AlarmTargetLabel>.`

**`ActionActivateSecurityBot`** — Gives every dormant spawned bot with the bot Label to a pawn as
its ally. Fields: `PawnLabel`, `BotLabel`. Row: `Have <PawnLabel> activate bot <BotLabel>.`

**`ActionAssignNextSecurityBotSpawnLocation`** — Tells the spawning manager where the next security
bots appear. Fields: `SpawnLocationLabel`. Row: `Tells the SpawningManager to spawn SecurityBots at
Actor(s) with the Label <SpawnLocationLabel>` or `SpawnLocationLabel is not set!`.

**`ActionSpawnSecurityBot`** — Spawns a bot from a `SecurityBotSpawner` actor and can hand it to a
pawn at once. Fields: `Spawner` (actor reference), `ImmediatelyGiveBotToPawn` (bool),
`ReceivingPawnLabel`. Row: `Spawn Security Bot using spawner <Spawner>`.

**`ActionSpawnTurret`** — Spawns a turret from a `TurretSpawner` actor. Fields: `Spawner` (actor
reference). Row: `Spawn Turret using spawner <Spawner>`.

**`ActionToggleSecurityCameraSpotlight`** — Turns the spotlight of every camera with the Label on or
off. Fields: `CameraLabel`, `SpotlightOn` (bool). Row: `Turn the spotlight on <CameraLabel> on.` or
`... off.`

**`ActionSetHealthStationUsableByAIs`** — Lets AIs use health stations, or not: all of them, or
those with the Label. Fields: `HealthStationLabel`, `bShouldBeUsable` (bool). Row: `All health
stations will be made usable by AIs.` or `Health stations with label <HealthStationLabel> will be
made not usable by AIs.`

## Containers and inventory

A container is an actor that holds item stacks in slots: a `StaticMeshContainer`, an
`AnimatedContainer`, a `KeypadContainer`, a corpse, or a `Booty` for gatherers. A stack is an item
class plus a count. The player's inventory is a container too, but the player actions need no Label.
You pick item classes from the drop-down of the `ItemClass` field. A class from a content package,
for example an audio diary, appears in the list only after you load its package in a browser.

The stack actions share two fields, inherited from the base `ActionShockInventory` (not in the
drop-down): `ItemClass` (class) and `StackSize` (int, 1).

**`ActionGiveItemsToPlayer`** — Common. Gives a stack to the player without the inventory warnings.
Row: `Give <StackSize> <ItemClass> to the player`. **NOTE:** The game stops with an error when
`ItemClass` is empty or `StackSize` is 0 or less.

**`ActionRemoveItemsFromPlayer`** — Takes a stack from the player. If the held weapon has that
ammunition selected, the action reselects it so the HUD updates. Row: `Remove <StackSize> <ItemClass> from the player`.

**`ActionGetNumItemsInPlayersInventory`** — Returns a number: how many of the item the player has.
`Credits` and `ADAM` as the class give the player's money and ADAM. Fields: `ItemClass` (class).
Row: `Get the number of <ItemClass> in the players inventory`.

**`ActionFilterItem`** — Removes an item class from the random loot rolls, or puts it back. Loot
lists that name the item directly are not affected. Fields: `ItemClass` (class), `UnFilter` (bool).
Row: `Add <ItemClass> to the Loot rolling filtration list.` or `Remove <ItemClass> from the Loot
rolling filtration list (NOTE: does NOT affect LootItemSpecifications).`

**`ActionPlaceItemInContainer`** — Puts a stack into the first container with the Label: tops up
stacks of the same class, then fills the first empty slot. Fields: `ContainerLabel`. Row: `Put
<StackSize> <ItemClass> in <ContainerLabel>.` Check the container's contents in the game.

**`ActionPlaceItemInContainerSlot`** — Puts a stack into one slot of every container with the Label.
Fields: `ContainerLabel`, `Slot` (int), `OverwriteExistingItem` (bool; off = merge when the slot
holds the same class, otherwise the operation fails). Row: `Put<StackSize> <ItemClass> in <ContainerLabel> at slot
<Slot>.` or `Overwrite<StackSize> ...`.

**`ActionRemoveItemFromContainer`** — Removes a count of the item across the slots; `StackSize` 0
removes all of that item. Fields: `ContainerLabel`. Row: `Remove <StackSize> <ItemClass> from
<ContainerLabel>.`

**`ActionRemoveItemFromContainerSlot`** — Removes from one slot; an empty `ItemClass` matches any
item, `StackSize` 0 takes the whole stack. Fields: `ContainerLabel`, `Slot` (int). Row: `Remove
<StackSize> items of any type from <ContainerLabel> at slot <Slot>.` or `Remove <StackSize>
<ItemClass> from <ContainerLabel> at slot <Slot>.`

**`ActionClearContainer`** — Empties every slot of the first container with the Label. Fields:
`ContainerLabel`. Row: `Remove all items from <ContainerLabel>.`

**`ActionGetNumItemsInContainer`** — Returns a number: all items in the container, or only those of
the class. Fields: `ItemClass` (class), `ContainerLabel`. Row: `Get the total number of items in the
container labeled: <ContainerLabel>` or `Get the number of <ItemClass> in the container labeled:
<ContainerLabel>`.

**`ActionSpawnPickup`** — Spawns a pickup actor at another actor and gives it a Label. When
`ItemClass` is set, the pickup holds that stack instead of its own loot. Fields: `PickupClass`
(class), `TargetActorLabel` (where to spawn), `ActorLabel` (the new pickup's Label), `ItemClass`
(class), `StackSize` (int), `StartsPhysical` (bool). Row: `Spawn a pickup of class <PickupClass> at
<TargetActorLabel> with label <ActorLabel>.`

**`ActionRemoveAvailableHoldable`** — Takes a weapon or other holdable away from the player.
Category Weapon. Fields: `HoldableClass` (class). Row: `Remove Holdable <HoldableClass> from the
player.`

## Plasmids, research and mods

Plasmids and gene tonics sit on tracks: `TRACK_Active` (plasmids), `TRACK_Physical`,
`TRACK_Engineering`, `TRACK_Weapons` (tonics), and `TRACK_None`. Research levels come from the
research camera. A "stat" is a number that the pawn's active tonics modify.

**`ActionEquipPlasmid`** — Equips a plasmid in a slot of the player. Fields: `Plasmid` (name),
`slotNumber` (int). Row: `Equip plasmid <Plasmid> on slot <slotNumber>`.

**`ActionUnEquipAllPlasmids`** — Unequips every plasmid. Row: `Unequip all currently equipped
plasmids for the player.`

**`ActionRemoveAvailablePlasmid`** — Takes a plasmid away from the player. Fields: `Plasmid` (name).
Row: `Remove plasmid <Plasmid> from the player.`

**`ActionGetNumAvailablePlasmids`** — Returns a number: plasmids or tonics the player owns on the
track (all tracks when `TRACK_None`). Fields: `Track` (enum). Row: `Get the number of plasmids that
are available to the player.` or `Get the number of <Track> plasmids that are available to the
player.`

**`ActionGetNumEquippedPlasmids`** — Returns a number: plasmids or tonics equipped on the track.
Fields: `Track` (enum). Row: as above with `equipped by`.

**`ActionSetPlasmidSlotLockedState`** — Locks or unlocks one slot on a track. Does nothing for
`TRACK_None`. Fields: `Track` (enum), `Lock` (bool). Row: `Lock a slot for track <Track>.` or
`Unlock ...`.

**`ActionGetResearchLevel`** — Returns a number: the research level of a track. Category Research.
Fields: `ResearchTrack` (name). Row: `Get the current research level for <ResearchTrack>`.

**`ActionModifyStat`** — Returns a number: the base value after the pawn's active tonic mods of a
group modify it. Category Mods. Fields: `PawnLabel` (`Player`), `ModGroup` (name), `BaseValue`
(float). Row: `Modifies the value '<BaseValue>' by <PawnLabel>'s active mods in group '<ModGroup>'.`
The action does not check the pawn. Check that `PawnLabel` names the required pawn.

**`ActionTelekinesisDropObject`** — Makes the player drop what telekinesis holds. Category Player.
Row: `Forces the player to drop anything they are holding with telekinesis`.

## Player, HUD, input, saving and level flow

These actions act on the local player and the game state. An input context is a named set of
controls. The game's own maps push `NullInput` or `NoMovement` during cinematics and pop them
after.

**`ActionDisablePlayerMovement`** — Stops movement, leaning, jumping and crouching, or gives them
back. Fields: `DisableMovement` (bool). Row: `Disable player movement.` or `Reenable player
movement.`

**`ActionForcePlayerCrouch`** — Holds the player crouched, or releases. Fields: `ShouldCrouch`
(bool). Row: `Forcibly crouch the player.`

**`ActionForcePlayerMove`** — Waits. Moves and turns the player to a marker actor (or to a bone of
it) at the given speeds, until in place or timed out. Fields: `MarkerLabel`, `MarkerBoneName`
(name), `TimeOut` (float), `LocationDeltaPerSecond` (float), `RotationDeltaPerSecond` (float). Row:
`Forcibly move the player to the location and rotation of the marker labeled '<MarkerLabel>'.` The
marker must exist.

**`ActionEnableBathysphereModeForPlayer`** — Turns the player's collision off and stops falling, for
a ride inside a moving bathysphere; or restores it. Fields: `EnableBathysphereMode` (bool). Row:
`Enable bathysphere mode for the player.` or `Disable ...`.

**`ActionGetPlayerMapRegion`** — Returns a name: the map-screen region or the HUD region the player
stands in (from the zone's `MapUIRegion` and the level's `MapUIRegions` table). Fields:
`DesiredRegionType` (enum: `DRT_MapUIRegion`, `DRT_MapHUDRegion`). Row: `Get the MapUI region of the
Player` or `Get the MapHUD region of the Player`.

**`ActionDisplayMapHUDRegion`** — Shows an area name banner on the HUD. Category HUD. Fields:
`MapHUDRegionDescription` (string, localized). Row: `Display <MapHUDRegionDescription> in the
'MapRegion' section of the HUD`.

**`ActionSetHUDDisplayState`** — Shows or hides the normal HUD elements. Fields: `EnableHUD` (bool).
Row: `Enable the HUD.` or `Disable the HUD.`

**`ShowNeedleElement`** — Shows the needle HUD element of the final boss fight. Row: `Show the
needle HUD element for the boss fight`.

**`HideNeedleElement`** — Hides it. Row: `Hide the needle HUD element for the boss fight`.

**`ActionPlayHUD`** — Restarts the HUD: resets the UI state and re-equips the first holdable. No
sentence; the row shows `PLAY the HUD`. **CAUTION:** This action sits in the category `DO NOT USE
UNLESS YOU KNOW WHAT YOU ARE DOING`.

**`ActionStopHUD`** — Stops the HUD movie. The row shows `STOP the HUD`. **CAUTION:** Same category
as `ActionPlayHUD`.

**`ActionSetOrUnsetInputContext`** — Pushes an input context, or pops it. Category Input. Fields:
`Context` (name), `Unset` (bool). Row: `Set the input context to '<Context>'.` or `UnSet the input
context '<Context>'.`

**`ActionSetDefaultInputContext`** — Deprecated. Fields: `Context` (name). Row: `Set the default
input context to '<Context>'.` **CAUTION:** The action stops the game with a "deprecated" error
before it does anything. Use `ActionSetOrUnsetInputContext`.

**`ActionDisableOrEnableAdaptiveDifficulty`** — Turns adaptive difficulty on or off. Category
Difficulty. Fields: `Enable` (bool). Row: `Enable adaptive difficulty` or `Disable adaptive
difficulty`.

**`ActionAutoSave`** — Waits. Runs the autosave console command. Category Other. Fields: `Command`
(string, `savegame autosave`). The row shows `Autosave`.

**`ActionEnableOrDisableLevelSaving`** — Forbids or allows saves by the player. Category Level.
Fields: `DisableLevelSaving` (bool). Row: `DISABLE user-initiated savegames.` or `ENABLE
user-initiated savegames.`

**`ActionEnableOrDisableLevelSwitching`** — Forbids or allows level changes. Category Level. Fields:
`DisableLevelSwitching` (bool). Row: `DISABLE level switching.` or `ENABLE level switching.`

**`ActionCanChangeLevel`** — Returns yes/no: a level change is allowed now. Row: `Get whether the
level is in a state where it can be transitioned.`

**`ActionEndGame`** — Plays the ending: the good ending when the player harvested fewer gatherers
than the threshold, else the bad one. Then returns to the main menu. Category Other. Fields:
`NumberOfGatherersKilledToGetBadEnding` (int, 14). Row: `Play ending sequence and return to main
menu`.

**`ActionRunConsoleCommand`** — Runs a console command as the player. Category Debug. Fields:
`Command` (string). Row: `Run command <Command>.` **NOTE:** The drop-down name reads `Run a console
command. DEBUG PURPOSES ONLY.`

## Scripted hand animation

The player's first-person hands can play authored animations, for example when the player picks up
an object in a cinematic. A sequence starts with `ActionStartScriptedHandAnimationSequence`, plays
one or more animations, may attach an object to a hand bone, and ends with
`ActionStopScriptedHandAnimationSequence`. Category Animation.

**`ActionStartScriptedHandAnimationSequence`** — Waits. Takes the hands into the scripted animation
state. Row: `Start a scripted hand animation sequence`. The game stops with an error when the hands
cannot start a sequence now.

**`ActionPlayScriptedHandAnimation`** — Plays an animation on the hands and, when set, on the
attachment. Waits when `WaitForAnimationToFinish` is on. Fields: `HandAnimation` (name),
`AttachmentAnimation` (name), `AnimationEndBehavior` (int, 4), `EaseIn` (float),
`WaitForAnimationToFinish` (bool). `AnimationEndBehavior` is a sum of flags: 1 smart ease out, 2
stop, 4 pause, 8 loop. Row: `Play animation
'<HandAnimation>' on the Player's hands.`

**`ActionApplyScriptedHandAttachment`** — Attaches an actor of the class to a bone of the hands.
Fields: `AttachmentClass` (class), `AttachmentBone` (name). Row: `Attach an attachment of type
'<AttachmentClass>' to bone '<AttachmentBone>'.`

**`ActionRemoveScriptedHandAttachment`** — Removes the attachment. Row: `Remove an existing scripted
attachment from the Player's Hands.`

**`ActionStopScriptedHandAnimationSequence`** — Ends the sequence. Row: `Stop a scripted hand
animation sequence`. The game stops with an error when no sequence is playing.

## Actor animation, damage and appearance

These actions work on placed actors and pawns by Label. Animation actions need an actor that draws a
mesh (`DrawType` `DT_Mesh`). A `ReactiveActor` is an actor that reacts to the player and to scripts;
a `ReactiveAnimatedMesh` carries an authored sequence of animation items. Damage actions use a
damage type from the game's list. The list is `Shocked`, `ShockedInWater`, `Frozen`, `FrozenInWater`, `Burning`,
`Diseased`, `Berserk`, `LatentBerserk`, `GenericPiercing` (the default), `ArmorPiercing`,
`AntiPersonnel`, `Bludgeoning`, `Heat`, `Cold`, `Electric`, `ElectricInWater`, `Explosive`,
`Falling`, `AIGenericPiercing`.

**`ActionPlayAnimation`** — Common. Plays an animation on every actor with the Label on an animation
channel. An empty `Animation` eases out whatever plays on the channel over `TweenTime`. Waits when
`bWaitForCompletion` is on. Fields: `TargetLabel`, `Animation` (name), `AnimationRate` (float, 1),
`TweenTime` (float), `Channel` (int), `EndBehavior` (enum: `EndBehavior_Pause`, `EndBehavior_Loop`,
`EndBehavior_Stop`, `EndBehavior_EaseOut`), `bWaitForCompletion` (bool),
`bPauseUntilEntirelyEasedIn` (bool), `bOnlyPlayOnAlivePawns` (bool, on). Row: `Play animation
'<Animation>' on '<TargetLabel>' at speed <AnimationRate> on channel <Channel> with tween time of
<TweenTime>`. The target must exist and draw a mesh.

**`ActionChangeAnimationRate`** — Changes the playback rate of a named animation that is playing on
the target, at once or ramped. Waits while it ramps. Fields: `TargetLabel`, `TargetAnimationName`
(name), `TargetAnimationRate` (float, 1), `RateChangeTime` (float; seconds per unit of rate, 0 = at
once). Row: `Change playback rate for animation '<TargetAnimationName>' on '<TargetLabel>'. New
rate: <TargetAnimationRate>` plus ` with a change rate of 1 unit every <RateChangeTime> seconds`
when ramped. The target must exist and draw a mesh.

**`ActionControlScriptedSequence`** — Common. Jumps a `ReactiveAnimatedMesh` to item number `RunNow`
of its authored sequence. Category ReactiveActor. Fields: `TargetLabel`, `RunNow` (int),
`UseProvidedTweenTime` (bool), `ProvidedTweenTime` (float). Row: `Go to item #<RunNow> for
ReactiveAnimatedMesh labeled <TargetLabel>`. The target must exist and draw a mesh. The game's own
maps use `RunNow=1` to start a sequence.

**`ActionTriggerReactiveActor`** — Runs the `TriggeredReactions` of every `ReactiveActor` with the
Label. Fields: `ReactiveActorLabel`. The row shows `Make a ReactiveActor execute its
TriggeredReactions`.

**`ActionSpawnReactiveActor`** — Spawns a `ReactiveActor` at another actor and gives it a Label.
Fields: `ReactiveActorClass` (class), `TargetActorLabel` (where), `ActorLabel` (name, the new
Label), `StartsPhysical` (bool). Row: `Spawn a ReactiveActor of class <ReactiveActorClass> at
<TargetActorLabel> with label <ActorLabel>.`

**`ActionAttachCollisionDamageListener`** — Makes a `ReactiveActor` deal damage to what it hits,
credited to an owner actor. Category Physics. Fields: `TargetLabel`, `OwnerLabel`. Row: `Attaches a
collision damage listener to <TargetLabel>`.

**`ActionDealDamage`** — Deals damage to every actor of the class with the Label. Fields:
`DamageeClass` (class, `ShockPawn`), `Target` (name), `DamageAmount` (float, 100), `DamageChance`
(float, 1), `DamageType` (enum, `GenericPiercing`). Row: `Deal <DamageAmount> points of <DamageType>
damage to <Target>`. **CAUTION:** With `Target` empty, the damage goes to **every** actor of the
class in the map.

**`ActionDealDamageInRadius`** — Deals radius damage around every actor with the Label. Fields:
`SourceActorLabel`, `DamageAmount` (float, 100), `DamageType` (enum), `InnerRadius` (int, 256),
`OuterRadius` (int, 256). Row: `Deal <DamageAmount> points of <DamageType> damage in a radius of
<OuterRadius>around <SourceActorLabel>` (or `(Inner=<InnerRadius>,Outer=<OuterRadius>)` when they
differ).

**`ActionInitiateDamage`** — Fires a real weapon attack from one actor toward another, credited to
a third. The attack (projectile, trace, melee or emitter) comes from an ammunition class. Category
Default Category. Fields: `DamagerLabel`, `SourceLabel`, `TargetLabel`, `DamageClass` (class of ammunition),
`OverrideInitialVelocity` (float). Row: `Initiate Damage from location marked by '<SourceLabel>'
towards the location marked by '<TargetLabel>'`. Does nothing when the damager, the source or the
ammunition data is missing.

**`ActionChangeResistanceSet`** — Changes the damage resistance set of every `ReactiveActor` or pawn
with the Label. Fields: `Target` (name), `ResistanceSetName` (name, `Default`). Row: `Change the
Resistance set of '<Target>' to '<ResistanceSetName>'.`

**`ActionSetPawnInvincibility`** — Makes the first pawn with the Label invincible or not. Category
AI. Fields: `PawnLabel`, `bInvincible` (bool, on). Row: `Pawn with label <PawnLabel> will be made
invincible.` or `... not invincible.`

**`ActionGetPawnHealth`** — Returns a number: the health of the first pawn with the Label. Category
Pawn. Fields: `PawnLabel`. Row: `Get the health of a pawn.` The pawn must exist.

**`ActionTeleportPawnToLocation`** — Moves a pawn and its controller to a marker's location and
rotation without fall damage on landing. Fields: `PawnLabel`, `MarkerLabel`. Row: `Move the pawn
with label <PawnLabel> to Marker with label <MarkerLabel>.` The action checks neither Label.
Check both Labels before the test. The game's own maps use `PawnLabel=Player`.

**`ActionWaitUntilActorHasLanded`** — Waits. Drops the actor and waits until it stops falling.
Fields: `TargetLabel`. Row: `Wait for actor '<TargetLabel>' to fall and land.`

**`ActionChangeSkinAtIndex`** — Sets one skin slot of the first actor with the Label to a material.
Fields: `TargetLabel`, `Material` (object), `Index` (int). Row: `Change skin on '<TargetLabel>' at
index <Index> to material '<Material>'.` The target must exist.

**`ActionChangeSkinToPhoto`** — Common. Sets one skin slot to a photo the player took with the
research camera. Fields: `TargetLabel`, `PhotoLabel`, `Index` (int). Row: `Change skin on
'<TargetLabel>' at index <Index> to Photo '<PhotoLabel>'.` The target must exist.

**`ActionChangeStaticMesh`** — Swaps the static mesh of the first actor with the Label. Fields:
`TargetLabel`, `StaticMesh` (object). Row: `Change static mesh on '<TargetLabel>' to static mesh
'<StaticMesh>'.` The target must exist.

**`ActionSetCorpseCanBeRemoved`** — Lets the game clean up an AI's corpse, or holds it. Fields:
`CorpseLabel`, `bCorpseCanBeRemoved` (bool). Row: `Set bCorpseCanBeRemoved for AI <Name> to
<bCorpseCanBeRemoved>`. The corpse must exist. **NOTE:** The row shows the action's own object name,
not the corpse Label.

**`ActionMakeBotsAttack`** — Makes the security bots of every pawn with the controller Label attack
a target. Fields: `ControllerLabel`, `AttackeeLabel`. Row: `Make <ControllerLabel>'s bots attack
<AttackeeLabel>.`

**`ActionMakeControllablesAttack`** — The same for everything the pawn controls. Row: `Make
<ControllerLabel>'s controllables attack <AttackeeLabel>.`

## Environment

These actions change the world around the player. They set the atmospheric pressure of a region:
the game's own maps set pressure at level start, and water and hatches read it. They also control
cascading water volumes, damage volumes, movable spotlights, plant life, sound propagation, effect
contexts and the master volume.

**`ActionChangePressure`** — Common. Sets the pressure of a named region of the level. Category
Environment. Fields: `RegionName` (name; type it, the level's `PressureRegions` list has no
drop-down), `DesiredPressure` (enum: `PL_Normal`, `PL_Low`, `PL_High`). Row: `Set the pressure in
region '<RegionName>' to <DesiredPressure>`.

**`ActionEnableOrDisableCascadingWaterVolume`** — Enables or disables a `CascadingWaterVolume` by
Label. Category Volumes. Fields: `VolumeLabel`, `EnableVolume` (bool). Row: `Enable cascading fluid
volume <VolumeLabel>.` or `Disable ...`. **NOTE:** This action appears to have no effect in the
game. Test it before you depend on it.

**`ActionEnableOrDisableDamageVolume`** — Enables or disables every `ShockDamageVolume` with the
Label. Category Volumes. Fields: `VolumeLabel`, `EnableVolume` (bool). Row: `Enable damage volume
<VolumeLabel>.` or `Disable ...`.

**`ActionSetMovableSpotlightState`** — Common. Turns a `MovableSpotlight` on or off. Category
Lights. Fields: `SpotlightLabel`, `SpotlightOn` (bool). Row: `Turns MovableSpotlight
<SpotlightLabel> on.` or `... off.`

**`ActionSetMovableSpotlightTarget`** — Common. Makes a `MovableSpotlight` track an actor, or stop
tracking when the target is empty. Category Lights. Fields: `SpotlightLabel`, `TargetActorLabel`.
Row: `Set Spotlight <SpotlightLabel> to track <TargetActorLabel>.` or `Stop spotlight
<SpotlightLabel> from tracking.` Does nothing when the target Label is set but not found.

**`ActionControlPlant`** — Waits. Kills or revives plant life drawn with the listed `PlantShader`
materials, over a time. Category AudioVisual. Fields: `PlantShaders` (list of materials), `Duration`
(float; 0 = at once), `bRevive` (bool). Row: `Control plant life`.

**`ActionEnableOrDisableSoundPropagation`** — Turns sound propagation on or off. Category
AudioVisual. Fields: `Enable` (bool). Row: `Enable sound propagation` or `Disable sound
propagation`.

**`ActionSetEffectsSystemContext`** — Adds or removes a named effects context on the player, the
player's hands, the player's head, or everything. Effect responses on those actors can depend on the
context. Category AudioVisual. Fields: `Context` (name), `ContextAppliesTo` (enum: `CT_Player`,
`CT_PlayerHands`, `CT_All`, `CT_PlayerHead`), `RemoveInsteadOfAdd` (bool). Row: `Add Effects Context
'<Context>' to '<ContextAppliesTo>'` or `Remove Effects Context '<Context>' from
'<ContextAppliesTo>'`.

**`ActionFadeVolumeOverride`** — Sets the master volume override, or fades it over a time. Waits
while it fades. Category AudioVisual. Fields: `Volume` (float), `Duration` (float). Row: `Fade the
volume override to '<Volume>' over '<Duration>' seconds.`

## AI

An AI is a `ShockAI` pawn. It can be a splicer (`Aggressor`) or a Little Sister (`Gatherer`, or
`PlayerEscortedGatherer` when she follows the player). It can also be a Big Daddy (`Protector`), a
Houdini splicer (`Assassin`), a Nitro splicer (`Grenadier`) or a spider splicer
(`CeilingCrawler`). Turrets, cameras and security bots are AIs too. The level's spawning manager
owns the spawn zones, the patrols, the
archetypes and the security bots. Most AI actions act on every live AI with the Label and skip dead
ones. When the main Label field is empty, the row reads `<Field> is not set!`.

The AI has a goal planner. A script posts a goal (move here, gather that) with a name and a
priority. The AI works on the highest-priority goal it has. The script can wait for a goal, or
remove it by name. The five goal actions (base `TyrionScriptAction`, not in the drop-down) use the
field `Target` (drop-down of pawn and squad Labels). Unless noted, the goal actions do not wait:
they post the goal and the script continues.

### Goals and orders

**`ActionPostMovementGoal`** — Common. Tells every live AI with the Label to move to an actor. A
gatherer sent to a `GathererVent` returns into the vent. Fields: `Target` (name),
`DestinationLabel`, `goalName` (string, `MovementGoal`), `Priority` (int, 50), `bShouldRun` (bool),
`bShouldBeAggressive` (bool), `bRotateToFaceDestinationRotation` (bool), `DesiredRotation`
(rotator), `DesiredFocusLabel`, `bRotateWhileMoving` (bool), `bShouldNeverSucceed` (bool). Row: `Add
movement goal named <goalName> to <Target>`. The destination must exist.

**`ActionPostGatherGoal`** — Tells every live gatherer with the Label to gather from a `Booty`.
Fields: `Target` (name), `BootyLabel`, `goalName` (string, `GatherGoal`), `bShouldRun` (bool). Row:
`Add gather goal named <goalName> to the gatherer <Target>`. The booty must exist.

**`ActionRemoveGoal`** — Removes the goal with the name from every AI with the Label. Fields:
`Target` (name), `goalName` (string). Row: `Remove goal named <goalName> from <Target>`.

**`ActionWaitForGoal`** — Waits until the named goal of the first pawn with the Label ends. Returns
a number: 0 completed, 1 failed, 2 timed out; 0 at once when there is no such goal. Fields: `Target`
(name), `goalName` (string), `TimeOut` (float). Row: `Wait for goal named <goalName> to finish` plus
`, or timeout in <TimeOut> seconds`.

**`ActionGathererCrawlThroughDoor`** — Waits. Sends a gatherer through a `ThreeStateDoor`, and waits
until she is through or dies. Fields: `Target` (name), `DoorLabel` (a `ThreeStateDoor`),
`bShouldUnlock` (bool, on), `bShouldRun` (bool), `bShouldBeAggressive` (bool). Row: `Gatherer
<Target> crawls through <DoorLabel>` plus ` and unlocks it`. The door and the gatherer must exist.

**`ActionAttackTarget`** — Common. Makes every live AI with the Label attack a pawn: at once, or
when they see it. Fields: `AILabel`, `TargetLabel`, `bAttackOnSight` (bool). Row: `AIs with label
<AILabel> will attack target with label <TargetLabel> when they see them` or `... right away`.
Check that both Labels name the required actors.

**`ActionSetAIPatrol`** — Puts a splicer on a named patrol of the spawning manager. Fields:
`AggressorLabel`, `PatrolName` (name; type it). Row: `Aggressor with label <AggressorLabel> will be
told to use patrol <PatrolName>`.

**`ActionSetAIState`** — Makes every AI with the Label aggressive or passive. Fields: `AILabel`,
`AIState` (enum: `Passive`, `Agg`; default `Agg`). Row: `Set AI with label: <AILabel> to use AI
state: <AIState>`.

**`ActionTellAIToWait`** — Tells every AI with the Label to stop and wait. Fields: `AILabel`. Row:
`AI with label <AILabel> will be told to wait.`

**`ActionTellAIToContinue`** — Releases the wait. Fields: `AILabel`. Row: `AI with label <AILabel>
will be told to continue.`

**`ActionTellAIToSendWeaponFireMessage`** — Makes the AI send a message each time it fires a weapon:
one weapon by Label, one weapon class, or any weapon. A script with `TriggeredBy` set to the AI can
answer it. Fields: `AILabel`, `WeaponLabel`, `weaponClass` (class). Row: `AI with label <AILabel>
will send a message when firing a weapon with label <WeaponLabel>` or `... a weapon of class
<weaponClass>` or `... any of its weapons.`

**`ActionPostAINotificationEvent`** — Posts an AI event (a noise, a sight) at the location of an
actor, so that AIs sense it. Fields: `Event` (an inner `AIEventNotification` object: expand it and
click New), `SourceLocationActorLabel`. Row: `Post event at location of actor
<SourceLocationActorLabel>.`

### Spawning and population

**`ActionSpawnAI`** — Common. Spawns one AI of a class at an actor, or at a random distance from it.
Gives it a Label, a loot container and a patrol. Returns yes/no: spawned. A splicer can start as
a mimic (a corpse pose), a gatherer with a vulnerable state. Fields: `AITypeToSpawn` (class),
`SpawnLocationLabel`, `SpawnedAILabel`, `SpawnedAILootContainer` (inner container object),
`SpawnedAIPatrol` (name), `MinRadiusToSpawnAroundSpawnLoc` (float), `MaxRadiusToSpawnAroundSpawnLoc`
(float), `StartOutAsMimic` (bool), `SpawnedMimicInitialPose` (`AnimationName` with a drop-down,
`PoseName`, `PoseRagdollState`), `ProtectorGuardRange` (range), `OverriddenAIArchetypeNames` (list
of names; the game picks one at random), `bCorpseCanBeRemoved` (bool, on), `GathererVulnerableState`
(enum: `AlwaysVulnerable`, `InVulnerableUntilSaved`, `NeverVulnerable`), `bForceSpawn` (bool). Row:
`Spawn AI <AITypeToSpawn> at <SpawnLocationLabel>` or `Spawn AI <AITypeToSpawn> between <Min> and
<Max> units from <SpawnLocationLabel>`. A `ScriptedAI` class needs an archetype in
`OverriddenAIArchetypeNames`; otherwise the game stops with an error.

**`ActionSpawnLinkedGathererAndProtector`** — Spawns a Big Daddy and the gatherer of a vent, linked
as a pair. When only one of the two spawns, the game destroys it again. Fields: `ProtectorTypeToSpawn`
(class), `AssociatedGathererVent` (actor reference), `ProtectorLabel`, `GathererLabel`,
`ProtectorSpawnLocationLabel`, `GathererSpawnLocationLabel`, `ProtectorLootContainer` and
`GathererLootContainer` (inner container objects), `OverriddenProtectorArchetypeNames` and
`OverriddenGathererArchetypeNames` (lists), `bProtectorCorpseCanBeRemoved` (bool, on),
`bGathererCorpseCanBeRemoved` (bool, on), `GathererVulnerableState` (enum), `bForceSpawn` (bool).
Row: `Spawn Protector <ProtectorTypeToSpawn> at <ProtectorSpawnLocationLabel> and linked Gatherer at
<GathererSpawnLocationLabel>`.

**`ActionSpawnPlayerEscortedGatherer`** — Spawns a gatherer that the player escorts, out of a vent
or at a marker. Skipped when the spawning manager has AI spawning off. Fields: `GathererVentLabel`,
`SpawnPositionLabel`, `SpawnedGathererLabel` (`PlayerEscortedGatherer`), `bShouldPlayerEscort`
(bool), `bDontWaitForPlayer` (bool), `bCorpseCanBeRemoved` (bool, on), `bForceSpawn` (bool),
`OverriddenAIArchetypeNames` (list), `GathererVulnerableState` (enum). Row: `Spawn a player escorted
gatherer at <GathererVentLabel>`.

**`ActionDestroyAIs`** — Destroys every AI of a class in the map, except those with a Label in the
exception list. By default it also spares AIs in full detail near the player. Stunned gatherers
stay. Fields: `BaseClass` (class), `LabelExceptions` (list of names), `bOnlyLowDetailAIs` (bool,
on). Row: `Destroy all AIs of type <BaseClass>, with some exceptions.` or `..., with no exceptions.`
or `BaseClass not set!`.

**`ActionGetNumAIsInSpawnZone`** — Returns a number: AIs of the class in a spawn zone. Fields:
`SpawnZoneName` (name), `AITypeToCount` (class, `ShockAI`). Row: `Get the number of <AITypeToCount>
in spawn zone: <SpawnZoneName>`.

**`ActionManipulateSpawnZoneRepopulation`** — Turns the repopulation of splicers and of Big Daddies
in a spawn zone on or off. Fields: `SpawnZoneName` (name), `AggressorRepopulationState` and
`ProtectorRepopulationState` (enum: `NoChange`, `Enable`, `Disable`). Row: `Manipulate SpawnZone
<SpawnZoneName> Repopulation to <AggressorRepopulationState> for Aggressors and
<ProtectorRepopulationState> for Protectors.` or `SpawnZoneName is not set!`.

**`ActionSetSpawnerRepopulationState`** — Lets every spawner with the Label spawn AIs, or not.
Fields: `SpawnerLabel`, `Flag` (bool). Row: `Set <SpawnerLabel> to spawn AIs` or `Set <SpawnerLabel>
to NOT spawn AIs`.

**`ActionSetGathererVentPlayerCanSpawn`** — Lets the player call a gatherer out of a vent, or not.
Fields: `GathererVentLabel`, `Flag` (bool). Row: `Set <GathererVentLabel> to allow the player to
spawn a gatherer` or `... to NOT allow ...`.

**`ActionSetCorpseFadeoutTime`** — Fades every AI with the Label out and destroys it. Fields:
`AILabel`, `FadeOutDuration` (float, 3). Row: `AI with label <AILabel> will be fade out over
<FadeOutDuration> seconds.`

### Perception, combat and appearance

**`ActionTweakAIHearing`** — Turns hearing on or off for AIs with the Label, or for every AI of a
class when the Label is empty. Fields: `AILabel`, `AIClass` (class, `ShockAI`), `bTurnHearingOn`
(bool). Row: `Turn hearing on for AIs with the label: <AILabel>` or `Turn hearing off for any AI of
class: <AIClass>`.

**`ActionTweakAIVision`** — Common. Turns vision on or off: of everything, or of the player only;
and can make the AI always see the player. Fields: `AILabel`, `AIClass` (class, `ShockAI`),
`bTurnVisionOn` (bool), `bAffectVisionOfPlayerOnly` (bool), `bAlwaysSeePlayer` (bool). Row: `Turn
vision of the Player only on for AIs with the label: <AILabel>` or `Turn vision of all other Pawns
off for any AI of class: <AIClass>`, plus ` and always see the player`.

**`ActionToggleAIAttacking`** — Allows or forbids attacking, for splicer-type AIs with the Label.
Fields: `AILabel`, `bCanAttack` (bool). Row: `AI with label <AILabel> will be allowed to attack.` or
`... not be allowed to attack.`

**`ActionToggleAIReactions`** — Sets nine reaction switches on AIs with the Label, each
`DoNotChange`, `Use` or `DoNotUse`. Fields: `AILabel`, `FullBodyHitReactions`, `QuickHitReactions`,
`FallDownHitReactions`, `EventReactions`, `BurningAnimations`, `BurningBehavior`, `DouseBehavior`,
`InsectSwarmAnimations`, `InsectSwarmBehavior` (all enum). Row: `Toggling an AI with label
<AILabel>'s reaction values.`

**`ActionSetCollisionAvoidance`** — Makes AIs with the Label avoid other pawns, or not. Fields:
`AILabel`, `bShouldUseCollisionAvoidance` (bool). Row: `AI with label <AILabel> will avoid other
pawns.` or `... will NOT avoid other pawns.`

**`ActionSetAIVulnerability`** — Makes AIs with the Label vulnerable or invulnerable, and can forbid
death and, for gatherers, unconsciousness. Fields: `AILabel`, `bVulnerable` (bool, on), `bCannotDie`
(bool), `bCannotBecomeUnconscious` (bool). Row: `AI(s) with label <AILabel> will be made vulnerable`
plus ` and won't be able to die.`, or `... will be made invulnerable (and won't be able to die no
matter what was set!)`.

**`ActionClearAIDamageStates`** — Clears shocked, burning, frozen, berserk and security beacon
states on AIs with the Label. Fields: `AILabel`. Row: `AI with label will have its damage states
cleared out.` (the Label is not shown).

**`ActionSetAINormalLODOverrideTime`** — Forces normal AI detail for a time. Fields: `AILabel`,
`LODOverrideTime` (float). Row: `AI with label <AILabel> will be told to use normal LOD for
<LODOverrideTime> seconds.` **NOTE:** The action applies to every live AI in the map. The Label only
appears in the row.

**`ActionSetAIRangedWeaponAccuracy`** — Sets the accuracy ranges of AI ranged weapon actors with the
Label, against the player and against AIs. Fields: `RangedWeaponLabel`, `AccuracyRangeVsPlayer`,
`AccuracyChangeTimeRangeVsPlayer`, `AccuracyRangeVsAI`, `AccuracyChangeTimeRangeVsAI` (all range).
The row shows `Change the accuracy on an AI's ranged weapon`.

**`ActionModifyLocomotionKeyword`** — Adds a locomotion keyword with a priority to AIs with the
Label, or removes it. Keywords select movement animation sets. Fields: `AILabel`, `keyword` (name),
`KeywordPriority` (int, 1), `bAddKeyword` (bool, on). Row: `AI with label <AILabel> will have a
keyword <keyword> added or set to the value <KeywordPriority>` or `... removed.`

**`ActionMuteAI`** — Silences AIs with the Label, or lets them talk again. Fields: `AILabel`,
`bShouldMuteAI` (bool). Row: `AI with label <AILabel> will be muted.` or `... will be told to start
blabbing.`

**`ActionAISpeech`** — Plays or stops a speech event on live AIs with the Label. Fields: `AILabel`,
`SpeechEventLabel`, `bStopSpeech` (bool). Row: `AI with label <AILabel> will play
<SpeechEventLabel>` or `... will stop playing <SpeechEventLabel>`.

**`ActionStartAIHeadTracking`** — Makes AIs with the Label look at an actor, as a quick look or a
casual look, for a time. Fields: `AILabel`, `HeadTrackTargetLabel`, `bIsQuickLook` (bool),
`Duration` (float), `Offset` (vector). Row: `AI with label <AILabel> will head track target with
label <HeadTrackTargetLabel>`.

**`ActionStopAIHeadTracking`** — Stops the look. Fields: `AILabel`. Row: `AI with label <AILabel>
will be told to stop head tracking.`

**`ActionToggleAIAttachmentVisibility`** — Hides or shows the attachments of a category on AIs with
the Label. Fields: `AILabel`, `AttachmentCategory` (name), `bHideAttachments` (bool). Row: `Hide the
attachments of category <AttachmentCategory> of AIs with label <AILabel>` or `Show ...`.

**`ActionToggleAIWeaponVisibility`** — Hides or shows the active weapon of AIs with the Label.
Fields: `AILabel`, `bShowWeapon` (bool). Row: `Show the active weapon of AIs with label <AILabel>`
or `Hide ...`.

**`ActionRagdoll`** — Knocks AIs with the Label into ragdoll with an impulse. Fields: `AILabel`,
`HitImpulseDirection` (vector), `HitMomentumImparted` (float), `bRelativeToAIRotation` (bool). Row:
`Make <AILabel> go into ragdoll`.

### Gatherers and protectors

**`ActionAssignNextGathererBooty`** — Tells a gatherer which `Booty` to gather next, by reference or
by Label. Make sure that only one gatherer matches. Fields: `GathererLabel`,
`NextGathererBooty` (actor reference), `NextGathererBootyLabel`. Row: `Assign Gatherer with Label
<..> to use the Booty <NextGathererBooty>` or `... with label <NextGathererBootyLabel>`. **NOTE:**
The gatherer Label in the row shows empty. The action uses `GathererLabel`.

**`ActionAssignNextGathererLabel`** — Sets the Label that a Big Daddy gives to the next gatherer he
takes out of a vent. Fields: `ProtectorLabel`, `GathererLabel`. Row: `Assign Protector with Label
<ProtectorLabel> to spawn a gatherer with the label <GathererLabel>`.

**`ActionAssignNextProtectorVent`** — Sets the vent a Big Daddy goes to next. Fields:
`ProtectorLabel`, `NextProtectorVent` (actor reference). Row: `Assign Protector with Label
<ProtectorLabel> to use the vent <NextProtectorVent>`.

**`ActionSetNextGathererPanicPoint`** — Sets where a gatherer runs when she panics. Fields:
`GathererLabel`, `DestinationLabel`. Row: `Tells a Gatherer with label <GathererLabel> to move to to
an Actor with label <DestinationLabel> while panicking.`

**`ActionForceGathererInteractable`** — Lets the player rescue or harvest a gatherer even when the
game would not allow it, or takes that back. Fields: `GathererLabel`, `ForceInteractable` (bool).
Row: `Force the gatherer labeled '<GathererLabel>' to be interactable.` The gatherer must exist.

**`ActionChangePEGWaitDistance`** — Sets how far ahead a player-escorted gatherer waits for the
player. Fields: `PEGLabel`, `WaitDistance` (float). Row: `PEG with label <PEGLabel> set to wait at
distance <WaitDistance>`.

**`ActionResetProtectorAttackTargets`** — Clears a Big Daddy's attack targets. Fields:
`ProtectorLabel`. Row: `Protector with label <ProtectorLabel> will have its Attack targets reset.`

### Houdini, Nitro and spider splicers

**`ActionAssassinTeleport`** — Teleports a Houdini splicer now to an actor, facing another actor.
Fields: `AssassinLabel`, `TeleportLabel`, `TeleportRotationLabel`, `bUseTeleportOutEffects` (bool),
`bSkipEtherTime` (bool). Row: `Tells an Assassin with label <AssassinLabel> to teleport NOW to an
Actor with label <TeleportLabel>`.

**`ActionSetNextAssassinTeleportPoint`** — Sets where the Houdini appears at its next teleport.
Fields: `AssassinLabel`, `TeleportLabel`. Row: `Tells an Assassin with label <AssassinLabel> to
teleport to an Actor with label <TeleportLabel> when teleporting.`

**`ActionSetNextAssassinTeleportInRunDestination`** — Sets where the Houdini runs to after it
appears. Fields: `AssassinLabel`, `RunDestinationLabel`. Row: `Tells an Assassin with label
<AssassinLabel> to teleport in and run to an Actor with label <RunDestinationLabel>.`

**`ActionGrenadierUseLiveGrenadeWeapon`** — Makes the first Nitro splicer with the Label use its
live grenade (over the shoulder) weapon. Fields: `GrenadierLabel`. Row: `Tells a Grenadier with
label <GrenadierLabel> to use its live grenade (over the shoulder) weapon.`

**`ActionSetGrenadierSuicideState`** — Makes Nitro splicers with the Label blow themselves up, now
or after moving. Fields: `GrenadierLabel`, `SpecialCommitSuicideState` (enum: `kNoState`,
`kCommitSuicideImmediately`, `kMoveToCommitSuicide`). Row: `Grenadiers with label <GrenadierLabel>
will commit suicide using the state <SpecialCommitSuicideState>`.

**`ActionToggleCeilingCrawlerRangedAttack`** — Allows or forbids the ranged attack of live spider
splicers with the Label. Fields: `CeilingCrawlerLabel`, `bEnableRangedAttack` (bool). Row: `Ceiling
Crawler with label <CeilingCrawlerLabel> ranged attack will be enabled.` or `... disabled.`

## Critical actions and known limits

Every action has one field of its own, `bIsGameCritical` (bool, on). When a Script has
`ShouldExecuteCriticalActionsImmediately` set, the game runs only the critical actions of the script
at a checkpoint or a level change. It skips the rest. Cosmetic actions are off by default:
`ActionDisplayMapHUDRegion`, `ActionAutoSave`, `ActionSetEffectsSystemContext`,
`ActionWaitUntilActorHasLanded`, `ActionPlayHUD`, `ActionStopHUD`, `TrainingConcept`.

**NOTE:** The actions that only wait do nothing on that immediate path, even with `bIsGameCritical`
on. They are `ActionAutoSave`, `ActionForcePlayerMove`, `ActionPostMovementGoal`, `ActionPostGatherGoal`,
`ActionRemoveGoal`, `ActionWaitForGoal`, `ActionSetAIState`, `ActionDestroyAIs`,
`ActionForceGathererInteractable`, `ActionSpawnPlayerEscortedGatherer`,
`ActionGathererCrawlThroughDoor`.

Known limits:

- The BioShock editor does not validate Labels, quest names, message names, patrol names or
  class names. A wrong name shows only in the game. The actions marked "must exist" give an error
  dialog. Other failures can have no visible error. Use temporary test actions to check execution.
- A class drop-down lists every loaded class of the right kind, including abstract ones, which
  you cannot use. Content classes appear only after you load their package in a browser.

## Related documents

- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [26-AI-Paths.md](26-AI-Paths.md)
- [20-Scripting-Basics.md](20-Scripting-Basics.md)
- [21-Scripting-Logic-and-Variables.md](21-Scripting-Logic-and-Variables.md)
- [23-Scripting-Examples.md](23-Scripting-Examples.md)
