---
title: SD11.4 Separating-item-types-into-tabs
description: 
published: true
date: 2020-12-20T22:11:35.115Z
tags: 
editor: undefined
dateCreated: 2020-09-23T00:36:36.715Z
---

Now that we've created separate groups of items in the ActorSheet class, we can update our items tab to replace it with multiple tabs, one for each type of item. First, we need to add the new tabs to our navigation.

Edit the actor-sheet.html file and update the nav for the tabs to have 4 links, as in the example below.

<!--- {% raw %} --->

```handlebars
    {{!-- ...continued... --}}
    {{!-- Sheet Tab Navigation --}}
    <nav class="sheet-tabs tabs" data-group="primary">
        <a class="item" data-tab="description">Description</a>
        <a class="item" data-tab="items">Items</a>
        <a class="item" data-tab="features">Features</a>
        <a class="item" data-tab="spells">Spells</a>
    </nav>
    {{!-- ...continued... --}}
```

<!--- {% endraw %} --->

After that, scroll down to the owned items tab and change the <!-- {% raw %} -->`{{#each`<!-- {% endraw %} --> loop from <!-- {% raw %} -->`actor.items`<!-- {% endraw %} --> to <!-- {% raw %} -->`actor.gear`<!-- {% endraw %} -->, which is the new array we made earlier that includes only the item entities with the <!-- {% raw %} -->`item`<!-- {% endraw %} --> sub-type.

<!--- {% raw %} --->

```handlebars
        {{!-- ...continued... --}}
        {{!-- Owned Items Tab --}}
        <div class="tab items" data-group="primary" data-tab="items">
            <ol class="items-list">
                <li class="item flexrow item-header">
                  <div class="item-image"></div>
                  <div class="item-name">Name</div>
                  <div class="item-controls">
                    <a class="item-control item-create" title="Create item" data-type="item"><i class="fas fa-plus"></i> Add item</a>
                  </div>
                </li>
            {{#each actor.gear as |item id|}}
                <li class="item flexrow" data-item-id="{{item._id}}">
                    <div class="item-image"><img src="{{item.img}}" title="{{item.name}}" width="24" height="24"/></div>
                    <h4 class="item-name">{{item.name}}</h4>
                    <div class="item-controls">
                        <a class="item-control item-edit" title="Edit Item"><i class="fas fa-edit"></i></a>
                        <a class="item-control item-delete" title="Delete Item"><i class="fas fa-trash"></i></a>
                    </div>
                </li>
            {{/each}}
            </ol>
        </div>
        {{!-- ...continued... --}}
```

<!--- {% endraw %} --->

Once that's been done, let's add the markup for another tab just below the items tab. This is almost identical to the items tab from before, with the following changes:

- The class on the tab is <!-- {% raw %} -->`features`<!-- {% endraw %} --> rather than <!-- {% raw %} -->`items`<!-- {% endraw %} -->, as is the <!-- {% raw %} -->`data-tab`<!-- {% endraw %} --> attribute.
- On the <!-- {% raw %} -->`<a class="item-control item-create"`<!-- {% endraw %} --> element, the <!-- {% raw %} -->`data-type`<!-- {% endraw %} --> attribute is <!-- {% raw %} -->`feature`<!-- {% endraw %} -->, which will instruct this control to create a feature instead of an item.
- The <!-- {% raw %} -->`{{#each`<!-- {% endraw %} --> loop is on <!-- {% raw %} -->`actor.features`<!-- {% endraw %} --> instead of <!-- {% raw %} -->`actor.gear`<!-- {% endraw %} -->.

<!--- {% raw %} --->

```handlebars
        {{!-- ...continued... --}}
        {{!-- Owned Features Tab --}}
        <div class="tab features" data-group="primary" data-tab="features">
            <ol class="items-list">
                <li class="item flexrow item-header">
                  <div class="item-image"></div>
                  <div class="item-name">Name</div>
                  <div class="item-controls">
                    <a class="item-control item-create" title="Create item" data-type="feature"><i class="fas fa-plus"></i> Add feature</a>
                  </div>
                </li>
            {{#each actor.features as |item id|}}
                <li class="item flexrow" data-item-id="{{item._id}}">
                    <div class="item-image"><img src="{{item.img}}" title="{{item.name}}" width="24" height="24"/></div>
                    <h4 class="item-name">{{item.name}}</h4>
                    <div class="item-controls">
                        <a class="item-control item-edit" title="Edit Item"><i class="fas fa-edit"></i></a>
                        <a class="item-control item-delete" title="Delete Item"><i class="fas fa-trash"></i></a>
                    </div>
                </li>
            {{/each}}
            </ol>
        </div>
        {{!-- ...continued... --}}
```

