# Creating a mod

!> Please note that this documentation is a work in progress and is still being improved and extended. If you can't find the answer here, please refer to the [example mods](#getting-the-examples) or [get help on Discord](#getting-help).

## Getting the tools

Before we start, we need to get the tools that we're going to work with.

* Slipways stores a lot of its game data in **Excel spreadsheets**, so you'll need an app that allows to edit those. I'm using [LibreOffice](https://www.libreoffice.org/download/download/) which is free, but you can use Microsoft's Office or anything else that opens `.xls` files.
* The scripts that control gameplay logic are written in **Python 2**. To code new stuff, you'll need to know Python already or be willing to learn. You'll also need a reliable text editor to write your code. We're using [Visual Studio Code](https://code.visualstudio.com/), but you can use any good code editor you're familiar with.
* If you want to feature custom graphics or sound in your mods, you will also need **Unity 3D**. Please follow the below instructions exactly:
  * Get the **specific version of Unity** that Slipways was built with. This is currently **Unity 2020.3.35f1**, which you can get from the [Unity download archive](https://unity3d.com/get-unity/download/archive). Only using this specific version will ensure your assets are fully compatible with the game.
  * When installing Unity, make sure to **enable "Mac Build Support (Mono)" and "Windows Build Support (IL2CPP)"**. You need both of these regardless of the operating system you're using yourself &ndash; the Slipways asset tool creates two variants of your assets to ensure compatibility with all versions of the game.

## Getting the examples

This documentation attempts to cover the basics, but it will never be fully exhaustive. Thankfully, there are examples available that you can build on when making your mods. Most of those are hosted on the **slipways-game** Github.

* [example-mods](https://github.com/slipways-game/example-mods) &nbsp; These are fully working mods that offer simple examples on how to make changes to the game. You can subscribe to them in [Steam Workshop](https://steamcommunity.com/app/1264280/workshop/) and let the game download them, or fetch them from the [Github repository](https://github.com/slipways-game/example-mods).
* [core-files](https://github.com/slipways-game/core-files) &nbsp; This is an extract from the game's **core scripts**. Everything that you can find in the core game: the technologies, races, perks, campaign missions, etc. is derived from these files. If you want to do something, chances are you can find something in the core game that does something similar, and adapt the spreadsheets/code from the core files.

## Getting help

We're trying our best with the documentation and the examples, but the Slipways codebase is quite big, and the possibilities with the modding tools will always be greater than what we can describe and explain here. If you're having any trouble, you can to the game's [Discord server](https://discord.gg/qD97EDu) and ask stuff in the **#modding** channel. Chances are one of the other modders will be able to help you. If not, Jakub (the game's developer) will also be checking this channel from time to time, so you might be able to get answers straight from the horse's mouth.

## Developer mode

While working on your mods, you should enable **Developer mode**. This is listed as one of the mods in the mod management window. When enabled, you will have access to additional debugging tools that will make it easier to work on your mod. You can read more about it [here](creating/devmode.md).

## Starting a new mod

* Subscribe to "Template: Blank mod" on Steam Workshop or download it from the [example-mods](https://github.com/slipways-game/example-mods) repository
  * If you just subscribed, remember to **"Refresh"** in the mod management interface so that the template actually downloads
* Find the template in the **mod folder**. The path to this folder should be visible at the bottom of the mod management window.
* Copy the template to a new subfolder in the mod folder, eg. `C:\Users\<your username>\AppData\LocalLow\Beetlewing\Slipways\mods\<your-mod-name>` - this folder will house your mod
* Go into your new folder and delete any files starting with a dot (`.`), like `.pid`, `.sid`, etc.
* Edit the `mod.pkg.py` file inside the folder to change the name and description of your mod.

After refreshing the mod management window in game, you should already see your nascent mod listed. You can enable it - it won't do anything yet, since this is a blank template that makes no actual gameplay changes.

?> If you prefer, you can use the same process to start from one of the **example mods** - this might make things easier if the changes you're planning are similar to what the examples are doing.
