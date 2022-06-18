# Using developer mode

Developer mode gives you various superpowers that can make testing your mod easier. To access it, go to the mod management window, find the **"Developer mode"** mod, and enable it by ticking its checkbox.

When using developer mode, some parts of the game are inaccessible, but in exchange you get the following superpowers ingame:

* use **Shift+drag** to move planets and other things around the map
* press **[+]** or **[-]** to quickly add/substract cash and science
* press **[F5]** during testing to **quick save** the game in one of 8 slots
* press **[F9]** anywhere in the game or in the menus to **quick load** this savefile later
* press **[P]** ingame to access a **command palette** that lets you place planets/structures, unlock/invent technology, and more

Additionally, you have access to the **developer console** which allows you to execute custom Python commands during the game.

* you can use **[`]** (the backtick/tilde key, usually below Escape) to access the developer console where you can execute custom Python commands
* you can use **Ctrl+click** on most objects in the game to bring up their data in the development console

# Using the developer console

You can type any Python expression into the input field at the bottom of the console to execute it. This is usually used to inspect various objects in the game. Any expression that you can find in the game's scripts can also be typed here to evaluate it.

The easiest way to inspect a specific object is to **Ctrl+click** it in the map view. This will assign it to the `it` Python variable, bring up the Python console and show you the properties of the object. From there, you can drill down by typing eg. `it.Products` to see all the products the planet you clicked. To make this easier, you can also click on property names, which will drill down automatically.
