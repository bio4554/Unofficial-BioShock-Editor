# Importing Assets with UCC

This document tells you how to import textures, static meshes and materials into a script
package at compile time with `#exec` directives, and how to fold a package saved from the
BioShock editor into a script package. Read it when you want assets that are built again
from their source files every time the package is compiled.

## Before you start

- You have compiled a package of your own with `ucc make`. See
  [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md).
- You know the in-editor imports. The Static Mesh browser imports meshes (see
  [10-Static-Meshes.md](10-Static-Meshes.md), "Import a custom static mesh") and the Texture
  browser imports textures (see [13-Materials-and-Textures.md](13-Materials-and-Textures.md),
  "Import a texture"). Those are the usual way. This document is the alternative.

## Compile-time import or editor import

| | Editor import | Compile-time import |
| --- | --- | --- |
| Where the asset lands | A package that you save from a browser as `Content\<Package>.pkg`. | Your script package, `System\<Package>.u`. |
| How it is repeated | By hand, in the dialog. | By `ucc make`, from the source files, every time the package is compiled. |
| Best for | A few assets, a quick test. | A set of assets that you change and rebuild often, or assets that belong with your classes. |

The two ways produce the same kinds of objects. A texture imported at compile time is the
same as a texture imported in the Texture browser.

## File layout

Put the source files in the package folder, next to `Classes\`. The names `Import\` and
`Textures\` are a convention; any folder works.

```
<SDK folder>\MyMod\
    Classes\MyAssets.uc
    Import\MyCube.ase
    Import\MyProp.glb
    Import\MyChar.glb
    Import\MyCube.props
    Import\MyShader.props
    Textures\MyTex.bmp
    Textures\MySpec.tga
```

Every `FILE=` and `PROPS=` path in a directive is relative to `System\`, the working directory
of `ucc make`. A file in your package folder is therefore `..\MyMod\Textures\MyTex.bmp`.

## The directive class

Directives live in a class file. A class that only holds directives is enough:

```
class MyAssets extends Object;

#exec TEXTURE IMPORT NAME=MyTex FILE=..\MyMod\Textures\MyTex.bmp PACKAGE=MyMod GROUP=Tex MIPS=1
```

- One directive per line. The line starts with `#exec`.
- `PACKAGE=` names your own package. The object is then saved inside `MyMod.u`.
- `GROUP=` names a group inside the package. The example makes `MyMod.Tex.MyTex`.
- The directives run when `ucc make` parses the class. A directive that succeeds prints
  nothing. A directive that fails prints a message with the file and the line, for example
  `MyAssets.uc(9) : ExecWarning, Failed factoring: NEW StaticMesh File="..\MyMod\Import\MyCube.ase" Name="MyCube" Package="MyMod.Meshes"`,
  and makes no object.
- Lines that start with `//` are comments.

Compile the package as described in [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md):
delete `System\MyMod.u`, then run `ucc make`. Open the package in the Texture browser or the
Static Mesh browser to check the result.

## Import a texture

```
#exec TEXTURE IMPORT NAME=<name> FILE=<path> PACKAGE=<Package> [GROUP=<group>] [options]
```

The importer reads bmp, pcx, tga and dds files. Use dds for a texture that is already
compressed with its mip levels. Use tga for a 32-bit image with an alpha channel.

| Option | Function |
| --- | --- |
| `MIPS=0` or `MIPS=1` | Builds the mip chain. Default `1`. Same as Generate MipMaps in the Import Texture dialog. |
| `ALPHA=1` | Keeps the alpha channel of a 32-bit image. Same as Alpha in the dialog. |
| `MASKED=1` | Marks the texture as a colour-key cutout (`bMasked`). Same as Masked in the dialog. |
| `ALPHATEXTURE=1` | Sets `bAlphaTexture`. |
| `DXT=1`, `DXT=3` or `DXT=5` | Compresses the texture on import. Use `DXT=1` for an opaque texture and `DXT=5` for a texture with an alpha channel. Without it, compress in the Texture browser afterwards. |
| `LODSET=<n>`, `NORMALLOD=<n>` | Sets the `LODSet` and `NormalLOD` properties. |
| `UCLAMPMODE=CLAMP` or `WRAP`, `VCLAMPMODE=CLAMP` or `WRAP` | Sets the clamp mode of each axis. |
| `NEXT=<texture>` | Sets `AnimNext` for a frame-flip animation. |
| `MODULATED=1`, `ADDITIVE=1` | Not used by the game's textures. Leave them out. |

Examples:

