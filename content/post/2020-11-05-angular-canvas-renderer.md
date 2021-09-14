---
title: 'Angular Canvas Renderer'
date: 2020-11-05T00:00:00Z
draft: false
tags: ['blog']
author: 'Rustam'
twitter: 'irustm'
---

Рад представить вам еще одну свою поделку на Angular:

🎨 Angular Canvas Renderer

<!--more-->

Может:

- Кастомные canvas элементы и компоненты
- Перерисовка в одном canvas контексте
- Поддержка inputs and outputs, attributes, styles
- EmulatedEncapsulation по умолчанию
- И самое главное: быстрая миграция вашего canvas кода на компонентный подход

Ну и чтобы проверить реализацию, была написана мини игрушка, исключительно рисованием на canvas. Демо

В демке примерно такой template

```html
<canvas>
  <background></background>
  <cactus></cactus>
</canvas>

<canvas>
  <cat
    #catElement
    [x]="x"
    [y]="y"
    (x)="updateUnitX($event)"
  ></cat>
</canvas>
```

⭐ → https://github.com/irustm/angular-canvas