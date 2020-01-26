---
title: 'Angular Ivy lazy load components'
date: 2020-01-25T00:00:00Z
draft: false
tags: ['blog', 'ivy']
author: 'Rustam'
twitter: 'irustm'
---

Loading of components in Ivy without linking to modules, thanks to the "Locality" principle

<!--more-->

### 1. Lazy load Angular components

https://medium.com/angular-in-depth/lazy-load-components-in-angular-596357ab05d8

Kevin Kreuzer
[@kreuzercode](https://twitter.com/kreuzercode)

Lazy loading modules with the Angular router!

```ts
const { QuizCardComponent } = await import('./quiz-card/quiz-card.component');

const quizCardFactory = this.cfr.resolveComponentFactory(QuizCardComponent);
const { instance } = this.quizContainer.createComponent(
  quizCardFactory,
  null,
  this.injector
);
```

---

### 2. Lazy Loading Locales with Angular

Netanel Basal [@NetanelBasal](https://twitter.com/NetanelBasal)

A simple and quick example of lazy load components with Ivy

[Demo](https://ng-run.com/edit/abHkVcNVQeDAX28ypUsD?open=app%2Fapp.component.ts)

```ts
import { Component, Type } from '@angular/core';
import { TodoComponent } from './todo/todo.component';

@Component({
  selector: 'my-app',
  template: `
    <ng-template [ngComponentOutlet]="comp | async"></ng-template>
    <button (click)="show()">Show</button>
  `
})
export class AppComponent {
  comp: Promise<Type<TodoComponent>>;

  show() {
    this.comp = import('./todo/todo.component').then(m => m.TodoComponent);
  }
}
```
