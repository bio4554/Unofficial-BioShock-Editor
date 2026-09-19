# Sound and Music

This document is not written yet. It will tell you how sound is put in a map: ambient sounds,
the sounds that actors and effects make, music and music changes, audio diaries and voice, and
what the bake carries into the game.

**CAUTION:** None of the game's gameplay systems has been extensively tested on custom maps.
That includes the systems that the SDK's authors have run on maps of their own. Expect
behaviour that differs from the game's own maps, and test every setup in the game.

**NOTE:** The BioShock editor plays part of a map's sound. In the Sound browser, select a
sound and press Play to hear it, Looping to repeat it, and Stop to end it. In a viewport with
Realtime Preview on (press P), the ambient sounds of the actors near the camera play as you
fly. An AmbientSound actor of the game's own maps plays the sound that its Tag selects, as it
does in the game, and what you hear carries the reverb of the zone the camera is in: a
ZoneInfo's ReverbType (category Sound) picks it, and changing the type changes the room as you
listen. Music, voice streams and the sounds that effect events play are not heard in the editor;
test those in the game with Play Map. See [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md).

Until this document exists, the material is spread over these places:

- The sound actors in the class list: "Where the placeable classes are" in
  [09-Actors-and-Properties.md](09-Actors-and-Properties.md).
- The actions that play sounds and music from a script: "Machines, hacking and security" and
  "Environment" in [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md).
- Audio diaries as pickups: [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md).

## Related documents

- [09-Actors-and-Properties.md](09-Actors-and-Properties.md)
- [18-Build-Save-Bake-and-Play.md](18-Build-Save-Bake-and-Play.md)
- [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)
- [24-Pickups-and-Loot.md](24-Pickups-and-Loot.md)
