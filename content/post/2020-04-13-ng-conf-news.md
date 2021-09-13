---
title: 'Что было на ngConf'
date: 2020-04-13T00:00:00Z
draft: false
tags: ['blog']
author: 'Rustam'
twitter: 'irustm'
---

А теперь немного выдержки того что было на ng-conf из нового

<!--more-->


▪️ HOC
```ts
export function extend(aComponent: AComponent) {
   class DynamicComonent implements OnInit { /* .. */};
   DynamicComonent.ɵcpm = ɵɵdefineComponent ({/* wraps AComponent */});
  return DynamicComonent;
}
```

▪️ Standalone Components, Dependencies are provided directly in directiveDefs fields

```ts
export class AComponent {/* ... */}
AComponent.ɵcpm = /* private API */
AComponent.ɵcpm.directiveDefs = [OtherComponent.ɵcmp];
```

▪️ Angular 9 Universal 

`ng run app:prerender`

▪️ RxJS: ⚠️  toPromise() deprecated, use lastValueFrom(source$)

▪️ RxJS: New flag resetOnSuccess

```ts
pipe(retry({
   count: 3,
   resetOnSuccess: true
})
```

▪️ NgRx: variable in observable

```html
<ng-container *ngrxLet="obs$ as n">
```

▪️ NgRX: New Reactive Router (experimental)

```html
<ngrx-router>
  <ngrx-route path="/blog/posts/:id" [component]="blogPostComponent"></ngrx-route>
</ngrx-router>
```