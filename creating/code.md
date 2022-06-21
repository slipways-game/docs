# Writing the code

!> This documentation page is a work in progress. If you find an empty section, it means that this part is still being written, sorry! If you can't find the information you're looking for and can't figure it our from example code, do let us know on the **#modding** channel on the game's [Discord server](https://discord.gg/qD97EDu).

This chapter describes how to write your custom code for your mod. It will not teach you Python - if you're not already familiar, there's tons of existing material and tutorials available around the web for that. What it will teach you is how the Slipways engine works and how to plug into it just the right way to get everything working.

The first part of this chapter covers general ideas the engine is built on. The second chapter focuses on how to write code for the specific additions to the game you'd like to make. You can read them all the way through, but here is what I would suggest:

* figure out what you'd like to add to the game in your mod, eg. a new structure
* read the chapter dedicated to that type of thing in the [second part of this chapter](#coding-specific-things) ("Coding specific things")
* find an example from the core game or example mods that does something similar to what you want
* copy code from that example and adapt it to your needs
* when something is unclear in how things are implemented, look for relevant information in the [first part of this chapter](#general-concepts) ("General concepts")

Either way, these docs can only provide a basic overview of how things work and will refer to examples a lot. Before you start digging in, consider downloading both the [core-files](https://github.com/slipways-game/core-files) and the [example-mods](https://github.com/slipways-game/example-mods) so you can look things up.

## General concepts

### Python scripts in Slipways

When your mod gets loaded, all of the scripts specified in its `mod.pkg.py` will be loaded along with it. Scripts in Slipways are loaded into one common namespace, in the order that they are specified in the package files. You can keep all the code for your mod in one `.py` file, or subdivide them however you want. Whichever approach you choose, there is no need (or indeed, no option) to use the Python `import` statement - everything lands in a single namespace, so things defined in one script file are automatically available in the others. If you rely on this, make sure you order the scripts in your `.pkg.py` correctly.

### Rulesets: custom logic for objects

Many of the game's objects are defined in spreadsheets. Building these spreadsheets gets its own [chapter](spreadsheets.md), but there is only so much you can write inside a spreadsheet. Sooner or later, you will want to add some custom logic to these things, and when you do, you'll need to specify a **ruleset** inside your spreadsheet.

Rulesets are defined by a single cell in the spreadsheet. The contents of this cell always refer to code provided by your Python scripts. The simplest way is to specify rules one at a time as Python functions. Here's an example from a structure definition:

```
placement: extractor_placement
can_be_obstructed_by: extractor_obstructed_by
when_placed: extractor_placed
```

On the left side of the colon, we specify the **name of the rule** we'd like to override for this object. Each object type has a different set of rules available, and these are described in the [second part of this chapter](#coding-specific-things). On the right side, you name the corresponding **function defined in your script** that implements your logic for that rule. This will look something like this:

```py
def extractor_placement():
    # ... code
def extractor_obstructed_by(planned_structure, obstruction):
    # ... code
def extractor_placed(built_structure):
    # ... code
```

Each type of rule provides you with some inputs, and will expect specific information as output. These are described in the sections about specific object types in the second part of this chapter.

?> Examples: In core-files, you'll find many such rulesets in the "Structures" and "Industries" sheets. You can find for the corresponding rules by searching in core-files for the names of specific functions. You will also find ruleset in many example mods, like eg. "example-assets".

#### Rulesets in classes

If you prefer, you can group all rules for an object in a class for better organization. This is done by using a special `from:` line in your spreadsheet:

```
from: ExtractorRules()
```

This allows you to define all the rules together in a class, like this:

```py
class ExtractorRules:
    def extractor_placement(self):
        # ...
    def extractor_obstructed_by(self, planned_structure, obstruction):
        # ...
```

Note that we had to add the `self` parameter, since our functions are now methods, and part of a class.

### Kinds: Accessing stuff from spreadsheets

Just like spreadsheets need a way to access things in your scripts through rulesets, your scripts sometimes want to refer to objects defined in the spreadsheets. Each object type gets a special class that is used for such access, but they all work in a similar manner:

```py
Resource.All["B"] # gets the resource with the identifier 'B' (bots)
StructureKind.All["relay"] # gets the structure with the ID 'relay'
ProjectKind.All["megastructures"]
IndustryKind.All["lab"]
TechKind.All["quantum_computing"]
PerkKind.All["reciprocity"]
PlanetKind.All["ocean"]
TechKind.AllList # gets the list of all defined technologies
```

Many methods in the game engine will either deliver you an object of one of these types, or expect to receive an object of one of these types.

### Nodes and the structure of the game

### Qualities: local effects on nodes

#### Local effect types

### Global conditions: reacting to things happening

.. what it is..

.. how to write one..

#### Activating conditions

Global conditions only work when **active**. Activating a global condition is usually done in one of three ways.

The first way is by specifying it in the `starting_conditions` list in your `.pkg.py`. These are specified as string that include the condition name and any parameters for the constructors, eg. `"MakeStructuresMoreExpensiveBy(10)"`. This type of condition is used for things that are always in force when your package is active, or conditions that have to be already active during scenario setup or map generation.

The second way is by specifying it in a `cond()` effect for a perk or technology in your spreadsheet. This type of condition implements the new rules that come in effect when the technology is invented or perk is picked.

Finally, conditions can also be activated manually from scripts, eg. `conditions.Activate("MakeThingsHarder()")`. These can be later deactivated like this:

```py
conditions.Get("MakeThingsHarder()").Deactivate()
```

### Triggers: what you can react to

### Commands and consequences: supporting undo

Making changes to the Slipways gameworld is complicated a little bit by the game supporting undo. This means that whenever we make changes to the gameworld, we have to think about how those changes can later be **reverted**.

For this reason, when we make changes from the scripts, we usually use either a **command** or a **consequence**. These are very similar: in essence, they encapsulate a single change to the gameworld, together with a way to revert it. **Commands** are defined and made available by the game engine itself, while **consequences** are defined as part of the Python scripts.

To issue a command or consequence, you use the `commands` global object like this:

```py
# invent the "Slipstream Relay" tech immediately using a command
commands.Issue(GrantTech(world, TechKind.All["slipstream_relay"]))
# give 5 cash immediately using a scripted consequence
commands.IssueScriptedConsequence(ConsGrantResources(5, Resource.Cash)) 
```

?> Examples: Look for `commands.Issue` and `commands.IssueScriptedConsequence` in **core-files** to see many examples of how to use both commands and consequences. `consequences.py` defines many generally useful consequence classes.

To create your own reversible consequences, you just need to define a class with `apply()` and `revert()` methods. Here is a simple example:

```py
class ConsMarkVisited:
    def __init__(self, node): self._node = node
    def apply(self):
        self._node.CustomData.Set("visited", True)
    def revert(self):
        self._node.CustomData.Clear("visited")
        self._node.TriggerChange()
```

If you want to make something irreversible, simply omit the `revert()` method. Be careful with that, though - this will disable undo whenever your consequence happens in the game, so you should probably do that only if there is no way to revert, or if you actually want to do that since hidden information was revealed to the player.

### Custom data: making sure things are saved :id=custom-data

Another annoying aspect is that the properties and data you store in your various Python objects (like conditions or qualities) will not get stored when the game is saved. There is, however, a way to stash data in a way that will be stored inside a savefile and correctly restored when it is loaded: so called **custom data**.

Any object in the slipways game can have custom data associated with. Here's an example of how to store and access such data:
```py
node.CustomData.Set('foo', 42) # sets the 'in_danger' flag on a node to True
node.CustomData.GetOr('foo', 0) # gets the data stored in a node, or a default value if no data by that name is stored (here: False)
node.CustomData.Get('foo') # gets the data, raises an error if it is missing
node.CustomData.Clear('some_data') # removes the custom data
node.CustomData.Inc('counter') # increases a counter
node.CustomData.Dec('counter') # decreases a counter
game.CustomData.Set('endangered_nodes', [...]) # stores a list of nodes in the central 'game' object
```

The usual places to store custom data are:
* on the object the data pertains to, for example a *node* or a *connection*
* on the `game` object if the data is more of a "global" thing

You can store any simple object, like a number, boolean or a Unity Vector2. You can also store references to nodes and other objects in the game, and they will be stored and restored correctly. Lists and dicts with string keys can be stored as well, as long as the values inside are simple types or references to objects in the game.

?> Examples: Look for the 'CustomData' string in **core-files** and you'll find many examples of things being stored this way, especially in campaign missions that have custom mechanics.

### Magic methods

## Coding specific things

### Industries

### Structures

### Actions

### Projects

### Resources

### Planet types and map generation

### Mutators: Sector types and quirks

### Quests

### Races