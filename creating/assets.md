# Adding graphics and sound to your mod

!> This documentation page is a work in progress. If you find an empty section, it means that this part is still being written, sorry! If you can't find the information you're looking for and can't figure it our from example code, do let us know on the **#modding** channel on the game's [Discord server](https://discord.gg/qD97EDu).

Adding new graphics or sound to the game is done by creating an **asset bundle** and including it with your mod. To do this, you use the [asset-tool](https://github.com/slipways-game/asset-tool) made available on GitHub.

## Starting a new asset bundle

* Download a fresh copy of asset-tool whenever you need to add assets to a mod. You can download it from here by pressing the green "Code" button and selecting "Download ZIP".
* Extract the archive somewhere. Change the name of the folder if you want, so that you know which copy goes with which mod.
* Open the project in Unity. It needs to be specifically **Unity 2020.3 LTS**, and you **need both Windows and Mac build support installed** so that asset bundles can be exported properly.
* Place your custom assets in `Assets/Bundle/Contents`. Each asset type has its own section below. The asset tool ships with examples of each asset type, so you can copy from those for your own things.
* Once your assets are ready, export your bundle into the folder containing your mod. At the top of the screen, there is an extra menu called "Slipways". Select "Export bundle..." from there. Point the export into the directory containing your mod. You can choose any name for the asset file, but this is usually "assets".
* You should see two new files in your mod, `[bundlename].win` and `[bundlename].mac`. You need to reference these files from your `mod.pkg.py` so that the assets are loaded with your mod. Always reference both of these files - they contain versions of your assets for different platforms supported by Slipways.

## How Slipways looks for assets

Slipways will look through your asset bundle for files whose names start with a specific prefix. Here is a list of those magic prefixes, along with the type of assets

* `icons_<something>` - spritesheets with multiple new icons to add to the game
* `text_icons_<something>` - a special type of icon set that can be used in ingame text
* `structure_<something>` - 3D models for existing or newly-added structures
* `planet_<something>` - new 3D materials that can be used for custom planet types
* `sound_<something>` - sounds that your mod's scripts can play
* `race_<something>` - a special asset pack for a new race, including a portrait, colors, a speech sound, and other data

These files need to be in `Assets`, inside the `Bundle\Contents` subfolder. You can verify that your assets were detected correctly in the "Export bundle..." window.

You should also include other Unity objects that your assets depend on in the `Bundle\Contents`. For example, when creating a new structure 3D model, you will probably want to include new materials and animation clips that you structure prefab uses. The names you give these files don't matter, but they do need to be in the right folder so that the bundle can be generated correctly.

## Asset types

### Icons

Icons are delivered as a Unity texture with the name `icons_<something>`, containing a spritesheet with multiple icons. When importing this texture, you should use "Sprite Mode: Multiple", and then use the "Sprite Editor" button in Unity's inspector to cut the spritesheet up into individual icons. When cutting the spritesheet, make sure you give **correct names** to each individual sprite you define. These names are what matters the most, and what will let Slipways find your icons later on.

Some icon names will be picked up by the game automatically:
* `structure_[structure id]` icons will be used in the "build" menu for structures
* `tech_[tech id]` and `perk_[perk id]` will be used automatically for the given tech or perk

Other icons are specified manually, and can be used for example to illustrate projects or global conditions. You can use icons like these by refering to the name you gave to the specific icon in the Unity Sprite Editor from a spreadsheet or a script.

?> Example: The "example-assets" example mod shows how to add icons (for the new "frobnicator" structure). The icon asset used there is the one shipped with asset-tool in `Bundle/Contents/Structures`.

### Text icons

Text icons are a little special - these are icons that can be used in ingame text. The most important situation in which you need to use those is when adding **new resources** to the game, which require this type of icon to work.

Text icons are delivered as a [TextMeshPro Sprite Asset](http://digitalnativestudios.com/textmeshpro/docs/sprites/). You don't really have to know what that means or how those work under the hood &ndash; you can base your asset on the example one included with the asset-tool.

To define your icons this way
* create a spritesheet with each of your icons taking up 26x26 pixels
* import it into the project as a texture
* go to your TMP Sprite Asset, and link the texture in the "Sprite Atlas" property using the Inspector
* again using the Inspector, define entries for each of your icons in the "Sprite Glyph Table" - you should mostly only have to use the X, Y, W, H, properties to pick the right part of the spritesheet
* then, define entries in the "Sprite Character Table" to refer to the glyphs, paying special attention to the name:
    * if this icon is to be used for a resource, the name has to be `res_[resource id]`, eg. "res_coal" for a resource called "Coal"
    * if this icon will be just used in your text strings directly, you can call it whatever - you will be able to use it by typing `:name:` in your ingame strings

Text icon filenames have to start with `text_icons_`. The name itself doesn't matter for the game, only the names of the character in the Sprite Character Table.

?> Example: The "example-assets" example mod shows how to add resource icons. The asset used there is the one shipped with asset-tool in `Bundle/Contents/TextIcons`.

### Structures

A new structure model is delivered as a Unity prefab, with a name following the pattern `structure_[structure id]`. To work properly, such a prefab should follow a few guidelines:

* It should include the `SWStructureVisual` component, which defines the size of your structure model for the game (Object Radius), how far away from the structure slipways connecting to it end (Slipway Radius), and whether the structure uses gate meshes or not (Needs Gates).
* It should only use basic Unity Universal Render Pipeline shaders, since those are included with Slipways. For safety, you should probably stick with the basic "Lit" shader for most objects, which is customizable enough to get things looking pretty good.

If you want your structure view to change dynamically based on the structure's state, you should also include an `SWDynamicView` component. From there, you can use either one or both of these options:

* Add an Animator to your object, create some Unity animations and link the Animator component to the SWDynamicView. The game will automatically set a property called "level" on the animator, corresponding to the industry level of the structure at all times. You can trigger the transitions in your animations off of that. Any [custom data](creating/code.md#custom-data) property starting with `anim_` will also be set on the animator.
* Write a custom Python class and animate your structure from code. The name of the class should be added in the "Python Class" property of the SWDynamicView, and anything the script will need to access should be added to the "References" list.

?> Example: The "example-assets" example mod has the "frobnicator" structure, which shows how to use custom scripts for your views. The asset used there is the one shipped with asset-tool in `Bundle/Contents/Structures`, and you can see an example animation defined in there as well.

### Planet types

These are delivered as a custom Unity material, with the name `planet_[planet type id]`. You should base this material on the "Universal Render Pipeline/Lit" shader, but otherwise you can use all its features. The textures you use should work with a standard UV-mapped sphere, and should usually by in 2:1 aspect to prevent stretching.

?> Example: The "example-assets" mod has a new planet type defined with its own custom look. The asset-tool ships with an example material and textures in `Bundle/Contents/Planet`.

### Custom sounds

These are just imported as standard Unity AudioClips, with the name `sound_[sound id]`. Later on, you can use these sounds in scripts by calling:

```py
SoundCue.Custom("your_sounds_id").Play()
```

?> Example: The "example-assets" example mod has the "frobnicator" structure, which plays a custom sound when the structure levels up.

### Council race assets

These are delivered as a prefab, with a filename `race_[race id]`. The format of the prefab is quite involved - the easiest way would be to start from the example shipping with asset-tool and replace the three needed images in the two provided prefabs. You can also edit the colors and the speech file specified in the prefab.

?> Example: The "example-race" example mod has a new race called the "Stickmen", which shows how to add a new race to the game.
