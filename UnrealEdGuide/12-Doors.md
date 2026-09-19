# Doors

This document is not written yet. It will tell you how to put the game's doors in a map:
which door classes to place from `Doors.pkg` and `ShockDesignerClasses.pkg`, how a door starts
locked and gets unlocked, how a keypad or a button controls a door, how the AI path runs
through a door, and how lifts and other movers differ from doors.

**CAUTION:** None of the game's gameplay systems has been extensively tested on custom maps.
That includes the systems that the SDK's authors have run on maps of their own. Expect
behaviour that differs from the game's own maps, and test every setup in the game.

Until this document exists, the material is spread over these places:

- The door classes and where they live: "Where the placeable classes are" in
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- Movers, the Door navigation point and `bAutoDoor`: "Movers and doors" in
  [26-AI-Paths.md](26-AI-Paths.md).
- The actions that open, close, lock and unlock a door, and the keypad and button controls:
  "Doors and keypads" in [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md).
- Real door and lift scripts from the game's own maps: examples 10, 11 and 12 in
  [23-Scripting-Examples.md](23-Scripting-Examples.md).
- Hacking a keypad: [32-Hacking.md](32-Hacking.md).

## Related documents

- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)
- [23-Scripting-Examples.md](23-Scripting-Examples.md)
- [26-AI-Paths.md](26-AI-Paths.md)
- [32-Hacking.md](32-Hacking.md)
