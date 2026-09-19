# Scripting: Logic and Variables

This document explains how the actions of a Script actor run. It also explains how you write
conditions, loops, variables, timers, watchers and calls between scripts in the BioShock
editor. It is the second of four scripting documents. Read it after [20-Scripting-Basics.md](20-Scripting-Basics.md),
when a script must make a decision, count something, wait for a condition, or start another
script. The full list of actions is in
[22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md). Complete scripts are in
[23-Scripting-Examples.md](23-Scripting-Examples.md).

If you know Hammer, this document covers what `logic_branch`, `logic_compare`, `math_counter`
and `logic_timer` do. If you know Kismet or Blueprint, it covers Branch, Loop, variables and
Delay. BioShock has no node graph. Everything is a row in the Actions list of a Script actor.

## Before you start

- You can place a Script actor, set its `Label`, `scriptMessageClass` and `TriggeredBy`, and
  add actions to its `Actions` list. See [20-Scripting-Basics.md](20-Scripting-Basics.md).
- You know the property grid: the Add, Empty, Insert and Delete buttons on array rows, and
  the New line that creates an inner object. See
  [03-Interface-Tour.md](03-Interface-Tour.md) and
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- You can test a map with Play Map. See
  [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).

## How an action runs

### Normal execution

When a script accepts a message, the game runs the `Actions` list from the top to the bottom.
Each action finishes before the next one starts. A waiting action, for example `ActionWait`,
pauses the list. The rest of the game continues while the list waits.

A script runs one message at a time. The game queues a message that arrives while the script
runs. When the list ends, the script runs the list again for the next queued message. The
game loses nothing and merges nothing.

Most actions address actors by `Label`, and most of them act on every actor that carries that
Label. If no actor has the Label, the action has no actor to work on and does nothing.
Some game actions stop the game with an assertion message when a required Label is left at its default.
The BioShock editor does not
check Labels. Test every script with Play Map.

