---
title: '🌿 Плоды Angular Ivy '
date: 2021-08-18T00:00:00Z
draft: false
tags: ['blog']
author: 'Rustam'
twitter: 'irustm'
---

Плоды Angular Ivy начинают радовать. Еще больше динамики!

<!--more-->


В настоящее время чтобы реализовать динамические компоненты в Angular есть 2 пути:

- Используя JIT Compiler, т.е. из любой строки создать компонент. Имеет в себе много недостатков, поэтому практически никто таким не занимается на проде.
- Используя ViewContainerRef, и ему подобные API, которая на самом деле не предоставляет достаточной гибкости динамического создания представлений из смеси статического HTML.

И вот появилось [предложение](https://github.com/angular/angular/issues/43120) для создания таких компонент, Imperative View & Template Composition APIs.

Пример, как это может быть

```ts
import {createView, html, component, directive, text, binding, template} from "@angular/core/compose";
import {MyYellowComponent} "./myYellow.component";

const myCustomView = await createMyCustomView({userName: 'Kahless'});

async function createMyCustomView(myContext) {
 return createView([
  html("<div>some static html,</div>"),
    component(MyYellowComponent, { projection: [
      text("html string followed by a binding"),

      // {{ userName }}
      binding(() => myContext.userName),
...
}
```


На самом деле, это скорее будет скрыто под каким либо wrapper, никому не охота писать в стиле Incremental Dom, я думаю. Возможности конечно радуют.

p.s. Такое можно уже делать, используя инструкции Ivy, через Θ, но никто ничего не гарантирует =)