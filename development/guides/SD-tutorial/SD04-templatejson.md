---
title: SD04 template.json
description: 
published: true
date: 2020-12-20T21:50:00.894Z
tags: 
editor: undefined
dateCreated: 2020-09-23T00:35:41.059Z
---

Your system's data model for character and item sheets is defined in <!-- {% raw %} -->`template.json`<!-- {% endraw %} -->. Any changes you make to this file will require you to return to Foundry's setup screen and relaunch your world. The template.json for the Boilerplate System looks like this.

<!--- {% raw %} --->

```json
{
  "Actor": {
    "types": ["character"],
    "templates": {
      "base": {
        "health": {
          "value": 10,
          "min": 0,
          "max": 10
        },
        "power": {
          "value": 5,
          "min": 0,
          "max": 5
        }
      }
    },
    "character": {
      "templates": ["base"],
      "biography": "",
      "attributes": {
        "level": {
          "value": 1
        }
      },
      "abilities": {
        "str": {
          "value": 10
        },
        "dex": {
          "value": 10
        },
        "con": {
          "value": 10
        },
        "int": {
          "value": 10
        },
        "wis": {
          "value": 10
        },
        "cha": {
          "value": 10
        }
      }
    }
  },
  "Item": {
    "types": ["item"],
    "templates": {
      "base": {
        "description": ""
      }
    },
    "item": {
      "templates": ["base"],
      "quantity": 1,
      "weight": 0
    }
  }
}
```

<!--- {% endraw %} --->

That file can be a bit difficult to understand, so let's break it down into a bit more detail.

## The basic structure

The top level view of template.json looks like this:

<!--- {% raw %} --->

```json
{
  "Actor": {},
  "Item": {},
}
```

<!--- {% endraw %} --->

Those two properties correspond with the two entity types that you can make changes to in your template.json. They both support 3 properties:

<!--- {% raw %} --->

```json
{
  "Actor": {
    "types": ["character", "npc"],
    "templates": {
      "base": {
        "health": {
          "value": 10,
          "max": 10
        }
      }
    },
    "character": {
      "templates": ["base"],
      "foo": "",
      "bar": {
        "value": ""
      }
    },
    "npc": {
      "templates": ["base"]
    }
  }
}
```

<!--- {% endraw %} --->

The first property, <!-- {% raw %} -->`types`<!-- {% endraw %} -->, defines the different sub-types of this entity type that your system will use.  In this example, those types are <!-- {% raw %} -->`character`<!-- {% endraw %} --> and <!-- {% raw %} -->`npc`<!-- {% endraw %} -->, but you can create as many as you need.

The second property, <!-- {% raw %} -->`templates`<!-- {% endraw %} -->, is where you define common attributes that can be applied to any of the other sub-types. In this example, we've defined a <!-- {% raw %} -->`base`<!-- {% endraw %} --> template that has a <!-- {% raw %} -->`health`<!-- {% endraw %} --> property that has <!-- {% raw %} -->`value`<!-- {% endraw %} --> and <!-- {% raw %} -->`max`<!-- {% endraw %} --> properties.

The third and fourth properties, <!-- {% raw %} -->`character`<!-- {% endraw %} --> and <!-- {% raw %} -->`npc`<!-- {% endraw %} -->, will match up with whatever you defined in the <!-- {% raw %} -->`types`<!-- {% endraw %} --> property earlier. If you had more or less types defined, you would continue adding them one after the other just like how <!-- {% raw %} -->`character`<!-- {% endraw %} --> and <!-- {% raw %} -->`npc`<!-- {% endraw %} --> came after the <!-- {% raw %} -->`templates`<!-- {% endraw %} --> property. Each of these sub-types can have templates applied to them, such as the <!-- {% raw %} -->`base`<!-- {% endraw %} --> template in this example. They can also have custom properties unique to their sub-type, such as the <!-- {% raw %} -->`foo`<!-- {% endraw %} --> and <!-- {% raw %} -->`bar`<!-- {% endraw %} --> properties in this example.

For more details, go to [https://foundryvtt.com/article/system-development/](https://foundryvtt.com/article/system-development/) and scroll down to the section for **The Data Template**.

## Actors vs. Items

What is the difference between an Actor and an Item? That depends on what your system needs, and only you can answer that question. On a technical level, actors will have tokens and can be added to scenes, while items are typically only used for creating compendium content and then attaching those to actors as owned items. Items are not just items/equipment in most RPG systems; they can be other things as well such as Class Features, Spells, or Feats. If your system needs to track more than a few properties on something that can be added to character sheets as a discreet piece, it should probably be an item type.

For example, this snippet would add three different item types to your system, some of which share features:

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

---

* **Prev:** [system.json](https://foundryvtt.wiki/en/development/guides/SD-tutorial/SD03-systemjson)
* **Next:** [Creating your main system javascript file](https://foundryvtt.wiki/en/development/guides/SD-tutorial/SD05-Creating-your-main-JS-file)