# Skeletal Meshes and Animation

This document tells you how to put a character or an animated prop of your own into the
BioShock editor. It explains what a skeletal mesh is in BioShock. Then it tells you how to:

- prepare and export the model in Blender
- import it in the Animation browser
- check its animations
- place it in a map.

It also lists what the importer does not support.

## What a skeletal mesh is in BioShock

A skeletal mesh is three objects that live together in one package group:

- The mesh: the vertices, the skin weights, the sockets and the material slots.
- The animation package: the skeleton (the bones, their order and their bind pose) and the
  clips. A clip is one animation. Each clip has a name. The game and the scripts refer to a
  clip by that name.
- The metadata: one record per skeleton and one per clip. They hold the pose markers, the
  notifies and the playback settings. The import writes default records. The editor does not
  edit them.

One skeleton per group. Every mesh in a group shares the group's animation package. Put each
character or prop in a group of its own.

The game's own characters and weapons have this structure. A mesh imported from a glTF file is
the same kind of object as a mesh of the game.

## The Animation browser

Open the Animation browser from the View menu, the toolbar or the Master Browser. It has three
lists at the top: Package, Mesh and Animation Set. The Animation Set list is for the animation
sets of the original Unreal Engine. A BioShock mesh does not use it. The list on the right
holds the clips of the selected mesh. Each line is one clip, with its number of frames in
brackets.

