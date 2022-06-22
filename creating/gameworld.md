# The structure of Slipways

Before we dive into how to add new things to Slipways, it would probably be worthwhile to talk about the things that are already there and how the game world is structured. This chapter will introduce some terms commonly used in Slipways code and explain how they all fit together, to make it easier to understand the following chapters.

If you're impatient, you can skip to the next chapter and start figuring out how to add new stuff, and only get back here to look up a term when you need to - but you should probably read at least enough to understand what **nodes** and **industries** are.

## The cosmos is a series of nodes

Perhaps the most central concept in Slipways is the **node**. A node is something that can receive and make resources and accept connections (usually slipways) connecting them to other nodes. There are two types of nodes most commonly encountered: **planets** and **structures**. Structures are used to represent actual stuff built by the player, but also pretty much any "non-planet" thing on the game map, for example asteroids and forebear ruins are also represent as structures.

The most important thing about a node is its **industry**. The node doesn't know much about how it should work on its own: the node's industry is what actually controls the needs and products, how the node upgrades from level and many, many other aspects of how the node works. On planets, the industry is usually chosen by the player during colonization - the colonization options offered for the planet are in fact just industry types. On the other hand, structures are usually tied together with one type of industry that is automatically established on a structure when it is built, but there are exceptions: for example, the microforge works much like a planet in this regard, and asteroids (which are structures) can be colonized with new industries given the right tech.

**Industries** are structured in **levels**, usually four. For planets, these correspond to the named advancement levels: "Struggling", "Established", "Successful" and "Prosperous". Structures also go through these levels, but they aren't named there (just numbered), and many structures cannot advance all the way up. The exact rules on how to upgrade between these levels are controlled by a scripted rule tied to the industry. The default rule for planet industries mirrors how planets usually work in the game. Structures do not have a default upgrade rule and as such cannot upgrade unless a new rule is defined.

There are many other rules that an industry can define, from controlling how and if a node accepts connections to adding UI elements labelling the node. These custom rules will be explained in the following chapters.

## Signals and potentials

Before there can be a node, there is a **potential**. A "potential" is what the game engine calls the dark orbs that become planets (or not) when they're scanned by a probe. Each potential is tied to a **signal** which describes what will be found inside. Signals are stored in a special "map" structure until they are discovered in the fog of war - after that, they are stored as part of the potential representing them.

## Needs and products

A **need** is what Slipways calls an input on a node - a resource that has to be provided. A **need** has a resource it's **asking for**, and a resource it is **met with**. These need not be the same (for example if energy was used, but there are other special cases). A need that is not currently receiving a resource is called **unmet**.

A **product** is one unit of a resource being made at a node. Each product can be sent to supply a different need somewhere else.

A product sent to satisfy a need is called a **trade route**. A trade route happens over a series of **connections** (usually slipways, but also eg. hyperlanes or teleporter links) that link the **producer** to the **consumer**.

## The ghosts in the machine

While the previous sections dealt with things you can see on the map, there are two very important types of objects that aren't seen in-game. While they aren't visible, they can be used to control just about every aspect of how the game works. You can find more information on how to script them in the [coding chapter](creating/code.md) - here we're just introducing the basic idea.

### Qualities

**Qualities** are additional effects affecting a node, layered on top of what the industry says. They can add additional needs, products or resource flows, or change other properties of the node. Many things in the game are implemented as qualities: the standard upkeep rules, the effects of some technologies, the effects of a project being built on a planet, or planetary quirks attached during map generation. Basically, if something affects a subset of nodes and changes based on the state of that node, it's probably best implemented as a quality.

There are two types of qualities: automatic and manual. The automatic qualities are established globally, and have an `applies()` that controls whether they affect a given node or not. Manually-attached qualities are directly attached to nodes by other code, and don't have an `applies()` method.

Both types of qualities use **local effects** to do their work - bits of data that are returned from the `effects()` method of a given quality that describe the changes made to the node.

### Global conditions

Where qualities let you affect singular node, **global conditions** are what you use to implement global rules affecting the entire sector in some way. This is usually done in one of two ways:

* reacting to **triggers** sent by the game when something happens - a global condition might use that to eg. grant the player money whenever an ocean planet is colonized
* implementing **magic methods** that are automatically called by the game in certain situations - for example to implement a global cost change on all the connections in the game
