---
title: SD11.3 Grouping-items-by-type
description: 
published: true
date: 2020-12-20T21:55:19.520Z
tags: 
editor: undefined
dateCreated: 2020-09-23T00:36:30.378Z
---

Most of the time, you probably don't want to output every owned item for an actor directly on the actor's character sheet. If you have multiple item types (like items, features, and spells), those should be sorted into groups and displayed under their own headlines or tabs. In order to do that, we'll need to make some adjustments in our ActorSheet class to group the items.

## template.json

Before we group items, let's make sure you have multiple item types. Here's a quick example of the Item definition for the Boilerplate System, and we've also adjusted it so that spells now have a <!-- {% raw %} -->`spellLevel`<!-- {% endraw %} --> attribute as well, which we'll use for grouping them by level later.

<!--- {% raw %} --->

```json
"Item": {
  "types": ["item", "feature", "spell"],
  "templates": {
    "base": {
      "description": ""
    },
    "class": {
      "classes": [],
      "level": 0
    }
  },
  "item": {
    "templates": ["base"],
    "quantity": 1,
    "weight": 0
  },
  "feature": {
    "templates": ["base", "class"],
    "usage": "",
    "roll": ""
  },
  "spell": {
    "templates": ["base", "class"],
    "spellLevel": 1,
	"school": "",
	"save_dc": "",
	"effect": ""
  }
}
```

<!--- {% endraw %} --->

## actor-sheet.js

We need to make a few changes to our ActorSheet class to support grouping items. First, let's modify our <!-- {% raw %} -->`getData()`<!-- {% endraw %} --> method.

<!--- {% raw %} --->

```js
  // ... continued ...
  /** @override */
  getData() {
    const data = super.getData();
    data.dtypes = ["String", "Number", "Boolean"];
    for (let attr of Object.values(data.data.attributes)) {
      attr.isCheckbox = attr.dtype === "Boolean";
    }

    // Prepare items.
    if (this.actor.data.type == 'character') {
      this._prepareCharacterItems(data);
    }

    return data;
  }
  // ... continued ...
```

<!--- {% endraw %} --->

We've modified this from earlier to add a new <!-- {% raw %} -->`this._prepareCharacterItems(data);`<!-- {% endraw %} --> call if this is a character entity. This is a custom method though, so now we also need to make a method for that directly below getData().

<!--- {% raw %} --->

```js
  // ... continued ...
  /**
   * Organize and classify Items for Character sheets.
   *
   * @param {Object} actorData The actor to prepare.
   *
   * @return {undefined}
   */
  _prepareCharacterItems(sheetData) {
    const actorData = sheetData.actor;

    // Initialize containers.
    const gear = [];
    const features = [];
    const spells = {
      0: [],
      1: [],
      2: [],
      3: [],
      4: [],
      5: [],
      6: [],
      7: [],
      8: [],
      9: []
    };

    // Iterate through items, allocating to containers
    // let totalWeight = 0;
    for (let i of sheetData.items) {
      let item = i.data;
      i.img = i.img || DEFAULT_TOKEN;
      // Append to gear.
      if (i.type === 'item') {
        gear.push(i);
      }
      // Append to features.
      else if (i.type === 'feature') {
        features.push(i);
      }
      // Append to spells.
      else if (i.type === 'spell') {
        if (i.data.spellLevel != undefined) {
          spells[i.data.spellLevel].push(i);
        }
      }
    }

    // Assign and return
    actorData.gear = gear;
    actorData.features = features;
    actorData.spells = spells;
  }

  /* -------------------------------------------- */
  // ... continued ...
```

<!--- {% endraw %} --->

There's a lot happening here! Let's break it down into more detail:

### Setup

First, we're setting up our variables.

<!--- {% raw %} --->

```js
const actorData = sheetData.actor;

// Initialize containers.
const gear = [];
const features = []
const spells = {
  0: [],
  1: [],
  2: [],
  3: [],
  4: [],
  5: [],
  6: [],
  7: [],
  8: [],
  9: []
};
```

<!--- {% endraw %} --->
We're putting the actor's data into a more convenient variable, and then initializing empty arrays for our various item types. Spells are being initialized as an object with numeric keys so that we have multiple sub-containers for each level of spells. If your system had fewer levels of spells, such as only odd-numbered spells, the even numbered keys could simply be deleted.

One thing to note is that we've also defined a <!-- {% raw %} -->`gear`<!-- {% endraw %} --> array. Because we already have an array of all items on the actor entity on the <!-- {% raw %} -->`item`<!-- {% endraw %} --> key, we would have problems if we placed our items with the sub-type <!-- {% raw %} -->`item`<!-- {% endraw %} --> into an array with the same name.

### The Loop

After the initial variable setup, we now need to step through our items and sort them.

<!--- {% raw %} --->

```js
// Iterate through items, allocating to containers
// let totalWeight = 0;
for (let i of sheetData.items) {
  let item = i.data;
  i.img = i.img || DEFAULT_TOKEN;
  // Append to gear.
  if (i.type === 'item') {
    gear.push(i);
  }
  // Append to features.
  else if (i.type === 'feature') {
    features.push(i);
  }
  // Append to spells.
  else if (i.type === 'spell') {
    if (i.data.spellLevel != undefined) {
      spells[i.data.spellLevel].push(i);
    }
  }
}
```

<!--- {% endraw %} --->

First, we do a <!-- {% raw %} -->`for (let i of sheetData.items)`<!-- {% endraw %} --> loop to loop through all owned items on this actor. We do some initial setup with the item and image, and after that, we have a series of <!-- {% raw %} -->`if`<!-- {% endraw %} --> statements to check the item type and append it to one of the empty item arrays we defined earlier.

For both <!-- {% raw %} -->`item`<!-- {% endraw %} --> and <!-- {% raw %} -->`feature`<!-- {% endraw %} --> subtypes, we just need to append those to their respective arrays. For spells, we're doing a
bit more logic to check if the spell has a spellLevel and then adding it to the spellLevel key of the <!-- {% raw %} -->`spells`<!-- {% endraw %} --> array.

### Push out the data

Now that all of our arrays are updated, we just need to add them onto our actorData variable as new properties and they'll become available in our HTML templates.

<!--- {% raw %} --->

```js
// Assign and return
actorData.gear = gear;
actorData.features = features;
actorData.spells = spells;
```

<!--- {% endraw %} --->

And that's it for changes to our character sheet's JS! Now let's make some changes to our <!-- {% raw %} -->`actor-sheet.html`<!-- {% endraw %} --> template.

---

* **Prev:** [Creating rollable buttons with event listeners](https://foundryvtt.wiki/en/development/guides/SD-tutorial/SD111-Creating-rollable-buttons-with-event-listeners)
* **Next:** [Separating item types into tabs](https://foundryvtt.wiki/en/development/guides/SD-tutorial/SD114-Separating-item-types-into-tabs)