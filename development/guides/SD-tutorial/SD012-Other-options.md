---
title: SD01.2 Other-options
description: 
published: true
date: 2020-09-23T01:34:01.384Z
tags: 
editor: undefined
dateCreated: 2020-09-23T00:35:23.660Z
---

In addition to using the Boilerplate System, you can also use Simple World-building or one of the other available boilerplate systems, such as flatpacktemplate.

## Starting with Simple World-building system

Starting with Simple World-building system (SWS) is a good option if you want to build your systems based on the latest standards that Atropos has built into FoundryVTT. The system is one of the two official systems for FoundryVTT, so you can be sure that the code in there will be a great example of how to build things The Right Way™.

If you use SWS as your base, you should search the files for any instances of <!-- {% raw %} -->`worldbuilding`<!-- {% endraw %} --> or <!-- {% raw %} -->`simple`<!-- {% endraw %} --> and rename them to be more appropriate for your system's name.

Something to watch out for is that SWS uses dynamic attributes for both actors and items. If your system doesn't also use those, you'll want to remove them from your html template files and make sure they're also removed from your actor and item sheet Javascript.

## Starting with a boilerplate

Another option is to use a boilerplate system as your base. These systems aren't intended to be use directly but are instead lightweight systems that include some stuff to get you started while leaving most of the work up to you.

This is the option that I recommend for most new developers, and is also the route this tutorial will follow. There are several good boilerplate systems available:

### flatpacktemplate

**URL**: [https://github.com/GateKept/flatpacktemplate](https://github.com/GateKept/flatpacktemplate)

The flatpacktemplate system is a lightweight starter set created by GateKeeper from the Foundry discord server. It includes sample item and actor classes to get you started.

### Boilerplate System
**URL**: [https://gitlab.com/asacolips-projects/foundry-mods/boilerplate](https://gitlab.com/asacolips-projects/foundry-mods/boilerplate)

The Boilerplate System is a lightweight starter set I created that includes a small number of examples (such as character ability scores) and a few helper CSS classes for laying out your sheets without actually having to write new CSS. It also includes the Sass source files I used to the compile the CSS if you're comfortable with NPM and Gulp.

We'll use this system for the tutorial. If you're comfortable with using NPM, you can also use my Yeoman generator to generate this system: [https://www.npmjs.com/package/generator-foundry](https://www.npmjs.com/package/generator-foundry). Once you have the generator installed, you just need to run <!-- {% raw %} -->`yo foundry`<!-- {% endraw %} --> in your systems directory and it will prompt you for your system name before generating the files for you.

### Foundry Project Creator

**URL**: [https://www.npmjs.com/package/create-foundry-project](https://www.npmjs.com/package/create-foundry-project)

The Foundry Project Creator is definitely the advanced option of these three, but I strongly recommend it if you're looking for an excellent CLI and would like to use TypeScript for a more robust developer experience. In addition to generating systems, the Foundry Project Creator will also let you generate modules!

## Starting from scratch

You can also start completely from scratch! This is the most advanced option since you need to already know what to build, but more information can be found at [https://foundryvtt.com/article/system-development/](https://foundryvtt.com/article/system-development/).

---

* **Prev**: [Getting an empty system together](https://foundry-vtt-community.github.io/wiki/SD01-Getting-started)
* **Next**: [Stuff to be aware of](https://foundry-vtt-community.github.io/wiki/SD02-Stuff-to-be-aware-of)