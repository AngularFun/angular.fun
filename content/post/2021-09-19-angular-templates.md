---
title: 'Improve Angular templates'
date: 2021-09-19T00:00:00Z
draft: false
tags: ['blog', 'compiler']
author: 'Rustam'
twitter: 'irustm'
---

Недавно собрали различные предложения по улучшению Angular templates. В ближайшем митинге их рассмотрят. Ниже я постарался собрать наиболее интересные предложения, если вы заинтересованы во внедрении этих фич думаю стоит зайти и проголосовать за них, или же написать отзывы в комменты.

<!--more-->

К сведению, есть доклад как рассматривают различные PR в Angular Team: Angular Connect [YouTube](https://www.youtube.com/watch?v=6Zfk0OcFGn4&ab_channel=AngularConnect)

Ниже я постарался собрать наиболее интересные предложения, если вы заинтересованы во внедрении этих фич думаю стоит зайти и проголосовать за них, или же написать отзывы в комменты.

**Деструктуризация различных типов объектов:** [38807](https://github.com/angular/angular/issues/38807)

```html
<div *ngFor="let { key as country, value as poi } of countryPoiMap | keyvalue">
   <div>{{ country.name }}</div>
   <div>{{ poi.name }}</div>
</div>
```

**Оператор in в шаблонах**
[38560](https://github.com/angular/angular/issues/38560)

```html
<div *ngIf="'a' in item">
  I'm expecting item is no longer of type A | B, but only A
  {{item.a}}
</div>
```

Совместимость с **ES Optional Chaining**
[34385](https://github.com/angular/angular/issues/34385)
Как вы знаете эта фича в шаблонах, появилась задолго до того как фича была в спеке.
Например выражение `a?.b` в Angular означает `null == a ? null : a.b` по спеке же `a == null ? undefined : a.b` , ну и различные изменения в этом духе.


**Внедрение директивы *ngLet**
[15280](https://github.com/angular/angular/issues/15280)
Фичу ждем уже 5й год, посмотрим. Напомню, ее реализация есть в различных либах, таких как `ngxs, rx-angular/templates, ngxf`

```html
<div *ngLet="userStream|async as user">...</div>
```

**Input Output Spreads**

[14545](https://github.com/angular/angular/issues/14545)

```html
<my-component [prop1]="prop1" {...otherInputs} ></my-component>
```

**Поддержка стрелочных функции**

[14129](https://github.com/angular/angular/issues/14129)
Сомнительное предложение как по мне. Согласен с утверждением в [коментах](https://github.com/angular/angular/issues/14129#issuecomment-275645024).

```html
<select [compareWith]="(a, b) => a.id == b.id" [(ngModel)]="selectedCountries"> ...
```

**Деструктуризация значений из *ngIf as** 
[27555](https://github.com/angular/angular/issues/27555)

```html
<div *ngIf="thingAndOther$ | async as {thing, other}">
```

**Cold event streams**
[https://github.com/angular/angular/issues/13248](https://github.com/angular/angular/issues/13248)
Предложение настолько устарело, что синтаксис компонентов еще с Angular 2 beta. Суть предложения, создать EventStream только в момент подписки, особенно ок для Output, и + не будет лишний раз дергать CD. В целом похож на одно из предложении из `rx-angular/templates` Так же в комментах указали на одну либу, пример:

```html
<button (*click)="clicks$; $event = 'custom payload'">Click Me</button>
```

```ts
@Component({...})
export class MyComponent implements OnInit {
  @ObservableEvent()
  clicks$: Observable<any>;

  ngOnInit() {
    // we can either manually subscribe or use the async pipe
    this.clicks$.subscribe(console.log);
  }
}

```

Полный список предложений: [43485](https://github.com/angular/angular/issues/43485)
