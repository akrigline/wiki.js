---
title: Split the responsibility of the current PlaceableObject class into core data management, permission control, and configuration which extends EmbeddedEntity vs the Canvas object representation and UX which extends PlaceableObject
description: 
published: true
date: 2021-02-07T17:40:58.106Z
tags: 
editor: undefined
dateCreated: 2021-02-07T17:40:54.674Z
---

# Summary
https://gitlab.com/foundrynet/foundryvtt/-/issues/4090

## What changed

The `PlaceableObject` class now takes an `EmbeddedDocument` as its parameter input to the constructor, rather than an `object` and a `Scene`.

## What you need to change

- [Unverified] AbientLight, AmbientSound, Drawing, Note, Tile, Token, Wall all are affected by this.

### Research Notes

- 