Check visible results in the game. Add temporary screen messages to find how far the action list runs.
SDK logs work, but they do not record retail script execution. No reliable retail runtime logging method is known.
See [Testing a script](20-Scripting-Basics.md#testing-a-script) for the procedure and the logging limits.

### Game-critical actions

Every action has a `bIsGameCritical` property. The default is True. It is False by default on
`ActionWait`, `ActionPlayEffect`, `ActionCinematicFadeView` and `ActionPrintClientMessage`.

The flag matters when the level changes while a script is running. The game then does not wait
for the script. It runs the remaining actions that have `bIsGameCritical` True at once, without
any pause, skips the actions that have it False, and ends the script. An `ActionIf` or an
`ActionLoop` that was running continues from the branch it was in. `ActionWait` does nothing in
this mode. An `ActionBlockingExecuteScript` or `ActionNonBlockingExecuteScript` runs the
game-critical actions of the other script in the same way.

Set `bIsGameCritical` True on an action that must happen even if the player leaves the level
in the middle of the script. Examples: an action that sets a variable or unlocks a door. Set
it False on cosmetic actions.

**CAUTION:** A loop that is still running at a level change enters this mode too. After
1000 passes the game stops with the message `DESIGNER SCRIPT BUG: Encountered potential
infinite loop`. Exit every loop before the level changes, or set `bIsGameCritical` False on
the `ActionLoop` row.

The Script actor has a property `ShouldExecuteCriticalActionsImmediately`. When it is True,
the script never pauses: on every message it runs only its game-critical actions, at once.
Leave it False. The game's own maps never set it.

### Saved games and level changes

A saved game stores the values of a script's variables, its `enabled` flag and the values
typed in its action fields. They travel with the map. A script that was in the middle of
a wait does not continue where it was. The game's own maps restart looping effects with a
second script that listens to `MessageSavegameRestored`.

`Global_` variables live in a container that travels with the player from map to map.
See "Variables".

### ActionExitScript

`ActionExitScript` stops a script. Its field `targetScript` is the Label of the script to stop.
The sentence is `Exit script <targetScript>`.

- A script stops itself when `targetScript` is its own Label. The game's own maps use this as
  an early return inside an `ActionIf`.
- The action stops every Script actor that carries the Label, not only the first one.
- The stopped script ends at its next step. A waiting action ends at once.

## Conditions

### ActionIf

`ActionIf` runs one of two lists of actions.

| Property | Type | Meaning |
| --- | --- | --- |
| `testsOr` | Array of statements | The tests. The condition is true when at least one test is true. |
| `trueActions` | Array of actions | Runs when the condition is true. |
| `elseActions` | Array of actions | Runs when the condition is false. |

The sentence reads `If <test> OR <test> Then <action>, <action>, Else <action>`. With more than
two actions in a list it reads `do N actions`. An empty list reads `do nothing`. The sentence
omits the Else part when `elseActions` is empty.

`testsOr` is an OR. To test that two things are both true, put one `AndStatement` in
`testsOr`. See "AndStatement, OrStatement and NotStatement".

To build a test:

1. Add an `ActionIf` row to `Actions`. Expand it.
2. Click Add on the `testsOr` row. The new `[0]` row opens.
3. In its New line, select `BooleanStatement` and click New. The statement's fields appear.
4. Fill in `lhs`, `logicOp` and `rhs`.
5. Click Add on `trueActions`, select an action class, click New, and fill in its fields.
6. Repeat step 5 for `elseActions` if needed.

Only statement classes can go in `testsOr`: `BooleanStatement`, `TruthStatement`,
`AndStatement`, `OrStatement`, `NotStatement`, `ActionPropertyTest`, `ActionScriptNote` and the
game's `ActionTestFact`.

### BooleanStatement

`BooleanStatement` compares two values.

| Property | Type | Meaning |
| --- | --- | --- |
| `lhs` | Text | The left value, or the name of a variable. |
| `logicOp` | Drop-down | The comparison. Default `LOGICOP_EQUALS`. |
| `rhs` | Text | The right value, or the name of a variable. |

| `logicOp` | Comparison |
| --- | --- |
| `LOGICOP_LESS` | `<` |
| `LOGICOP_LESSEQUAL` | `<=` |
| `LOGICOP_EQUALS` | `==` |
| `LOGICOP_NOTEQUAL` | `!=` |
| `LOGICOP_GREATEREQUAL` | `>=` |
| `LOGICOP_GREATER` | `>` |

The sentence reads `<lhs> <op> <rhs>`, for example `Global_bal > 1`.

The game decides the type of the comparison from the text of `lhs` after their values
replace the variables:

| `lhs` text | Type | How the comparison works |
| --- | --- | --- |
| `True` or `False` | Boolean | `==` and `!=` compare with `rhs` read as a boolean. The other operators are always false. |
| Only digits, `-` and `.`, or empty | Number | Both sides are read as numbers. All six operators work. `rhs` text that is not a number reads as 0. |
| Letters, digits, `_` and `-` | Name | `==` and `!=` compare the text. Case does not matter. The other operators are always false. |
| Anything else | String | Same as Name. |

Examples from the game's own maps: `global_FrameQuest == 0`, `global_bal >= global_tolerance`,
`global_Friend1Clue == given`, `Global_AreDrunk != True`.

To compare a variable, type its name in `lhs` or `rhs`. The BioShock editor records the name
in the Bindings list of the statement (see "The Bindings list"). The sentence shows the name.
At run time the variable's value takes its place.

**NOTE:** If no variable of that name exists when the statement runs, the statement compares
the name itself as text. `Global_Started == True` is then false, and stays false. Create the variable
first, for example with `ActionVariableAssignIfNotExist` at level start.

### TruthStatement

`TruthStatement` has one field, `Value`. The sentence is the value. It is true when the value
is `True`, a number other than 0, a name other than `None`, or a text that is not empty. Type a
variable name in `Value` to test the variable.

### AndStatement, OrStatement and NotStatement

These statements combine other statements.

| Class | Fields | Sentence | True when |
| --- | --- | --- | --- |
| `AndStatement` | `lhs`, `rhs` | `(<lhs>) AND (<rhs>)` | Both are true. |
| `OrStatement` | `lhs`, `rhs` | `(<lhs>) OR (<rhs>)` | At least one is true. |
| `NotStatement` | `rhs` | `NOT(<rhs>)` | `rhs` is false. |

`lhs` and `rhs` are boolean fields. A literal True or False in them is of no use. Each field
takes its value from a nested statement, and the nested statement lives in the Bindings list of
the And, Or or Not statement. This is the procedure for one nested statement. You add every
other nested action in this document the same way.

1. Add an `AndStatement` to `testsOr` (Add, select `AndStatement`, New). Expand it.
2. Expand the Bindings row at the end of its rows. The row's name is `resolveInfoList`.
3. Click Add on `resolveInfoList`. The new entry opens. It has three fields: `Action`,
   `Variable` and `PropertyName`.
4. Type `lhs` in `PropertyName`. This names the field of the `AndStatement` that the entry feeds.
5. Expand `Action`. In its New line, select `BooleanStatement` and click New. The nested
   statement's fields appear in place.
6. Fill in the nested `lhs`, `logicOp` and `rhs`. Leave `Variable` empty.
7. Click Add on `resolveInfoList` again. Type `rhs` in `PropertyName` and create the second
   nested statement under `Action`.

The sentence of the `AndStatement` now reads, for example,
`(global_FrameQuest >= 4) AND (global_Frame3Used != 0)`.

A nested statement can itself be an `AndStatement` or `OrStatement` with its own Bindings. The
game's own maps nest two levels deep, for example `A AND (B AND C)`.

### ActionPropertyTest

`ActionPropertyTest` tests a numeric property of actors by Label. It needs no variable and no
binding. The sentence reads `<maxPasses> <Label>.<propertyPath> <op> <Value>`, or `all` when
`maxPasses` is -1.

| Property | Meaning |
| --- | --- |
| `Label` | The Label of the actors to test. A drop-down lists the Labels in the map. |
| `actorClass` | The class of the actors. Required: the property path is looked up on this class. |
| `propertyPath` | The property name. A path with dots reaches into a struct or into a referenced object, for example `Region.ZoneNumber`. |
| `opTest` | The comparison. Default `OPTEST_LESS`. The drop-down lists the six comparisons. |
| `Value` | The value to compare with, as text. |
| `maxPasses` | How many labelled actors must pass. -1 means all of them. |

The property must be an int, a float or a byte. Other types are not supported. The test reads
only actors that are not static. A Light actor and a static mesh actor are static.

### ActionScriptNote as a comment

`ActionScriptNote` has one field, `Note`. Its sentence is the note. It does nothing at run time.
Use it to document a script. The game's own maps start their reusable scripts with rows such as
`>> USAGE: Change the TriggeredBy field to use the 3 labels of your lift's buttons.` A note
placed in `testsOr` appears in quotes and counts as false.

## Loops

### ActionLoop

`ActionLoop` repeats the actions in its `loopActions` list without end. The sentence is
`Loop`. The only ways out are `ActionExitLoop` and `ActionExitScript`.

Always put an `ActionWait` inside a loop. A loop without a wait repeats without any pause.

### ActionExitLoop

`ActionExitLoop` has no fields. The sentence is `Exit Loop`. It leaves the innermost running
loop after the current action. It does nothing if no loop is running.

### ActionFor

`ActionFor` repeats its `forActions` list a set number of times.

| Property | Default | Meaning |
| --- | --- | --- |
| `counterName` | `forCounter` | The name of the counter variable. |
| `beginValue` | 0 | The first value. |
| `EndValue` | 0 | The last value, inclusive. |
| `forActions` | | The actions to repeat. |

The sentence reads `For <counterName> = <beginValue> to <EndValue>`. The counter is a normal
script variable of type number. Actions inside the loop read it by name. A `Global_` name
works too. The counter increases by 1 after each pass. The game's own maps never use `ActionFor`.

### Polling with a loop

The game's own maps wait for a condition with a loop, a wait and a test. Three patterns:

| Pattern | Rows |
| --- | --- |
| Check every second | `ActionLoop`: `ActionWait Seconds=1`, `ActionVariableIncrement`, `ActionIf` counter `>=` limit Then act, `ActionExitLoop` |
| Check every 2 seconds, leave on a flag | `ActionLoop`: `ActionIf` `OrStatement`(count `>=` 3, flag `==` False) Then stop effect, reset counter, `ActionExitLoop`, Else `ActionWait Seconds=2` |
| Reminder every 2 minutes | `ActionLoop`: `ActionWait Seconds=120`, `ActionIf` flag `!=` True Then show message, `ActionIf` flag `==` True Then `ActionExitLoop` |

The `DrunkManager` script of the game's `4-Recreation` map polls every 0.1 second and calls a second
script with `ActionBlockingExecuteScript` on every pass. A wait of 1 or 2 seconds is enough for
most checks.

**NOTE:** Rows inside a loop in the game's own maps carry `bIsGameCritical` False, and so does
the `ActionLoop` row. See "Game-critical actions".

## Variables

### What a variable is

A variable is a named value that a script creates at run time. It has no actor and no row in
the map. The first `ActionVariableAssign` or `ActionVariableAssignIfNotExist` creates it. The
type comes from the text of the first value:

| First value | Type |
| --- | --- |
| `True` or `False` | Bool |
| Digits, `-` and `.` only, for example `0`, `2.5`, `-1` | Float |
| Letters, digits, `_` and `-`, for example `given`, `Cohen` | Name |
| Any other text | String |

A Float variable supports `+`, `-`, `*` and `/` and all six comparisons. A Bool variable
supports `==` and `!=`. A Name or String variable supports `==` and `!=` without regard to
case; `+` joins text. Variable names are not case-sensitive. `global_BAL`, `Global_bal` and
`global_bal` are one variable.

### Scopes

| Scope | Name form | Lives in | Visible to |
| --- | --- | --- | --- |
| Global | `Global_<name>` | A container that travels with the player | Every script in every map, across level changes and saved games |
| Script-local | `<name>` | The Script actor that created it | The actions of that script |
| Another script's local | `<ScriptLabel>.<name>` | That Script actor | Read from any script by the full form |
| Temporary | (none) | The running script | Created by value actions for their results, deleted when the list ends |

The prefix `Global_` is not case-sensitive. `global_RecStartedFirstTime` is global.

The form `<ScriptLabel>.<name>` reads a script-local variable of another Script actor. The part
before the first dot must be the Label of a Script actor. `ActionVariableAssign` cannot create
a variable with a dot in its name: it reports
`You can only create variables that reside within the current script`.

The game's own maps use `Global_` names for almost everything. They use plain names only for
the settings block of a reusable script, for example `MoverKeyframe` in the lift template.

### Create and assign

| Class | Fields | Sentence | Effect |
| --- | --- | --- | --- |
| `ActionVariableAssign` | `lhs` (variable name), `rhs` (value text) | `<lhs> = <rhs>` | Creates the variable if needed, then sets it. |
| `ActionVariableAssignIfNotExist` | same | `<lhs> = <rhs> (if not exist)` | Creates and sets the variable only if it did not exist. |

`rhs` is text. `rhs="0"` makes a Float. `rhs="True"` makes a Bool. A variable name in `rhs`
copies that variable's value.

The idiom at level start is one `ActionVariableAssignIfNotExist` per flag, so that a return to
the map keeps the values. An `ActionIf` on a first-visit flag then sets the rest with
`ActionVariableAssign`. See [23-Scripting-Examples.md](23-Scripting-Examples.md).

### Arithmetic

| Class | Fields | Sentence | Effect |
| --- | --- | --- | --- |
| `ActionVariableIncrement` | `Target` | `Increment <Target>` | Adds 1 to the variable. |
| `ActionVariableDecrement` | `Target` | `Decrement <Target>` | Subtracts 1 from the variable. |
| `ActionVariableAdd` | `lhs`, `rhs` | `<lhs> + <rhs>` | Returns lhs + rhs. Does not change lhs. |
| `ActionVariableSubtract` | `lhs`, `rhs` | `<lhs> - <rhs>` | Returns lhs - rhs. Does not change lhs. |
| `ActionVariableMultiply` | `lhs`, `rhs` | `<lhs> * <rhs>` | Returns lhs * rhs. Does not change lhs. |
| `ActionVariableDivide` | `lhs`, `rhs` | `<lhs> / <rhs>` | Returns lhs / rhs. Does not change lhs. |
| `ActionRandomNumber` | `minimum` (0), `maximum` (1) | `A random number between <minimum> and <maximum>` | Returns a random float. It is not rounded. |
| `ArithmeticStatement` | `ArithmeticOp`, `lhs`, `rhs` | `(<lhs> <op> <rhs>)` | Returns the result. `ArithmeticOp` is `ARITHMETICOP_ADD`, `ARITHMETICOP_SUBTRACT`, `ARITHMETICOP_MULTIPLY` or `ARITHMETICOP_DIVIDE`. |

`ActionVariableIncrement` and `ActionVariableDecrement` change the variable in place. They are
the only arithmetic used by the game's own maps as a row. The other actions return a value and
change nothing by themselves. Use them as a nested action in a Bindings entry, for example
under the `rhs` of an `ActionVariableAssign`:

- `ActionVariableAssign` with `lhs=Counter`, and a Bindings entry `PropertyName=rhs`,
  `Action=ActionVariableAdd` with `lhs=Counter`, `rhs=5`. Sentence: `Counter = Counter + 5`.

`ArithmeticStatement` works the same way inside a comparison. The game's own maps compare
`global_TallyEnemiesPlasmided <= (global_TallyEnemiesKilled / 2)` with an `ArithmeticStatement`
nested under the `rhs` of a `BooleanStatement`.

### Read a variable in a field

To use a variable's value in a field of any action, type the variable's name in the field.
Examples: `ActorLabel=global_RecVandalarmSoundMarkerName` on an `ActionPlayEffect`,
`NewValue=Global_Count` on an `ActionSetProperty`, `Seconds=MyDelay` on an `ActionWait`.

- In a text or name field, the field shows the name.
- In a number field, the number shown does not change. The BioShock editor records the binding
  and the sentence row of the action shows the variable name. That is how you check it.
- At run time the variable's value replaces the field content. If the variable does not exist,
  the field keeps its typed value.

**NOTE:** Do not give a variable the same name as a Label. A Label typed in a field is also
recorded as a name. If a variable of that name exists, its value replaces the Label.

### The Bindings list

Every action ends with a row named `resolveInfoList`, under the heading Bindings. It is an
array. Each entry says where one field of the action gets its value when the action runs.

| Field | Meaning |
| --- | --- |
| `Action` | A nested action. When the action runs, the nested action runs first and its result is written into the field named in `PropertyName`. |
| `Variable` | A variable name. When the action runs, the variable's value is written into the field named in `PropertyName`. If no such variable exists, the field keeps its typed value. |
| `PropertyName` | The name of the field of this action that the entry feeds, exactly as shown in the grid, for example `lhs`, `rhs`, `Seconds`, `NewValue`. |

An entry uses `Action` or `Variable`, not both. A field with no entry keeps the value you typed.
A script that uses only typed values needs no entries at all.

The BioShock editor writes the `Variable` entries for you. When you type an identifier into a
field, it adds an entry. An identifier is letters, digits, `_` and `.`; it is not `True`,
`False`, `None` or a number. When you replace the identifier with a literal, the BioShock
editor removes the entry. You add the `Action` entries by hand.

Procedure (a): a variable in a field.

1. Select the action row and expand it.
2. Type the variable name in the field, for example `MyDelay` in `Seconds`.
3. Expand `resolveInfoList`. One entry shows `Variable=MyDelay`, `PropertyName=Seconds`.
4. Check the sentence row of the action. It reads `Wait MyDelay seconds`.

Procedure (b): a nested value action feeding a field. The example waits for the `MoveTime` of
a mover, as the lift script of the game's own maps does.

1. Add an `ActionWait` row and expand it.
2. Expand `resolveInfoList` and click Add.
3. Type `Seconds` in `PropertyName`.
4. Expand `Action`. In the New line, select `ActionGetProperty` and click New.
5. Fill in the nested action: `Object=Lift`, `Property=MoveTime`.
6. Leave `Variable` empty. The value in `Seconds` does not matter. The nested action replaces
   it when the action runs.
7. Check the sentence row. It reads `Wait Get Lift.MoveTime seconds`.

The same procedure nests `ActionGetMessageValue`, `ActionCalcDistance`, `ActionRandomNumber`,
`ArithmeticStatement`, an `ActionVariable...` arithmetic action, or a statement. The nested
action's own fields can carry variable names and their own Bindings.

## Reading values

### ActionGetProperty and ActionSetProperty

| Class | Fields | Sentence | Effect |
| --- | --- | --- | --- |
| `ActionGetProperty` | `Object` (a Label), `Property` | `Get <Object>.<Property>` | Returns the value of the property as text. Use it as a nested action. |
| `ActionSetProperty` | `Object` (a Label), `Property`, `NewValue` (text) | `Set <Object>.<Property> = <NewValue>` | Sets the property on every actor that carries the Label. |

`Property` shows a drop-down with the properties of the actor that `Object` names. Set `Object`
first, then click `Property`. For `ActionSetProperty` with several actors under one Label, the
list holds the properties that all of them share.

`NewValue` is text in the form the Properties window shows. Examples from the game's own maps:
`NewValue="true"`, `NewValue="39"` for `MoveTime`, `NewValue="Texture'Gent_Intro.LightDim_Down'"`
for `Skins`. The action handles `Property=bHidden` and `Property=Label` specially so that the
actor updates correctly.

`ActionSetProperty` is the general switch of BioShock scripting. The game's own maps use it to
disable a trigger (`Object=StepLightsTV`, `Property=Disabled`, `NewValue="true"`) and to enable
a script (`Object=LaMerScript`, `Property=enabled`, `NewValue="true"`). They also use it to
change a mover's speed (`Object=Lift`, `Property=MoveTime`).

### ActionGetMessageValue

`ActionGetMessageValue` reads one field of the message that started the script. Its field
`Property` is the name of the message field. Examples: `Instigator` on a trigger message,
`RA` on a reactive actor message, `Keycode` on a keypad message. The sentence reads
`<MessageClass>.<Property>`. Use it as a nested action.

The value comes back as text. A class field comes back as `Class'Package.Name'`.

**NOTE:** The value exists only in the script that received the message. A script started by
`ActionBlockingExecuteScript` or `ActionNonBlockingExecuteScript` has no current message, and
`ActionGetMessageValue` returns nothing there. Read the message in the first script and pass
the value in a variable.

The lift script of the game's own maps tests which button sent the message with a
`BooleanStatement`. Its `lhs` is bound to `ActionGetMessageValue` with `Property=RA`, and its
`rhs` is the button's Label.

### ActionCalcDistance

`ActionCalcDistance` has two Label fields, `actorOne` and `actorTwo`. The sentence is
`Distance between <actorOne> and <actorTwo>`. It returns the distance in units between the two
actors. Use it as a nested action, for example in the `lhs` of a `BooleanStatement`.

### ActionGetLevelLabel

`ActionGetLevelLabel` has no fields. The sentence is `Get the label of the Level.` It returns
the level label: the map file name in lower case, for example `4-recreation`. The game's own
maps use it to make one copied script behave differently per map. The test is an `ActionIf`
with a `NotStatement`. Its `rhs` is bound to a `BooleanStatement` with `lhs` bound to
`ActionGetLevelLabel` and `rhs="7-gauntlet"`.

## Timers

A timer is a delayed message. Two actions control it:

| Class | Fields | Sentence | Effect |
| --- | --- | --- | --- |
| `ActionStartTimer` | `Seconds` | `Start timer for <Seconds> seconds` | Starts the timer of this Script actor. It fires once. |
| `ActionStopTimer` | `scriptLabel` | `Stop timer for <scriptLabel>` | Cancels the timer of every Script actor with that Label. |

When the timer fires, the Script actor sends a `MessageTimerExpired` under its own Label.
The receiver can be the same Script or another Script.
An accepted message waits in the queue if the receiving Script is still running.

To use a separate receiver:

1. In the first script, add `ActionStartTimer` with `Seconds` set.
2. Place a second Script actor. Set `scriptMessageClass` to `MessageTimerExpired` and
   `TriggeredBy` to the Label of the first script.
3. Put the delayed actions in the second script's `Actions`.

The game's own `6-Slums` map uses this. The `HammerLock` script sets
`global_HammerLock = 1`, runs `ActionStopTimer scriptLabel=HammerLock` and then
`ActionStartTimer Seconds=300`. The `HammerUnLock` script, with `TriggeredBy="HammerLock"` and
`scriptMessageClass=MessageTimerExpired`, sets the variable back to 0.

To receive the timer message in the same Script, set `TriggeredBy` to that Script's own Label.
Set `scriptMessageClass` to `MessageTimerExpired`. Restart the timer in the action list to repeat the cycle.
Start the first cycle with a call from another script. The `PlasmidJuggler` script uses this pattern with a 40-second timer.

Timers are rare in the game's own maps: four uses in the whole game. An `ActionWait` inside
the same script does the same job for most delays.

## Watchers

A watcher polls a condition once per second and sends a message when it becomes true. The
game's own maps never use watchers. A loop with `ActionWait` and `ActionIf` does the same job.
Watchers are rarely needed.

| Class | Fields | Sentence |
| --- | --- | --- |
| `ActionCreateWatcher` | `newWatcher` (inner object) | `Create watcher '<watcherName>' to: <watcher sentence>` |
| `Watcher` | `watcherName`, `enabled` (True), `watchedExpression` | `Send a watch message when <watchedExpression> is true` |
| `ActionEnableWatcher` | `scriptName`, `watcherName` | `Enable watcher <scriptName>.<watcherName>` |
| `ActionDisableWatcher` | `scriptName`, `watcherName` | `Disable watcher <scriptName>.<watcherName>` |

To create a watcher, add `ActionCreateWatcher`, expand `newWatcher`, select `Watcher` in the
New line and click New. Set `watcherName`. `watchedExpression` is a boolean field. Bind a
statement to it through the Bindings list of the `Watcher`, with `PropertyName=watchedExpression`,
the same way as for an `AndStatement`. A literal True fires the watcher on its first poll; a
literal False never fires.

The watcher runs on its own after the action. Every second it evaluates the statement. When
the statement is true, the watcher disables itself and sends a `MessageWatcher` under the
Label of the script that created it. The message carries `watcherName`. The receiver is a
Script actor with `scriptMessageClass=MessageWatcher` and `TriggeredBy` set to the creating
script's Label. Set `watcherName` in that script's `messageFilter` to react to one watcher only.

`ActionEnableWatcher` restarts a watcher that has fired or that is no longer enabled. `scriptName` empty
means the current script; otherwise it is the Label of the script that owns the watcher.

## Calling other scripts

### ActionBlockingExecuteScript and ActionNonBlockingExecuteScript

Both actions start another Script actor. The field `targetScript` is the other script's Label.
Its drop-down lists the Labels of the Script actors in the map.

| Class | Sentence | Behaviour |
| --- | --- | --- |
| `ActionBlockingExecuteScript` | `Blocking execute script <targetScript>` | The other script's actions run now, one after the other, inside this script. This script continues after the last one. |
| `ActionNonBlockingExecuteScript` | `Non-blocking execute script <targetScript>` | The other script starts on its own. This script continues at once. |

The called script needs no `TriggeredBy` and no `scriptMessageClass`. Leave both empty on a
script that is only called. This is how the game's own maps split a long sequence into parts,
run several sequences at the same time, and share a routine. Four scripts call the
`AnyFriendKilled` script blocking, and it counts the kills in a global variable.

Rules:

- A called script that has `enabled` False refuses to run.
- A called script that is still running refuses to run again.
- The call reaches only the first Script actor with the Label, even if several carry it.
- The called script has no current message. See "ActionGetMessageValue".
- Blocking calls can nest: a called script can call another one.

### ActionSendTriggerMessage

`ActionSendTriggerMessage` sends a `MessageTrigger` under the Label of this script. Its field
`Instigator` is a Label carried in the message; leave it empty to use the script's own Label,
or set it to `Player`. The sentence is `Send Trigger Message`.

The receivers are the actors whose `TriggeredBy` contains this script's Label:

- A `ScriptableMover` with the script's Label in `TriggeredBy` moves as it does for a trigger.
  With `InitialState=TriggerToggle` it goes to its next key. This is how a script moves a lift
  or a platform. Never use the door actions on a mover.
- A Script actor with `scriptMessageClass=MessageTrigger`, or with the base class `Message`,
  and the script's Label in `TriggeredBy` starts.

One mover can list several scripts: `TriggeredBy="BathroomStallScript, ThroughBrickWallScript"`.

### Enable and disable scripts

There is no action that enables or disables a script. The game's own maps write the `enabled`
property of the Script actor with `ActionSetProperty`:

| Purpose | Row |
| --- | --- |
| Run once | `ActionSetProperty` with `Object=<own Label>`, `Property=enabled`, `NewValue="False"` as the first or last row |
| Arm another script | `ActionSetProperty` with `Object=<other Label>`, `Property=enabled`, `NewValue="true"` |
| Disarm a trigger | `ActionSetProperty` with `Object=<trigger Label>`, `Property=Disabled`, `NewValue="true"` |

A disabled script ignores messages and refuses calls. The game drops its queued messages. The
self-disable row is the standard "trigger once" of BioShock scripting; more than 40 scripts in
the game's `4-Recreation` map use it.

## General actions

The table lists the general-purpose actions of the scripting package. Each action determines how many matching actors it uses.
Check the action's target rules before you give several actors the same Label.
The door, AI, quest, effect-context and player actions are in
[22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md).

| Class | What it does | Key fields | Sentence |
| --- | --- | --- | --- |
| `ActionSpawnPickup`, `ActionSpawnReactiveActor`, `ActionSpawnAI` | Spawn an actor at a labelled actor and give it a Label. These are game-package actions built on the base spawn action. | `ActorLabel` (Label given to the new actor), `TargetActorLabel` (spawn position) | `Spawn an actor at <TargetActorLabel> with label <ActorLabel>.` |
| `ActionDestroyActor` | Removes actors. Pawns are reported as killed first. | `Target` | `Destroy Actor <Target>` |
| `ActionHideOrShowActor` | Hides or shows actors. | `ActorLabel`, `HideActor` (True = hide) | `Hide <ActorLabel>` / `Show <ActorLabel>` |
| `ActionChangeCollision` | Changes collision flags. Each flag is `SetToTrue`, `SetToFalse` or `DoNotChange`. | `Target`, `CollideActors`, `CollideWorld`, `BlockActors`, `BlockPlayers`, `BlockNonZeroExtentTraces`, `WorldGeometry`, `blockHavok` | `Change the collision of <Target> to ...` |
| `ActionSetActorLabel` | Renames the Label of actors. | `ActorLabel`, `NewLabel` | `Change label of <ActorLabel> to <NewLabel>` |
| `ActionSetLightProperties` | Changes Light actors. Each field is a pair of a value and a `ChangeProperty` flag; only pairs with the flag set are applied. | `Object`, `LightBrightness`, `LightColor`, `LightType`, `LightPeriod`, `LightPhase`, `bCastsShadowMapShadows` | `Set the properties of <Object>` |
| `ActionSetMaterialSwitchIndex` | Selects the entry of a MaterialSwitch material. Takes a material reference, not a Label. | `Material`, `Index` | `Set the current index of <Material> to <Index>` |
| `ActionTriggerMaterials` | Triggers materials. Takes material references. | `Materials` | `Trigger materials <list>` |
| `ActionPlayEffect` | Plays an effect tag on actors. `bIsGameCritical` is False by default. | `EffectTag`, `ActorLabel`, `SlowAlsoTriggerOnStaticActors` (set for static meshes), `LogTriggerInfo` | `Trigger effect event ScriptTrigger with tag '<EffectTag>' on dynamic actors labeled '<ActorLabel>'` |
| `ActionPlayEffectAndWaitForStart` | Plays an effect and waits until its sound starts, or until the timeout. | `EffectTag`, `ActorLabel`, `TimeoutSeconds` (60) | `Wait up to <TimeoutSeconds> seconds for audio '<EffectEventToPlay>' to start playing` |
| `ActionStopEffect` | Stops an effect tag on actors. | `EffectTag`, `ActorLabel` | `Stop effect event ScriptTrigger with tag '<EffectTag>' on actor labeled '<ActorLabel>'` |
| `ActionWaitForCriticalMessageStart` | Waits for a scripted voice line to start, or until the timeout. | `EffectEventToWaitFor`, `TimeoutSeconds` (60), `ActorLabel` | `Wait up to <TimeoutSeconds> seconds for audio '<EffectEventToWaitFor>' to start playing` |
| `ActionWait` | Pauses the script. `bIsGameCritical` is False by default. | `Seconds` (1.0) | `Wait <Seconds> seconds` |
| `ActionPlayMovie` | Plays a movie file. | `MovieName` | `Play movie <MovieName>` |
| `ActionCinematicEnter`, `ActionCinematicExit` | Enters or leaves cinematic mode and hides or shows the HUD. | none | `Enter cinematic mode` / `Exit cinematic mode` |
| `ActionCinematicFadeView` | Fades the view to a colour and back. The script waits until the fade ends. `bIsGameCritical` is False by default. | `fadeStart`, `fadeEnd` (colours), `fadeAlphaStart` (0), `fadeAlphaEnd` (1), `Duration` (2), `holdDuration`, `bRestoreFadeControl` (True) | `Fade view` |
| `ActionChangeLevel` | Loads another map. Does nothing while the player is dead. | `MapName`, `StartLocationLabel`, `bShowLoadingMessage`, `persist` (True) | `Change level to map <MapName>` |
| `ActionOpenMenu` | Opens a menu of the game's interface. | `Menu`, `param_1`, `param_2` | `Open menu <Menu>` |
| `ActionReturnToMainMenu` | Leaves the game to the main menu. | none | `Return to main menu` |
| `ActionPrintClientMessage` | Prints text on the HUD. `bIsGameCritical` is False by default. | `MessageText`, `MessageType` | `Print '<MessageText>' to the HUD.` |
| `ActionApplyImpulse` | Gives a physics actor a velocity. | `Target`, `Velocity`, `BoneName` | `Apply a velocity of <Velocity> on <Target>.` |
| `ActionApplyConstantForce` | Pushes a physics actor for a time. | `Target`, `Force`, `Duration`, `Delay`, `BoneName` | `Apply a force of <Force> on <Target> for <Duration> seconds after waiting <Delay> seconds.` |
| `ActionFreezeHavokActor` | Freezes or unfreezes a physics actor. | `Target`, `Freeze` (True), `ActivateWhenUnfreezing` (True) | `Freeze <Target>.` / `Unfreeze <Target>.` |
| `ActionEnableOrDisableHavokForceActor` | Enables or disables a force actor. | `Target`, `enabled` (True) | `Enable HavokForceActor <Target>.` |
| `ActionTriggerHavokForceActor` | Fires a force actor. | `Target` | `Trigger HavokForceActor <Target>.` |
| `ActionChangePawnPhysics` | Turns a pawn's physics off or on. | `Target`, `DisablePhysics`, `EnableRootMotionWhenPhysicsDisabled` | `Enable the Unreal physics on <Target>` / `Disable ... the Unreal physics on <Target>` |
| `ActionSetPlayerInvincibility` | Makes the player invincible, or not. | `bInvincible` (True) | `Player is invincible.` / `Player is not invincible.` |
| `ActionAwardAchievement` | Awards an achievement. The drop-down lists the achievements. | `Achievement` | `Award achievement <Achievement>` |
| `ActionLog` | Does nothing at run time in the game. Use it as a comment. | `Text` | `Log '<Text>'` |
| `ActionDisplayOnScreenDebugMessage` | Shows text on the screen. A development aid; remove it before you ship a map. | `Message` | `Display "[DEBUG: <Message>]" on the screen` |

## Known limits

- Scripts do not run inside the BioShock editor. They run only in the game, through Play Map
  or a deployed map. Check visible results as described in
  [Testing a script](20-Scripting-Basics.md#testing-a-script).
- The BioShock editor does not check Labels, variable names, property names or nested
  bindings. A wrong name shows only when the script runs.
- A variable name typed in a number field does not appear in the field. Check the sentence
  row or the Bindings list.
- `scriptMessageClass` lists every message class, including abstract ones. `messageFilter`
  rows show an object path, not a sentence.
- Empty and Delete on an array row have no confirmation. The `...` button on an action row
  does nothing.
- Edit > Copy and Edit > Paste of an actor do not carry its `Label`. Set the Label again after
  a paste.
- `ActionLog` does nothing at run time. The game's own maps never use `ActionFor` or watchers.
  Retail timer examples include `HammerLock`, `HammerUnLock` and `PlasmidJuggler`. See "Timers" above.
  Loops with `ActionWait` are the usual pattern for repeated checks.

## Related documents

- [20-Scripting-Basics.md](20-Scripting-Basics.md): the Script actor, triggers and messages.
- [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md): every action, by game system.
- [23-Scripting-Examples.md](23-Scripting-Examples.md): complete scripts from the game's own maps.
- [03-Interface-Tour.md](03-Interface-Tour.md): the property grid.
- [09-Actors-and-Properties.md](09-Actors-and-Properties.md): the Properties window and movers.
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md): Play Map.
- [36-Troubleshooting.md](36-Troubleshooting.md): the log and other problems.
