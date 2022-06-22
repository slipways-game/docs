# Making the spreadsheets

!> This documentation page is a work in progress. If you find an empty section, it means that this part is still being written, sorry! If you can't find the information you're looking for and can't figure it our from example code, do let us know on the **#modding** channel on the game's [Discord server](https://discord.gg/qD97EDu).

Almost all of your additions to the game will start as a new row in a special spreadsheet that the game uses to create the game world. The core game is defined mostly in a spreadsheet called `game.xls`, and you can see that spreadsheet in the [core-files](https://github.com/slipways-game/core-files).

The simplest way to make your own additions to the game is to copy parts of `game.xls` to a spreadsheet in your own mod, like this:

* Open `game.xls` and find the sheet your interested in.
* Open your mod's spreadsheets (if you're starting from the blank template, it's called `additions.xls`).
* Copy the entire sheet from `game.xls` to your spreadsheet. In LibreOffice, you right-click the sheet, select "Move or Copy Sheet..." and you can directly copy it between documents.
* Delete all the rows you got from `game.xls` - you don't want to redefine them again in your mod.
* You can keep some rows from 'game.xls' if your intention *is* to override the core definitions.
* Add new rows - for each new thing of that type that you want to add to the game.

## Common column types

### Costs

Whenever you are allowed to specify a cost in your spreadsheet, the syntax is always the same. Different parts of the cost are separated by commas, and each part can have one of the following forms:

* `5$`, `20$` - These are **resource costs**. The resource is usually `$`, which just means money, but you can also use `5S` to specify science costs, for example. 
* `3mo` - These are **time costs**, controlling how much time the associated action takes (building the structure, colonizing with that industry, building that project, etc.)
* `2$/t` - These are **upkeep costs**, applied each turn. Only valid as part of industries.

### Rules

These are used to specify custom rulesets for an object, scripted in Python. Details on how to create those are in the [code chapter](creating/code.md).



## Tricks that work with any sheet

Instead of overriding a row from the core files, you can completely remove it by prefixing the ID with `!`. For example, if you put `!quantum_computing` in the ID column of your `Technologies` sheet, it will remove the Quantum Computing tech from the game whenever your mod is loaded.

You can also temporarily "comment out" rows by prefixing the ID with `//`. Any rows starting like this are ignored.

The game also ignores any rows that have an empty ID, and any columns that it doesn't recognize the names for. You can use this to write comments to yourself in the spreadsheet or organize things using divider rows.



## Industries

The node's industry is what actually controls the needs and products, how the node upgrades from level and many, many other aspects of how the node works. Here is a basic outline of what goes into each column:

* **ID** Just pick any ID you like. We usually stick to lowercase letters, `connected_by_underscores` if necessary.
* **Host** Controls the default rules that will apply to an industry. You should specify the one that matches how you intend the industry to be used: if you're going to use this industry as part of a structure, specify `structure`. If this will be a colonization option for a planet, use `planet`.
* **Planet Types** Controls which planet types have this industry as a colonization option from the start of the scenario. These are planet type IDs, separated by commas if you're specifying multiple. If this industry is part of a structure or enabled by a technology, leave this empty.
* **Level 0 - Level 3** These are the needs and products at every given level of advancement of this industry. Nodes start at level 0 and advance according to the upgrade rules. The syntax for this part is explained below.
* **Costs** How much this industry costs to establish, and optionally its yearly upkeep. Syntax described in its own section above. For structure industries, you should probably only use the upkeep part, since structure industries are automatically established when the structure is built.
* **Price Multiplier** How much money a node with this industry pays when a resource is delivered to it. Usually just `1` or `0`, with `0` being used for structures that you don't want to be profitable when resources are delivered to them (eg. labs in the core game)
* **Rules** This is a *ruleset* with all the special rules for this industry. These are explained in the [code chapter](creating/code.md). You can leave them empty for now to stick to the default rules for planet/structure industries.
* **Qualities** What qualities are automatically attached to the node when this industry is established on it. Qualities are also explained in more detail in the [code chapter](creating/code.md).
* **Description string** Used to override the *localized string* used as description of this industry. You don't need to worry about it unless you want to have multiple industries share the same description in-game.

?> Examples: The 'assets' example mod has an example of how to define new industries, both for a planet type and for a structure.

### Industry levels

Each industry level is specified as a list of needs the node will have on that level, followed by an arrow (`→`), followed by a list of products. Both needs and products are specified as IDs of **resources**. If you don't know which ID corresponds to which resource, you can always check in the [core-files](https://github.com/slipways-game/core-files), specifically in the "Resources" sheet of the `game.xls` spreadsheet.

You can use numbers to specify multiple units in the product part, eg. `FG→4P` is a planet that needs food and goods, and provides 4 units of people in return.

Products in parentheses mean that the products aren't available yet at this level. These will be displayed with a "red lock" icon in-game. For example, `T→(E)` means that this node will produce energy at some higher level, but doesn't yet (probably until the `T` (chips) is delivered).

## Structures

Structures are most commonly used for one of two things:

* things that the player can build wherever they choose on the map
* things that the game places on the map during map generation

Regardless of the purpose, they are defined in the spreadsheet in the same way:

* **ID** Just pick any ID you like. We usually stick to lowercase letters, `connected_by_underscores` if necessary.
* **Industry** The ID of the *industry* that will automatically be established on the structure when it is built/put on the map. This industry is what will control most of the structure's behavior once it's there.
* **Costs** How much this structure costs to build. Syntax for costs is described in its own section above. If you want the structure to have an upkeep, this should be part of the industry instead - here, specify only the one time costs to build the thing. If this is a structure automatically put on the map by your mod, you can keep the costs empty.
* **Obstruction radius** How wide of a radius around the structure is excluded from having connections or anything else in it. Custom rules let you create fancier obstruction shapes than a circle, for structures that need it.
* **Rules** The custom rules for this structure, mostly dealing with how the structure behaves before being placed (later concerns are handles by the industry's ruleset). Details on creating these rulesets are explained in the [code chapter](creating/code.md).

?> Examples: The 'assets' example mod has an example of how to define a new structure.

## Actions

Actions are things that you can do to a node before it is colonized and has an industry. They are represented in-game as small square buttons below the colonization options. Most of the logic of how they work will live in Python scripts - the spreadsheet part just defines the metadata.

* **ID** Just pick any ID you like. We usually stick to lowercase letters, `connected_by_underscores` if necessary.
* **Icon** The ID of the icon to display on the button. You should include this icon in a [custom asset bundle](creating/assets.md) attached to your mod.
* **Order** Action buttons are sorted according to this number, the standard values are usually between `0` and `100`. 
* **Groups** Only matters if this action belongs to a group that will be affected by a technology. For example, a technology can have a `reduce(action.terraforming.cost$,20%)` to make all actions in the `terraforming` group cheaper. Usually left empty.
* **Rules** The rules that define what happens if this action is performed. Details on creating these rulesets are explained in the [code chapter](creating/code.md).
* **Costs** How much this action costs and how long it takes. Syntax for costs is described in its own section above.

## Projects

This sheet is where you define the **planetary projects** that can be built on planets.

* **ID** Just pick any ID you like. We usually stick to lowercase letters, `connected_by_underscores` if necessary.
* **Icon** The ID of the icon to display on the button. You should include this icon in a [custom asset bundle](creating/assets.md) attached to your mod.
* **Qualities** The Python expression describing the qualities that are attached to the planet when the project is built. The qualities are what actually make the project do something. If left empty, the quality will be assumed to be `IdOfTheProjectQuality()`. You can find details on writing qualities in the [code chapter](creating/code.md).
* **Costs** How much this project costs and how long it takes to build. Syntax for costs is described in its own section above.
* **Req. Level** The minimum level the planet has to achieve before this project is available. `0` is struggling, `3` is prosperous.

## Default Unlocks

This is a simple section where you list which of the structures, industries, action, projects etc. in your mod should be accessible to the player from the start of the scenario. Others can be added by technologies and perks, but the ones listed here are always available.

* **ID** What to unlock, eg. `structure.something`, `project.something`, `industry.something` etc.
* **Context** Where it should be unlocked, which is relevant for eg. actions or industries. Should be `planet.[planet_type]` or `structure.[structure_type]`. If empty, the object is unlocked "everywhere".

?> Examples: The 'assets' example mod has an example structure which is unlocked this way.

## Constants

These are simple values that influence various numeric parameters in the game. The special thing about naming those is that [technologies and perks](#techs-perks) can later increase/reduce these numbers, and they can also be overriden in eg. campaign missions, or indeed in your mods.

Most of the ones in `game.xls` will include a description. To override the base value completely, just copy a row from `game.xls` to your sheet and change the value. Otherwise, you can use **effects** to tweak the value up and down.

## Technologies 

The "Technologies" sheet is used for all the techs that you want the player to be able to invent in-game.

* **ID** Just pick any ID you like. We usually stick to lowercase letters, `connected_by_underscores` if necessary.
* **Races** The IDs of the races that have this tech in their pool. Comma-separated.
* **BVADS columns** These are just here to visualize which races have access. They aren't required or used by the game engine.
* **Level** Controls both the tier the technology is encountered in (A-E) and its cost. The number is the relative cost with the tier, usually `0-10`, but can go above and below if necessary.
* **Leads To** The IDs of any upgrade technologies that become available once this one is invented. The upgrade tech should have their "Races" column empty - they are unlocked through the upgrade process regardless of races.
* **Implemented** Put an "X" in this column. It had other uses in the past, but now it's just a quick toggle to enable/disable techs.

The **Effects** column is a list of what the tech does once invented, one effect per line. These effects get their own description below.

### Tech effects

<div class="definition">

**increase()/reduce()** 
Change the value of a named property in the game. The game supports adjusting any named constant in the spreadsheet, for example `increase(asteroid.bonus, 50%)` will increase how much money you get for asteroids by 50%. You can also change cost this way: `reduce(project.cost.$, 5)` will reduce the cost of all projects by 5$.

</div>
<div class="definition">

**unlock()**
Unlocks something, like an industry or a structure. `unlock(structure.frobnicator)` will unlock that structure for building. Industries take an additional parameter specifying where the industry will become available, eg. `unlock(industry.lava_geo, planet.lava/mining/ice)` to unlock this industry on three types of planets.

</div>
<div class="definition">

**project()**
Enables a project with a specific ID being built, eg. `project(culture_hub)`.

</div>
<div class="definition">

**quality()**
Enables a global quality, specified by a given Python expression, eg. `quality(GeneticAdjustments())`. This type of quality should have an `applies()` method to control which nodes 
it affects. You can find more info on how to create qualities in the [code chapter](creating/code.md).

</div>
<div class="definition">

**cond()**
Similarly, this enables a global condition, again specified as a Python expression, eg. `cond(EmpathicLinksTradeRoutes())`. You can find more info on how to create conditions in the [code chapter](creating/code.md).

</div>
<div class="definition">

**supersede()**
Used in upgrade techs sometimes, specifying their parent tech, eg. `supersede(gravitic_mining)`. Indicates that the new technology should completely override the effects of its parent tech, instead of being cumulative.

</div>
<div class="definition">

**explain()**
Include an explanation of something in the tech description, eg. `explain(resource.E)` to attach a description of how energy works. Usually used for resources, since project/structure/industry explanations are included automatically.

</div>
<div class="definition">

**description()**
The description of a technology is usually auto-generated from its effects. You can use this special effect to replace the whole description with a localized string of a specified ID. You can also provide arguments to fill placeholders in this string, eg. `description(tech.frobulation.desc, 12, 25%)`.

</div>

## Perks

Perks are very similar to technologies - they use the same type of effect mini-language to specify how they work, and they're attached to races. The only difference is in when and how they are applied.

* **ID** Just pick any ID you like. We usually stick to lowercase letters, `connected_by_underscores` if necessary.
* **Race** The IDs of the race this perk belongs to.
* **Effects** Controls both the tier the technology is encountered in (A-E) and its cost. The number is the relative cost with the tier, usually `0-10`, but can go above and below if necessary.
* **Short Text** Replaces the perk icon if it's missing. You should probably ignore this column and instead create an icon called `perk_[your_perk_id]` in an [asset bundle](creating/assets.md).

## Resources

?> Examples: The 'assets' example mod has a new resource type.

## Planet types

?> Examples: The 'assets' example mod has a new planet type.

## Races

?> Examples: The 'New council race' example mod shows how this is done.

## Quests
