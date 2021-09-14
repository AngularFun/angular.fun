---
title: 'Angular push pipe for optimization'
date: 2020-11-09T00:00:00Z
draft: false
tags: ['blog']
author: 'Rustam'
twitter: 'irustm'
---

Angular Push Pipe - новый способ оптимизации работы Angular приложения без работы zone.js c включенным в проект zone.js 
Обзор 2 статей.


<!--more-->

https://indepth.dev/angulars-push-pipe-part-1

Как вы знаете вся магия работы Angular работает исключительно за счет zone.js. (Для тех кто не в курсе, тут есть замечательное объяснение на русском - zone js от а до я). В статье  рассказывается за счет чего работает тот же async pipe, и как собственно работает changeDetection. Приводится много аргументов того что async pipe будет недостаточно производительным в приложениях, где идет большой объем данных, за счет того что этот pipe pure: false и сам внутри смотрит latestValue и вызывая changeDetectionRef.markForCheck() отмечая связанный компонент как dirty (следующее обнаружение изменения zone.js запустит рендеринг). Так же приводит факт того что об этом уже неоднократно говорили и выступали на разных конфах Victor Savkin, Rob Wormald и другие ex-Angular team. (Тут же вопрос у меня, почему бы не занести это в angular/core если все знали, не понятно, Upd - в Angular team не хотят вносить доп функционал из либ [rxjs]). 

Michael Hladky предлагает использовать detectChanges, для немедленного реагирования, и позволяя тем самым исключить работу с zone.js.

Пример реализации этого pipe - push 

```ts
@Component({
selector: 'app-display',
template: `
  {{observable$ | push}}
`
})
export class DisplayComponent {
  observable$ = of(1, 2, 3);
}
```

Но в данном случае этот код запустит detectChanges аж 3 раза, что наоборот увеличит нагрузку. Решение этой проблемы с помощью pipe функции  coalesceWith(animationframes)  которая пересылает последнее значение один раз в кадре анимации.

Много слов выше, просто стоит попробовать:

`npm i @rx-angular/template`

или прочитать [серию статаей](https://indepth.dev/angulars-push-pipe-part-1) от Michael Hladky.

Ну и возможно будет интересно взглянуть на внутренности, самого rx-angular, ведь все они рассчитаны на улучшение производительности.

-----

**rx-angular/templates: rxLet**

Директива *rxLet, по сути такой же как и *ngrxLet но с некоторым отличием. Дополнительные каналы RxJS, next, error, complete и бонусом suspense когда еще ничего не определено.

```html
<ng-container *rxLet="hero$; let hero; rxSuspense: suspenseView; rxError: errorView; rxComplete: completeView">
        {{hero.name}}
<ng-container>
<ng-template #suspenseView>Loading...</ng-template>
<ng-template #errorView>Error!</ng-template>
<ng-template #completeView>Complete.</ng-template>
```

-----

**x-angular/templates: unpath**

UnpatchEventsDirective - для частичной деактивации NgZone, а также избавления от ненужных рендеров через патчи addEventListener зоной. Его можно использовать с любым элементом, к которым вы применяете привязки событий.

Проблемный код:
`<div (mousemove)="doStuff($event)">Hover me</div>`
Проблема в том, что каждое событие, зарегистрированное через (), например (mousemove) (или custom @Output ()) помечает компонент и всех его предков как dirty и повторно перерисовывает все дерево компонентов. Таким образом, даже если ваш eventListener вообще не связан с какими-либо изменениями, ваше приложение повторно перерендерит все дерево компонентов. Это может привести к очень плохому взаимодействию с пользователем, особенно если вы работаете с часто запускаемыми событиями, такими как mousemove.

Директива unpatch решает эту проблему удобным способом:

```html
<button [unpatch] (click)="triggerSomeMethod($event)">click me</button>
<button
  [unpatch]="['mousemove']"
  (mousemove)="doStuff2($event)"
  (click)="doStuff($event)"
>
  click or hover me
</button>
```

-----
**rx-angular/templates: render strategy**

Дополнительный property - strategy. Есть в push pipe и rxLet directive. 

RenderStrategies можно рассматривать как основу уровня оптимизации производительности. 

Local Strategy - эта стратегия рендерит текущий компонент и все его дочерние элементы, которые находятся на пути, отмеченном как dirty или имеющем компоненты с ChangeDetectionStrategy.Default На первый взгляд может показаться, что это тоже самое что вызов detectChanges, но тут он имеет отличия, а именно группировку событий по компоненту по которому был вызван change detection.

Global Strategy - markForCheck на стероидах, может работать без зоны.

Noop - Ничего не детектит и не отрисовывает, может быть полезен для отладки, и в разных оптимизациях.

Native - markForCheck который отрендерит только в следующем Application.tick, т.е по сути async pipe.