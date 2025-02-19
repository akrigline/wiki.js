---
title: SD11.1 Creating-rollable-buttons-with-event-listeners
description: 
published: true
date: 2020-12-20T21:54:39.491Z
tags: 
editor: undefined
dateCreated: 2020-09-23T00:36:24.667Z
---

To make things on your character sheet rollable, you'll need to add a class that you can listen for in your sheet's <!-- {% raw %} -->`activateListeners()`<!-- {% endraw %} --> method. In the Boilerplate System, ability rolls have an example of this with a <!-- {% raw %} -->`rollable`<!-- {% endraw %} --> class. Let's look at the ability modifier line from <!-- {% raw %} -->`actor-sheet.html`<!-- {% endraw %} -->:

<!--- {% raw %} --->

```handlebars
<span class="ability-mod rollable" data-roll="d20+@abilities.{{key}}.mod" data-label="{{key}}">{{numberFormat ability.mod decimals=0 sign=true}}</span>
```

<!--- {% endraw %} --->

We've added a <!-- {% raw %} -->`rollable`<!-- {% endraw %} --> class along with a few different attributes for <!-- {% raw %} -->`data-roll`<!-- {% endraw %} --> and <!-- {% raw %} -->`data-label`<!-- {% endraw %} --> that our listener can then use to create the rolls. Those attributes aren't really necessary; because this is a character sheet we could just add the rollable class and then compute those rolls in the JS. However, by making them into attributes, we can just include the attributes with whatever formula we need, and the <!-- {% raw %} -->`data-label`<!-- {% endraw %} --> will show up in the chat message as flavor text.

The formula in this example includes <!-- {% raw %} -->`@abilities.{{key}}.mod`<!-- {% endraw %} -->, which would compute as <!-- {% raw %} -->`@abilities.str.mod`<!-- {% endraw %} -->, for example. This should follow the same format if you were to write out inline rolls in chat or in macros, because in the roll method we're about to make, we're passing it <!-- {% raw %} -->`this.actor.data.data`<!-- {% endraw %} --> just like core does for those scenarios.

Finally, the <!-- {% raw %} -->`rollable`<!-- {% endraw %} --> class also includes some lightweight CSS for it to turn the mouse into a pointer and add a red text shadow on hover or focus.

### activateListeners()

Next, let's take a look at <!-- {% raw %} -->`actor-sheet.js`<!-- {% endraw %} --> where we've overridden the ActorSheet class. We need to add a new click listener inside our ActorSheet's <!-- {% raw %} -->`activateListeners()`<!-- {% endraw %} --> method:

<!--- {% raw %} --->

```js
// Rollable abilities.
html.find('.rollable').click(this._onRoll.bind(this));
```

<!--- {% endraw %} --->

This will call a custom <!-- {% raw %} -->`_onRoll()`<!-- {% endraw %} --> method on click. Because that method doesn't exist, we need to go down towards the end of our class and add a new method after the <!-- {% raw %} -->`_onItemCreate()`<!-- {% endraw %} --> method:

<!--- {% raw %} --->

```js
/**
 * Handle clickable rolls.
 * @param {Event} event   The originating click event
 * @private
 */
_onRoll(event) {
  event.preventDefault();
  const element = event.currentTarget;
  const dataset = element.dataset;

  if (dataset.roll) {
    let roll = new Roll(dataset.roll, this.actor.data.data);
    let label = dataset.label ? `Rolling ${dataset.label}` : '';
    roll.roll().toMessage({
      speaker: ChatMessage.getSpeaker({ actor: this.actor }),
      flavor: label
    });
  }
}
```

<!--- {% endraw %} --->

First, we're preventing other click behaviors (such as if this were an <!-- {% raw %} -->`<a>`<!-- {% endraw %} --> tag), and then we're grabbing both the element that was clicked and any data attributes that were included on that element (the <!-- {% raw %} -->`data-roll`<!-- {% endraw %} --> and <!-- {% raw %} -->`data-label`<!-- {% endraw %} --> attributes, specifically).

Next, if there is a <!-- {% raw %} -->`dataset.roll`<!-- {% endraw %} --> attribute, we'll use that to build the roll. To create a roll, we use <!-- {% raw %} -->`new Roll('formula')`<!-- {% endraw %} -->, and we've already written out the formula in our Handlebars template. The second argument of Roll() is the object to use for its data, so in this case we want to pass along the actor's data attributes.

After that, we get the label attribute if any. And with that we have everything we need to send it to chat. Creating a new roll doesn't actually rolling it, so we have to call the <!-- {% raw %} -->`roll()`<!-- {% endraw %} --> method on our Roll object, and then call the <!-- {% raw %} -->`toMessage()`<!-- {% endraw %} --> method on that result to send it to chat. We want this to reference the actor token as the speaker for the chat message, so we set the speaker and flavor properties to match what we need.

And with that, we've successfully made rollable attributes!

---

* **Prev:** [Extending the ItemSheet class](https://foundryvtt.wiki/en/development/guides/SD-tutorial/SD10-Extending-the-ItemSheet-class)
* **Next:** [Grouping items by type](https://foundryvtt.wiki/en/development/guides/SD-tutorial/SD113-Grouping-items-by-type)