<!--- {% endraw %} --->

After that, let's make the spells tab. The spells tab is a bit different from the other two item tabs, as for this one we also want to sort the spells into multiple rows, one for each spell level. Review the code below closely and compare it to the feature tab we just made. After that, scroll down below the code example for a breakdown of what's happening.

<!--- {% raw %} --->

```handlebars
        {{!-- ...continued... --}}
        {{!-- Owned Spells Tab --}}
        <div class="tab spells" data-group="primary" data-tab="spells">
            <ol class="items-list">
                <li class="item flexrow item-header">
                  <div class="item-image"></div>
                  <div class="item-name">Name</div>
                  <div class="item-controls"></div>
                </li>
                {{#each actor.spells as |spells spellLevel|}}
                    <li class="item flexrow item-header">
                      <div class="item-name">Level {{spellLevel}} Spells</div>
                      <div class="item-controls">
                        <a class="item-control item-create" title="Create item" data-type="spell" data-spell-level="{{spellLevel}}"><i class="fas fa-plus"></i> Add LVL {{spellLevel}}</a>
                      </div>
                    </li>
                    {{#each spells as |item id|}}
                        <li class="item flexrow" data-item-id="{{item._id}}">
                            <div class="item-image"><img src="{{item.img}}" title="{{item.name}}" width="24" height="24"/></div>
                            <h4 class="item-name">{{item.name}}</h4>
                            <div class="item-controls">
                                <a class="item-control item-edit" title="Edit Item"><i class="fas fa-edit"></i></a>
                                <a class="item-control item-delete" title="Delete Item"><i class="fas fa-trash"></i></a>
                            </div>
                        </li>
                    {{/each}}
                {{/each}}
            </ol>
        </div>
        {{!-- ...continued... --}}
```

<!--- {% endraw %} --->

At first, it looks pretty similar to gear and features. The major difference is that we actually have two loops, an outer loop for spell levels and an inner loop for spell items within those levels.

- We've removed the <!-- {% raw %} -->`item-create`<!-- {% endraw %} --> control from the tab as a whole, since those would be more useful if we had one per spell level.
- To loop through the spell level keys we made in our JS earlier, we use an each loop like <!-- {% raw %} -->`{{#each actor.spells as |spells spellLevel|}}`<!-- {% endraw %} -->. The <!-- {% raw %} -->`spells`<!-- {% endraw %} --> identifier will be an array of spells, while the <!-- {% raw %} -->`spellLevel`<!-- {% endraw %} --> key will be the numerical spell level.
- Each spell level has it's on <!-- {% raw %} -->`item-header`<!-- {% endraw %} --> list item with a customized <!-- {% raw %} -->`item-create`<!-- {% endraw %} --> control for creating new spells. Notice that it also has a <!-- {% raw %} -->`data-spell-level`<!-- {% endraw %} --> attribute that we haven't added any additional code for in our JS. The <!-- {% raw %} -->`_onItemCreate()`<!-- {% endraw %} --> method will be able to interpret <!-- {% raw %} -->`data-spell-level`<!-- {% endraw %} --> as <!-- {% raw %} -->`item.spellLevel`<!-- {% endraw %} --> since we were grabbing the whole dataset and it matches the variable name. Because we can't actually use <!-- {% raw %} -->`data-spellLevel`<!-- {% endraw %} --> as our attribute name, Foundry has some additional logic to be able to correctly interpret the hyphenated attribute name as our camelCase property.
- The inner <!-- {% raw %} -->`{{#each spells as |item id|}}`<!-- {% endraw %} --> loop behaves exactly like the item and feature loops did earlier.

## Next steps

That's all we have to do to get item grouping working! While this works for separating item types into multiple tabs and spells by level, you could extrapolate these ideas to work with any item structures you need. For instance, what if you had an <!-- {% raw %} -->`itemType`<!-- {% endraw %} --> field on your gear items that was similar to our <!-- {% raw %} -->`spellLevel`<!-- {% endraw %} --> field? With a little bit of extra work, we could build a structure on gear with keys such as <!-- {% raw %} -->`weapon`<!-- {% endraw %} -->, <!-- {% raw %} -->`armor`<!-- {% endraw %} -->, and <!-- {% raw %} -->`trinkets`<!-- {% endraw %} --> similar to the numerical keys we had on spells and do a very similar loop to group items by their <!-- {% raw %} -->`itemType`<!-- {% endraw %} -->.

---

* **Prev:** [Grouping items by type](https://foundryvtt.wiki/en/development/guides/SD-tutorial/SD113-Grouping-items-by-type)
* **Next:** [Localization](https://foundryvtt.wiki/en/development/guides/SD-tutorial/SD13-Localization)