| Control | Effect |
| --- | --- |
| Package list | Selects a loaded package. Use File > Open to load a package from `Content\`. |
| Mesh list | Selects a mesh of the package. The viewport shows it. |
| Clip list | Selects a clip. The viewport plays it. |
| Play/pause, Goto start frame, Goto last frame, advance one frame, back one frame, toggle looping | The playback controls under the viewport. |
| The scrub bar | Drags to a time in the clip. The marks on it show the clip's notifies and pose markers. You cannot edit them here. |
| View > RefPose | Shows the mesh in its bind pose. |
| View > Bones, BoneNames, Influences, Bounds, Wireframe, Collision | Show the skeleton, the bone names, the skin weights, the bounds, the wireframe and the collision. |
| View > Lit, View > Unlit | Lit draws the mesh with a light that follows the camera and a grey ambient. Unlit draws the textures without shading. |
| View > Sync Level Actors | Plays the selected clip on every actor in the map that uses the selected mesh. Turn it off to return those actors to their placed pose. |
| Mesh > Mesh properties | The mesh's properties, among them the Materials slots. |

The Animation menu and the Edit > Linkup items belong to the animation sets of the original
Unreal Engine. They do nothing useful for a BioShock mesh.

## Prepare the model in Blender

The importer reads glTF 2.0. Blender exports it directly with File > Export > glTF 2.0. Other
tools that write glTF 2.0 work the same way. The rules below apply to the file, not to
Blender.

The scene:

- One armature. One mesh object, parented to the armature with an Armature modifier. Join
  several mesh objects into one before you export.
- Apply the rotation and the scale of the mesh and of the armature (Object > Apply). The
  importer takes the bind pose from the skin's bind matrices. It takes the vertices as they are
  in the bind pose. When the rest pose of the file does not agree with the bind matrices, the
  importer writes a warning in the log.
- Faces of any size. The exporter triangulates.

The skeleton:

- One root bone. The skeleton takes its name from the root bone.
- Bone names use letters, digits, `_` and `-`. A space becomes `_`. The importer drops any
  other character. After that, every name must be different from every other name.
- At most 255 bones.

The skin:

- At most four weights per vertex. The importer drops the extra weights and normalizes the
  rest.
- At most 65,535 vertices. One set of texture coordinates.

Sockets:

- A socket is a point on a bone where the game attaches things. To make one, add an Empty,
  parent it to a bone, and name it `SOCKET_<name>`. It becomes the socket `<name>` on that
  bone.

Materials:

- Each material of the mesh becomes one material slot. The importer looks for a loaded material
  with the Blender material's name. Then it tries the name of the base colour texture. Then it
  tries that texture's file name without path and extension. Open the package that holds your
  materials before you import. A slot that finds nothing stays empty. Set it afterwards in
  Mesh > Mesh properties.

Animations:

- Set the scene frame rate to 30. Every action in the file becomes one clip with the name of
  the action. You write the clip names in the actor's properties later. Choose them as you
  would type them there.
- A clip plays for the length of its keys. The game decides whether a clip loops. The file does
  not.
- Root motion stays in the root bone.
- You cannot make pose markers and notifies in Blender. The import writes a clip record without
  them.

Size:

- One glTF unit is one Unreal unit. To change this, set a Scale in the import dialog. Blender's
  exporter writes the numbers you modelled with. The player's collision cylinder is 136 units
  tall. Compare the imported mesh with a mesh of the game in the Animation browser before you
  decide on a scale.

Not supported: morph targets, more than one level of detail, ragdoll physics, and files with
Draco compression. When a file has more than one skinned mesh, the importer reads the first
and ignores the rest.

## Export from Blender

In File > Export > glTF 2.0 (the option names are Blender's, and a newer version can group
them differently):

1. Format: glTF Binary (`.glb`).
2. Include: Selected Objects when the scene holds other things. Custom Properties off.
3. Transform: +Y Up (the default).
4. Mesh: Apply Modifiers on. Compression off.
5. Skinning: Include All Bone Influences off, so that the file holds four weights per vertex.
6. Animation: on, with sampling of every frame on (Always Sample Animations). Blender exports
   the active action of the armature and the actions on its NLA tracks. Push each action you
   want as a clip down to an NLA track.

## Import a skeletal mesh

1. Open the packages that hold your materials, in the Texture browser.
2. Open the Animation browser.
3. Click File > Mesh import.
4. Select a `.glb` or `.gltf` file. A `.gltf` needs its `.bin` file beside it.
5. In the Import Skeletal Mesh dialog, enter a package name, a group name and a mesh name. Use a
   new package name for your own content. Use one group per character or prop. The default
   mesh name is the name of the file.
6. Set Scale, Clip group and Rate when the defaults do not fit. Scale multiplies the file's
   units on the way in. Clip group is the name the game uses to group the clips of a skeleton.
   The game's own weapons use `Default`. Rate applies only to a file with keys that are not
   evenly spaced. A file with one key per frame plays with one frame per key.
7. To import the mesh and the skeleton without clips, clear "Import the file's animations as
   clips".
8. Click OK.

The browser shows the new mesh in its bind pose. The clip list shows the file's animations.
Select one and press Play/pause.

9. Click Mesh > Mesh properties and expand Materials. Set each empty slot with the Use button
   and a material selected in the Texture browser.
10. Click File > Save and save the package into `Content\` in the SDK folder. The package holds
    the mesh, the animation package and the metadata records.

The log window lists what the importer read: the vertices, the triangles, the bones, the
sockets and the clips. It also lists every warning.

## Replace the animations

To bring new or changed animations to a mesh without importing the mesh again:

1. Select the mesh in the Animation browser.
2. Click File > Animation import.
3. Select a `.glb` or `.gltf` file exported from the same rig.
4. Set Scale, Clip group and Rate as for the mesh import. Click OK.

The file's animations replace every clip of the mesh's skeleton. The file's skeleton must have
the same bones in the same order as the mesh. When it does not, the editor shows a message that
names the first difference. The new clips then do not pose the mesh. Export the animations from
the rig the mesh came from.

File > Animation append belongs to the animation sets of the original Unreal Engine. For a
BioShock mesh it shows a message and does nothing. The clips of a skeleton come from one file.

## Place a skeletal mesh in a map

An actor that draws a skeletal mesh needs `DrawType` `DT_Mesh` and the mesh in its `Mesh`
property. Two kinds of actor use the clips:

- A `ReactiveAnimatedMesh` and its subclasses, such as `AnimatedContainer`, play clips from an
  authored sequence. Expand `ScriptedSequence` and add a set. Add a `ScriptedAnimation` to the
  set and set its `Animation` to a clip name. `RunNext` names the set that plays after this one.
  `-1` stops the sequence. Set `bPlayOnStartup` to start the sequence when the map loads. A
  script can also start it with `ActionControlScriptedSequence` (see
  [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)).
- Any actor with a mesh can hold a still pose in `StartingPose`. Set `AnimationName` to a clip
  name. With no `PoseName`, the pose is the first frame of the clip. See
  [19-Working-with-Retail-Maps.md](19-Working-with-Retail-Maps.md) for the poses of the game's
  corpses.

A script can also play a clip on any actor with a mesh by Label, with `ActionPlayAnimation`.

The map uses the package that holds the mesh. Save the package into `Content\` before you save
the map, and keep it there. The bake embeds what the map needs from it.

## Import at compile time

A script package can also import a skeletal mesh. The compiler then builds the mesh again from
its glTF file each time it compiles the package. See "Import a skeletal mesh" in
[34-Importing-Assets-with-UCC.md](34-Importing-Assets-with-UCC.md).

## Related documents

- [03-Interface-Tour.md](03-Interface-Tour.md)
- [10-Static-Meshes.md](10-Static-Meshes.md)
- [13-Materials-and-Textures.md](13-Materials-and-Textures.md)
- [22-Scripting-Action-Reference.md](22-Scripting-Action-Reference.md)
- [34-Importing-Assets-with-UCC.md](34-Importing-Assets-with-UCC.md)
- [36-Troubleshooting.md](36-Troubleshooting.md)