```
#exec TEXTURE IMPORT NAME=MyTex    FILE=..\MyMod\Textures\MyTex.bmp   PACKAGE=MyMod GROUP=Tex MIPS=1
#exec TEXTURE IMPORT NAME=MyDds    FILE=..\MyMod\Import\MyDds.dds     PACKAGE=MyMod GROUP=Tex MIPS=1
#exec TEXTURE IMPORT NAME=MySpec   FILE=..\MyMod\Textures\MySpec.tga  PACKAGE=MyMod GROUP=Tex MIPS=1 ALPHA=1 DXT=5
#exec TEXTURE IMPORT NAME=MyMasked FILE=..\MyMod\Textures\MyTex.bmp   PACKAGE=MyMod GROUP=Tex MIPS=0 MASKED=1 DXT=1
```

`MySpec` becomes a DXT5 texture with `bAlphaTexture` set. `MyMasked` becomes a DXT1 texture
without mipmaps.

**NOTE:** A texture imported this way keeps its pixels inside the package, like a texture
imported in the Texture browser. See "The bulk content" in
[13-Materials-and-Textures.md](13-Materials-and-Textures.md).

## Import a static mesh

An `.ase`, `.gltf` or `.glb` file goes through `NEW StaticMesh`:

```
#exec NEW StaticMesh File="..\MyMod\Import\MyCube.ase" Name="MyCube" Package="MyMod.Meshes"
#exec NEW StaticMesh File="..\MyMod\Import\MyProp.glb" Name="MyProp" Package="MyMod.Meshes" Scale=52.5
```

`Package=` takes the package and the group, separated by a dot. The example makes
`MyMod.Meshes.MyCube`. `Scale=` multiplies a glTF file's units on the way in; without it one
glTF unit is one Unreal unit. A `.gltf` reads its `.bin` from the same folder. See "glTF
files" in [10-Static-Meshes.md](10-Static-Meshes.md) for what a glTF import keeps.

A LightWave `.lwo` file goes through `STATICMESH IMPORT`:

```
#exec STATICMESH IMPORT FILE=..\MyMod\Import\MyCube.lwo NAME=MyCube PACKAGE=MyMod GROUP=Meshes COLLISION=1
```

`STATICMESH IMPORT` also takes `.ase`, `.gltf` and `.glb` files and hands them to the same
importer as `NEW StaticMesh` (`SCALE=<n>` sets the glTF unit scale there). Only `.lwo` files
need `STATICMESH IMPORT`.

The importer gives the mesh a placeholder material. Assign the real materials afterwards with
`NEWMATERIAL CLASS=StaticMesh` and a properties file. See the next section.

A static mesh imported this way is the same as a mesh imported in the Static Mesh browser.
An `.ase` or glTF file with named collision objects gets game collision from them; a file without gets a
box fitted to the mesh bounds. See "Collision in the game" in
[10-Static-Meshes.md](10-Static-Meshes.md).

## Import a skeletal mesh

A rigged `.glb` or `.gltf` file goes through `SKELETALMESH IMPORT`:

```
#exec SKELETALMESH IMPORT PACKAGE=MyMod.MyChar NAME=MyCharMesh FILE=..\MyMod\Import\MyChar.glb
#exec SKELETALMESH IMPORT PACKAGE=MyMod.MyChar NAME=MyCharMesh FILE=..\MyMod\Import\MyChar.glb SCALE=52.5 GROUP=Default RATE=30 CLIPS=1
```

`PACKAGE=` takes the package and the group, separated by a dot. The group holds the mesh,
its animation package and its metadata records. Use one group per character or prop. The
options are those of the import dialog. `SCALE=` multiplies the file's units. `GROUP=` is the
clip group (`Default` without it). `RATE=` samples a file with keys that are not evenly
spaced. `CLIPS=0` imports the mesh and the skeleton without clips. See "Prepare the model in
Blender" in [11-Skeletal-Meshes-and-Animation.md](11-Skeletal-Meshes-and-Animation.md) for
what the file must hold.

`HAVOK BUILD` rebuilds only the animation package of a group from a file. Use it for a mesh
that already exists in the package:

```
#exec HAVOK BUILD PACKAGE=MyMod.MyChar FILE=..\MyMod\Import\MyChar.glb
```

It takes the same `SCALE=`, `GROUP=` and `RATE=` options. The file's animations replace every
clip of the group's skeleton. The file's skeleton must have the mesh's bones in the same order.
The game's own packages use it. Their skeletal mesh bodies load from a carved package, and
`HAVOK BUILD` makes their animation packages from the `.glb` files the SDK Manager exported
(see [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md)).

The importer matches materials by name, as the Animation browser does. Set the slots it
does not find in the Animation browser afterwards and save the package from there.

