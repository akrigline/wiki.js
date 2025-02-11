---
title: Getting Started with Package Development
description: Some common hurdles facing new Package Developers
published: true
date: 2021-03-17T12:26:57.877Z
tags: settings
editor: markdown
dateCreated: 2021-02-05T16:13:36.470Z
---

> ### What is this?
> This is going to be our FAQ and "common hurdles to overcome" guide to help new package developers. It's going to be perpetually updating and changing.
> If you know of some questions or common sticking points for new package developers, please edit this to include those things.
> ### Have a question not answered here?
> The [League of Foundry VTT Developers](https://discord.gg/cudQBu8HKT) is a helpful development-focused discord community which aims to help veterans and new developers alike. Please drop by and ask us your questions, the more questions we get the more likely this document is going to be updated with their answers.
> ### Pseudocode
> None of the code within this document is guaranteed to work and should be tested before used in a world that you care about.
{.is-warning}

> This document is up to date as of 0.7.9
{.is-info}

# FAQs

## Is there a list of hooks?

[There is a community maintained hook typescript definition and documentation file.](https://github.com/League-of-Foundry-Developers/foundry-vtt-types/blob/foundry-0.7.9/foundry/hooks.d.ts) But there's an easier way to discover what hook you can use for a specific piece of functionality:

```js
CONFIG.debug.hooks = true;
```

With this enabled, you'll see every hook that fires as you interact with Foundry's UI and what arguments it is passed.

> Bonus Tip: Install [Developer Mode](https://www.foundryvtt-hub.com/package/_dev-mode/) and you can explore the hooks debug more easily.
{.is-info}

## How do I delete a value?

This is Foundry specific and will delete the `someKey` in the database entry for `someEntity`
```js
someEntity.update({
  -=someKey: null
})
```

## What does Foundry VTT use for templating?

The `.html` files in Foundry are actually [Handlebars](https://handlebarsjs.com/) files. In your own packages you can use either `.html` or `.hbs`.

### How do I use Handlebars Templates?

> Stub
> [`loadTemplates`](https://foundryvtt.com/api/global.html#loadTemplates)
> [`renderTemplate`](https://foundryvtt.com/api/global.html#renderTemplate)

### How do I debug Handlebars?

Handlebars comes built in with a `log` helper, which can be used exactly like `console.log` can be to log out what data a template sees. It might help to think of a space as a comma within Handlebars `{{}}` brackets.

This snippet will log an object with all of the data that the "current scope" has access to:
```hbs
{{log 'some arbitrary string' this}}
```

## How do I change or extend a core function's behavior?

> stub
> "monkeypatching" definition (maybe in appendix?)
> Use [libWrapper.](https://github.com/ruipin/fvtt-lib-wrapper)


The short and mildly helpful answer is "Use [LibWrapper](https://github.com/ruipin/fvtt-lib-wrapper)."

LibWrapper is a library module which lets you 'safely' [monkeypatch](#monkeypatch) other functions, including class methods. We say "safely" because it has some built in QOL things around conflicting patches and puts the power in the user's hands to prioritize one module over another when said conflicts arise (and they will arise).

To pull this off first you have to find where the method or function is in the global scope which libWrapper can see and affect. For dnd5e's entity class that ends up being `game.dnd5e.entities.Actor5e` (assigned [here](https://gitlab.com/foundrynet/dnd5e/-/blob/master/dnd5e.js#L49)). Once you know what prototype you need to modify, you'll end up with something that looks like this:

```js
    libWrapper.register('my-fvtt-module-name', 'game.dnd5e.entities.Actor5e.prototype._prepareSkills', function (wrapped, ...args) {
        console.log('game.dnd5e.entities.Actor5e.prototype._prepareSkills was called with', ...args);
        const actor = this.actor;  // you should have access to `this` in here as well
        // ... do things ...
        const result = wrapped(...args); // remember to call the original (wrapped) method
        // ... do things ...
        return result; // this return needs to have the same shape (signature) as the original, or things start breaking
    });
```

## How do I get started with sockets?

> [Stub](https://github.com/VanceCole/macros/blob/master/sockets.js)

## How do I make functions available to other modules?

> Stub
> 
> [Example: DevMode](https://github.com/League-of-Foundry-Developers/foundryvtt-devMode/blob/f1f98916d25c633ac22020e30cc335a786ed5aa4/src/foundryvtt-devMode.ts#L57)
> Basically you will want to put something namespaced on window or global this, then fire a custom hook.
>
> I would not recommend putting the whole class on window, rather I think it's a better idea to put a few functions as a dedicated api.
>
> This makes it easier to not break api consumers code when your own code changes. Since you are providing these specific api methods, you can ensure that their shape doesn't change
>
> There's two ways to accomplish this and I'm not really sure why one is better, or even if one is better:
> ```js
> window[MODULE_ID] = {
>   someFunction: YourClass.method
> }
> ```
> 
> Putting it on the window allows an optional dependency like so window['your-module']?.someFunction.
> 
> ```js
> globalThis[MODULE_ID] = {
>   someFunction: YourClass.method
> }
> ```
> 
> Putting it on the globalThis doesn't seem to allow the same thing.


## What is a flag and how do I use them?

Flags are the safest way that modules can store arbitrary data on existing entities. If you are making a module which allows the user to set a data point which isn't supported normally in Foundry Core or a system's data structure, you should use a flag.

A flag does not have to be a specific type, anything which can be `JSON.stringify`ed is valid.

### Setting a flag's value
Flags are automatically namespaced within the first parameter given to [`Entity#setFlag`](https://foundryvtt.com/api/Entity.html#setFlag).

```js
const newFlagValue = 'foo';

someEntity.setFlag('myModuleName', 'myFlagName', newFlagValue);
```

#### Can I mutate the value itself?
Setting a flag's value without `setFlag` will not persist that change in the database. This should only be done as part of a larger operation which persists an entity's data in the database, for example as a part of character sheet editing.

### Getting a flag's value
There are two places to get a flag value: On the data model itself, or with [`Entity#getFlag`](https://foundryvtt.com/api/Entity.html#getFlag)

```js
const flagValue = someEntity.getFlag('myModuleName', 'myFlagName');
// flagValue === 'foo'
```

#### On the data model itself
This can be somewhat tricky as it might be different depending on what entity you're dealing with. Somewhere in the entity's data object there is a `flags` key. The object attached is keyed by module `name`, which is itself an object keyed by flag name, as registered in `setFlag`.

### Unset a flag
A safe way to delete your flag's value is with [`Entity#unsetFlag`](https://foundryvtt.com/api/Entity.html#unsetFlag). This will fully delete that key from your module's flags on the provided entity.

```js
someEntity.unsetFlag('myModuleName', 'myFlagName');
```

### How do I use this?
It's arbitrary data that you can safely control on any `Entity`. Because of this, all of the hooks related to that entity are going to have your flag available when they fire.

For example, if I have a flag on a Scene, I can check if that flag exists when the `updateScene` hook fires.

```js
Hooks.on('updateScene', (scene, data) => {
  if (hasProperty(data, 'flags.myModule')) {
    console.log(data);
  }
});
```

## How do I work with settings?

Settings, like flags, are a way for modules to store and persist data. Settings are not tied to a specific entity however, unlike flags. Also unlike flags they are able to leverage the 'scope' field to keep a set of data specific to a user's localStorage (`scope: client`) or put that data in the database (`scope: world`).

For the vast majority of use-cases, settings are intended to be modified by a UI, either a Menu or within the Module Settings panel itself. These settings are intended to be used to modify the functionality of a module or system, rather than store arbitrary data for that module or system.

### Registering a Setting

All settings must be registered before they can be set or accessed. This needs to be done with [`game.settings.register`](https://foundryvtt.com/api/ClientSettings.html#register), with `game.settings` being an instance of `ClientSettings`.

```js
/*
 * Create a custom config setting
 */
await game.settings.register('myModuleName', 'mySettingName', {
  name: 'My Setting',
  hint: 'A description of the registered setting and its behavior.',
  scope: 'world',     // "world" = sync to db, "client" = local storage 
  config: true,       // false if you dont want it to show in module config
  type: Number,       // Number, Boolean, String,  
  default: 0,
  range: {.           // range turns the UI input into a slider input
    min: 0,           // but does not validate the value
    max: 100,
    step: 10
  },
  onChange: value => { // value is the new value of the setting
    console.log(value)
  }
});
```

### Setting a Setting's value
Settings can be set with [`game.settings.set`](https://foundryvtt.com/api/ClientSettings.html#set). It's important to note that a `scope: world` setting can only be set by a Gamemaster user, and that `scope: client` settings will only persist on the user's local machine.

```js
const whateverValue = 'foo';

game.settings.set('myModuleName','myModuleSetting', whateverValue);
```

### Getting a Setting's value

Settings can be read with [`game.settings.get`](https://foundryvtt.com/api/ClientSettings.html#get). 

```js
const someVariable = game.settings.get('myModuleName','myModuleSetting');
console.log(someVariable); // expected to be 'foo'
```

### Setting Menus

Sometimes your use case is more complex than a few settings will allow you to manage. In these cases the best play is to register a settings menu with [`game.settings.registerMenu`](https://foundryvtt.com/api/ClientSettings.html#registerMenu), and manage your settings logic with a [FormApplication](https://foundryvtt.wiki/en/development/guides/understanding-form-applications). Note that we aren't actually registering a setting to be stored, simply a menu button.

This works best when used in conjunction with a registered setting of type `Object`.

```js
game.settings.registerMenu("myModule", "mySettingsMenu", {
  name: "My Settings Submenu",
  label: "Settings Menu Label",      // The text label used in the button
  hint: "A description of what will occur in the submenu dialog.",
  icon: "fas fa-bars",               // A Font Awesome icon used in the submenu button
  type: MySubmenuApplicationClass,   // A FormApplication subclass
  restricted: true                   // Restrict this submenu to gamemaster only?
});


await game.settings.register('myModuleName', 'myComplexSettingName', {
  scope: 'world',     // "world" = sync to db, "client" = local storage 
  config: false,      // we will use the menu above to edit this setting
  type: Object,
  default: {},        // can be used to set up the default structure
});


/**
 * For more information about FormApplications, see:
 * https://foundryvtt.wiki/en/development/guides/understanding-form-applications
 */
class MySubmenuApplicationClass extends FormApplication {
  // lots of other things...
  
  getData() {
  	return game.settings.get('myModuleName', 'myComplexSettingName');
  }
  
  _updateObject(event, formData) {
    const data = expandObject(formData);
    game.settings.set('myModuleName', 'myComplexSettingName', data);
  }
}
```

#### Why would I want this?

FormApplications allow you to run any logic you want, which includes setting settings, thus this kind of power could be leveraged to accomplish many things:

1. **Space.** You could easily use this to tidy up a lot of module settings which would otherwise take up a lot of vertical space on the settings list.
2. **Validation.** Since you control the FormApplication's submit logic, you could run validation on user inputs before saving them to the database.
3. **Edit Setting Objects.** If you have a use case for a complex object of data being stored as a setting, a FormApplication menu would let your users manipulate that object directly.

## How do I get data based on a user-defined data path?

For system independence, it's often a good idea to allow data paths to be defined by a setting (e.g. if you want to reference an attribute modifier in a dialog, then you could hard-code `modifier = actor.attributes.str.mod`, but that path is 5e specific - so allowing a user to define the path to the attribute modifier makes it easy to tweak your module for a specific system).

This can be done with the `getProperty(object, key)` function, e.g. `modifier = getProperty(actor, attributeKey)`.


# Common Hurdles and How to Overcome Them

## My new package doesn't show up in Foundry

By far the most common issue when you're brand new to this. Don't panic and go through this checklist

### 1. Your manifest json has all the required fields. 

* [Modules](https://foundryvtt.com/article/module-development/)
* [Systems](https://foundryvtt.com/article/system-development/)

### 2. Your manifest's `name` matches your directory exactly.

Foundry verifies that the package `name` and the package directory name match exactly, otherwise it deems them invalid.

```json
{
  "name": "my-module-name"
}
```

With this module.json in the following structure, your module will not appear as installed in Foundry.

```
/Data
└ /modules
  ├ /not-my-module
  └ /my-module-wrong-name
    ├ myScript.js
    └ module.json
```

Instead you need to either change your directory or your `name` so they match.

### 3. Your manifest is in the root of your package directory.

Similar to 2, but affects people who have a build step.

```
/Data
└ /modules
  ├ /not-my-module
  └ /my-module-name
  	└ /dist
    	├ myScript.js
    	└ module.json
```

In this example, the manifest at `modules/my-module-name/dist/module.json` will not be found by Foundry and thus the module will not appear as installed.


## Storing Module Data

```plantuml
@startuml

(*)-> "I have data I want to store"

if "Data is asociated with an Entity" then
	->[Yes] "Flag"
else
	-->[No] "Setting"
  if "Data needs to be shared between clients"
  	->[No] "Setting, scope 'client'"
  else
  	if "Only GM can modify data"
    	->[Yes] "Setting, scope 'world'"
    else
    	-->[No] "Setting, scope 'world' + GM proxy"
    endif
  endif
endif

@enduml
```

Use case flowchart:
1. I want to store data associated with a particular entity -> Flag. Some entities are not editable by all clients and flags respect that.
2. I want to store data not associated with a particular entity:
	  1. The data does not have to be shared between clients -> Setting, scope `client`.
 	  2. The data does have to shared between clients:
		    1. All clients can access and modify -> Setting with a GM Proxy is the only way to do this, it is not pretty.
		    2. All clients can access but only the GM can modify -> Setting, scope `world`, no workarounds needed.


> stub
> - Flags
> - Settings
> - Pros and Cons of the two

# Hello World Module Walkthrough 


> ### [Stub](https://www.reddit.com/r/restofthefuckingowl/), should be its own guide article.
> https://foundryvtt.com/article/module-development/
> 1. Create your directory
> 1.a Symlink from a /dist directory
> 2. Common gotchyas with manifest.json and file structure
> 3. Include a script file
> 6. Include localization
> 4. Include a template
> 5. Include CSS
> 6. ???
> 7. Profit


# Appendix 1: Fun Javascript Terminology

## Mutate

Basically means "changing" a piece of data.

```js

let foo = 'bar';

someFunction() {
  // does things...
  foo = 'bat';
  
  console.log({foo}); // 'bat'
}

someOtherFunction() {
  if (foo === 'bar') {
  	// do things...
  }
}
```

In the example above, `someFunction` mutates `foo`. Depending on the order in which it is called (before or after `someOtherFunction`) this might cause `someOtherFunction` to behave unexpectedly.

This isn't necessarily a bad pattern but it can save you some headaches if you avoid mutating things as much as possible. Instead try to make duplicates of your data when you want to change it.

## Promise

> [stub](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

## Monkeypatch

> stub [monkeypatch](https://davidwalsh.name/monkey-patching)
