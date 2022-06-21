# Contents of a mod

Every mod has to be described by a `mod.pkg.py` file. The most important purpose of this file is to list the contents of the mod: files to be loaded whenever the mod is enabled. These go into a `contents` list, which usually looks something like this:

```py
contents = [
    "new-race.xls", # a spreadsheet that defines new game objects

    "new-race.py", # a Python script that defines new rules for those objects

    "text-english.xls" / lang("english"), # english text for the mod

    "assets.win", # Windows version of the graphics/sound asset bundle
    "assets.mac", # Mac version of the graphics/sound asset bundle
]
```

As you can see, the files listed there come in a few different flavors. Each of these flavors gets its own chapter later, but here is a quick summary of what they are for and how they are used.

## `.xls` files with gameplay data :id=xls-files

These files will contain multiple individual sheets with special names, like "Resources" or "Technologies". Each sheets deals with a different type of object in the game. Each row in such a sheet specifies the properties of one object of that type, eg. a resource or a technology.

The easiest way to create those spreadsheets is to start from the core files. 

* Get the `game.xls` spreadsheet from the [core-files](https://github.com/slipways-game/core-files). This contains all the objects defined by the core game.
* Copy the sheet you're interested in to a new spreadsheet. Eg. if you want to add a new structures, copy the "Structures" sheet.
* Delete all the rows, keeping the black "header" row at the top intact.
* Add one row for each new thing of that type that you want to add to the game.

Creating each specific object type is described in the next chapter, *[Making the spreadsheets](creating/spreadsheets.md)*.

## `.py` files with gameplay code :id=py-files

For many object types, just specifying a new row in a spreadsheet will not be enough - you will also have to write some custom code to describe the rules governing eg. your own structure.

These rules will be stored inside `.py` files that you will need to add to your contents list to ensure they are loaded with your mod. You can keep everything in one .py file, or have as many of those as you want. They will be loaded in the order you specify them.

Writing the code gets its own chapter later, predictably named *[Writing the code](creating/code.md)*.

## `text-<language>.xls` localization files :id=localization-files

All text in Slipways supports localization to various languages. This includes text used in your mod.  The spreadsheet itself is very simple - each sheet has two columns, "ID" and "Text". "ID" is the technical identifier for the localized string. "Text" is the text of that string in a given language and can include some special tags to eg. add resource icons or provide spaces to be filled out later by the game's code.

These spreadsheets have to be **tagged** with the language they are for. It looks like this:

```py
contents = [
    # ...
    "text-english.xls" / lang("english"),
    # ...
]
```

The `/ lang("english")` is a language tag, making sure that this file is only loaded if English is the preferred language. You should always provide at least the English version of your mod's text. Other versions are optional, and the game will fall back to the English version if it can't find text in the user's preferred language.

Read more about this in the *[Preparing the text](creating/text.md)* chapter.

## `.win` and `.mac` files with graphics/sound assets :id=asset-files

These files are created using the Unity-based Slipways [asset-tool](https://github.com/slipways-game/asset-tool). You will need to do this to add custom icons, 3d models and sounds for your additions to the game. 

The asset tool will automatically export two versions of your asset bundle, one with a `.win` extension and one with a `.mac` extension. You should include both files in your `contents` list. The game will automatically load the right version of the assets depending on the platform it is running on.

Read more about this in the *[Adding graphics and sound](creating/assets.md)* chapter.

## Paths in mod.pkg.py

All paths in this file are relative to where your mod.pkg.py file is located, by default. If you want to organize your things in subfolders below that, you should only use forward slashes in your paths to make sure the game understands your paths correctly, eg. `code/my-structures.py`.

?> You can also refer to files outside your mod by starting the path with a `/`, eg. `/modes/campaign-diaspora/campaigns.py` will pull that script from the game's core files. This is rarely needed, and when it is, you can usually rely on the example mods having the right "recipe" for which core files need to be pulled in.