## Create a material and set its properties

```
#exec NEWMATERIAL CLASS=<MaterialClass> PACKAGE=<Package.Group> NAME=<name> [PROPS=<file>]
```

`NEWMATERIAL` creates a material object of the given class: `Shader`, `Combiner`, `TexPanner`,
or another class from the material class tree in
[13-Materials-and-Textures.md](13-Materials-and-Textures.md). Without `PROPS=`, the object is
created with its defaults. With `PROPS=`, the properties in the file are applied to the object.

The properties file is a text file with one `Property=Value` line per property. The syntax is
the syntax of a `defaultproperties` block:

```
Diffuse=Texture'MyMod.Tex.MyTex'
Glossiness=50
SpecularBrightness=2
DiffuseColor=(B=255,G=245,R=210,A=255)
SpecularMask=(Material=Texture'MyMod.Tex.MySpec',Channel=2)
```

- An object reference is `Class'Package.Group.Name'`, for example `Texture'MyMod.Tex.MyTex'`.
- A struct value is a list in parentheses, for example `DiffuseColor=(B=255,G=245,R=210,A=255)`.
- A mask is a struct with a `Material` and a `Channel`, for example
  `SpecularMask=(Material=Texture'MyMod.Tex.MySpec',Channel=2)`.
- The property names are the names in the Properties window of the material. See "Shader
  properties" and "Mask properties" in
  [13-Materials-and-Textures.md](13-Materials-and-Textures.md).

Every object that a properties file names must already exist when the file is applied. Write
the directives in two groups: first create every object, with no `PROPS=`; then apply the
properties files in a second group of lines.

A complete example. The class file:

```
class MyAssets extends Object;

// First group: create every object.
#exec TEXTURE IMPORT NAME=MyTex  FILE=..\MyMod\Textures\MyTex.bmp  PACKAGE=MyMod GROUP=Tex MIPS=1 DXT=1
#exec TEXTURE IMPORT NAME=MySpec FILE=..\MyMod\Textures\MySpec.tga PACKAGE=MyMod GROUP=Tex MIPS=1 ALPHA=1 DXT=5
#exec NEWMATERIAL CLASS=Shader PACKAGE=MyMod.Tex NAME=MyShader
#exec NEW StaticMesh File="..\MyMod\Import\MyCube.ase" Name="MyCube" Package="MyMod.Meshes"

// Second group: apply the properties.
#exec NEWMATERIAL CLASS=Shader     PACKAGE=MyMod.Tex    NAME=MyShader PROPS=..\MyMod\Import\MyShader.props
#exec NEWMATERIAL CLASS=StaticMesh PACKAGE=MyMod.Meshes NAME=MyCube   PROPS=..\MyMod\Import\MyCube.props
```

`MyMod\Import\MyShader.props`:

```
Diffuse=Texture'MyMod.Tex.MyTex'
SpecularMask=(Material=Texture'MyMod.Tex.MySpec',Channel=2)
Glossiness=50
SpecularBrightness=2
```

`MyMod\Import\MyCube.props`, which sets the first material slot of the mesh:

```
Materials(0)=(EnableCollision=True,Material=Shader'MyMod.Tex.MyShader')
```

`Materials(0)` is slot 0 of the mesh's `Materials` array, the array that the Static Mesh browser
shows in its property pane. A mesh with several material sections has one entry per slot:
`Materials(0)`, `Materials(1)`, and so on.

After `ucc make`, the Texture browser shows `MyShader` in the `Tex` group of `MyMod`, and the
Static Mesh browser shows `MyCube` in the `Meshes` group with `MyShader` in its first slot.

## Fold an editor-saved package into your package

A package that you saved from a browser (see "Save a package" in
[10-Static-Meshes.md](10-Static-Meshes.md) and "Save your materials to a package" in
[13-Materials-and-Textures.md](13-Materials-and-Textures.md)) can be loaded into your script
package at compile time:

```
#exec OBJ LOAD FILE=..\Content\MyProps.pkg PACKAGE=MyMod
```

The objects of `MyProps.pkg` are then saved inside `MyMod.u`. Use it when you author assets in
the BioShock editor and want to ship them in one script package with your classes.

## Related documents

- [10-Static-Meshes.md](10-Static-Meshes.md)
- [13-Materials-and-Textures.md](13-Materials-and-Textures.md)
- [36-Troubleshooting.md](36-Troubleshooting.md)
- [33-UCC-and-Script-Packages.md](33-UCC-and-Script-Packages.md)
- [35-UCC-Commandlet-Reference.md](35-UCC-Commandlet-Reference.md)
