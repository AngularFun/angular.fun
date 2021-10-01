---
title: 'Как писать на HTML Canvas удобно, или как изобрести очередной renderer на Angular.'
date: 2021-10-01T00:00:00Z
draft: false
tags: ['blog', 'canvas', 'angular canvas']
author: 'Rustam'
twitter: 'irustm'
---

<!--more-->

![Canvas angualar](/post/images/angular-canvas.png)

Разработка на canvas с контекстом 2D обычно не предполагает никаких сложностей. Для начала необходимо изучить пару десятков встроенных методов [WEB API CanvasRenderingContext2D](https://developer.mozilla.org/ru/docs/Web/API/CanvasRenderingContext2D), прочитать [рекомендации по оптимизации](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial/Optimizing_canvas) , вспомнить школьный курс геометрии. И на этих базовых вещах можно уже строить неплохие приложения на canvas. 

Как один из вариантов начала разработки на canvas: из примитивов фигур строят элементы, затем их объединяют в функцию, эти функции складывают в готовый элемент,  объединяют их в слой, ну и в конце уже отдают в функцию рендера. Все еще звучит довольно неплохо и с этим можно даже жить, если использовать чистые функции, и придерживаться везде этого подхода. Но не всегда этого удается, всегда есть соблазн выхватить что-либо из контекста. Для примера приведу [код](https://github.com/evanw/source-map-visualization/blob/gh-pages/code.js) из source-map-vizualization [https://evanw.github.io/source-map-visualization/](https://evanw.github.io/source-map-visualization/) замечательный инструмент, сделанный на canvas. Только чтобы понять весь код и привнести какие либо исправления, я думаю придется посидеть не один час. 

Типичный проект на canvas+Angular может выглядеть так, как описано в статье: [How to get started with Canvas animations in Angular](https://medium.com/angular-in-depth/how-to-get-started-with-canvas-animations-in-angular-2f797257e5b4). Достаточно хорошо, если вам необходимо рисовать мало элементов:

```tsx
import { Component, ViewChild, ElementRef, OnInit } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <canvas #canvas width="600" height="300"></canvas>
    <button (click)="animate()">Play</button>   
  `,
})
export class AppComponent implements OnInit {
  @ViewChild('canvas', { static: true })
  canvas: ElementRef<HTMLCanvasElement>;  
  
  private ctx: CanvasRenderingContext2D;

  ngOnInit(): void {
    this.ctx = this.canvas.nativeElement.getContext('2d');
  }
  
  animate(): void {
    // draw function
  }
}
```

Постепенно такой код всегда стремится к большей степени не поддерживаемости. Так случилось и у меня, как я пришел на проект, где повсюду использовался canvas + Angular, к тому уже с большим легаси на тот момент. Само собой скорость разработки уменьшилась, все нужно было глобально рефакторить. После недолгого погружения в проект сформировались требования для рефакторинга.


### Требования для удобного использования HTML Canvas

- Чистые функции
- Поддержка использования обобщенного кода функции
- Возможность использовать старый код в новом подходе
- Поддержка перерисовки в одном canvas context
- Поддержка анимации
- Поддержка listeners для элементов рисования
- Поддержка инфраструктуры Angular, (Input, Output, OnInit, OnDestroy )

Так как уже был опыт создания custom renderer для Angular: Статья [Делаем кроссплатформенное нативное десктоп приложение на Angular](https://habr.com/ru/post/480396/), было решено что делаю очередную реализацию Angular renderer.
Из существующих решений Angular Canvas renderer в open source, большинство выглядело простым экспериментом, и не было упоминаний что где-то на проде его юзают. К тому же эти решения подменяют полностью DefaultDomRenderer, а хотелось использовать Canvas Renderer только для определенных компонентов, дабы не сломать своим рендерером остальной функционал приложения.
В этой статье не буду рассказывать как создавать свои renderer под Angular, выше в статье уже описывал процесс создания.

### Вышел: AngularCanvasRenderer

Для того чтобы использовать этот рендерер только в нужных для нас местах, необходимо отметить компонент через декоратор **@CanvasComponent.** В свою, очередь этот декоратор унесет метаинфу по компоненту в мапу, и рендерер будет понимать какой все-таки использовать renderer при выполнения инструкции из шаблона.

**Component:**

```tsx
@CanvasComponent
@Component({
  selector: 'app-graph-canvas-example',
  templateUrl: './graph-canvas-example.component.html',
  styleUrls: ['./graph-canvas-example.component.scss'],
})
```

**Template:**

```xml
<p>Graph example</p>

<canvas class="first">
  <rect [x]="mouseX" [y]="20" w="20" h="20"></rect>
  <line *ngFor="..." [x1]="10" [x2]="100" [y1]="10" [y2]="200"></line>
</canvas>
<canvas>
  <graph-line
    [data]="data"
    [step]="step"
    [deltaX]="deltaX"
    strokeStyle="orange"
  ></graph-line>
  <graph-line [data]="data3" [deltaX]="deltaX" strokeStyle="blue"></graph-line>
</canvas>
```

Для поддержки "компонентного" подхода внутри canvas, и внедрение чистых функции в шаблонах, как в примере выше, были придуманы так называемые **CanvasElement,** которые поддерживают **selector** внутри canvas элемента.

```tsx
import { CanvasElement, NgCanvasElement, NgCanvas } from 'angular-canvas';

@CanvasElement({
  selector: 'graph-line'
})
export class GraphLineElement implements NgCanvasElement {
    // parent element
    public parent: NgCanvas;

    // canvas element redraw until all NgCanvasElement needDraw as true
    public needDraw: boolean;

    setNgProperty(name: string, value: any): void {
      this[name] = value;

      // redraw all element in one canvas context after set ng property
      this.parent.drawAll();
    }

    draw(context: CanvasRenderingContext2D, time: number): void {
      context.strokeStyle = 'red';
      // ...
    }
}
```

**CanvasElement** должен реализовать интерфейс **NgCanvasElement.** По большей части интерфейс состоит из методов, которые напрямую выполняются из renderer. Например, можно придумать систему стилей для canvas элементов и изменять их через метод `setStyle?(style: string, value: any, flags?: RendererStyleFlags2): void;`
и `<graph-line style="color: green">`

но на практике не используется.

**parent: NgCanvas;**

Родительский элемент в котором регистрируются дочерние элементы

**needDraw: boolean;**

Атрибут который отвечает за продолжения цикла рисования, используется при анимациях.

**setNgProperty(name: string, value: any): void**

Метод при котором значения попадают через Input шаблона при changeDetection. 
*parent.drawAll()* в этом случае вызывает перерисовку всех элементов в текущем canvas слое.

**draw(context: CanvasRenderingContext2D, time: number)**

Метод в котором ведется отрисовка canvas примитив.

**Output event**

Для организации Output необходимо реализовать интерфейс рендерера addEventListener.

Пример реализации hover на элемент:

```html
<canvas>
    <triangle
      [x]="mouseX"
      [y]="mouseY"
      (hover)="hover($event)"
    ></triangle>
</canvas>
```

 

```tsx
private callbackFunc: {
    hover?: () => {};
  } = {};

addEventListener(eventName: string, callbackFunc) {
   this.callbackFunc[eventName] = callbackFunc;
}
```

Проверку принадлежности координат мыши в элемент необходимо реализовывать самому, вспомнив курс геометрии. Благо легко входишь во вкус.

Полный пример: [triangle.element.ts](https://github.com/irustm/angular-canvas/blob/master/src/app/demos/elements/triangle.element.ts)

Еще больше методов в readme проекта: [angular-canvas](https://github.com/irustm/angular-canvas)

---

### NgCanvas

В схеме ниже описан механизм работы Angular Canvas Renderer:

![Renderer](/post/images/renderer.png)

Где в CanvasComponent:

```tsx
<canvas>   NgCanvas
   <canvas-element></canvas-element>   CanvasElement
```

----

### Последствия

**AngularMaterial**

В Angular Material сильно используется Angular Animation, и так как BrowserAnimationModule заменяет дефолтный рендерер, начинаются конфликты. 

Первым простым решением было подрубить NoopAnimationModule, и отказаться от плавной анимации AngularMaterial, что выглядело вполне интересно. Но конечно же в Angular предусмотрели и этот случай (но не публичный API, не думаю что кому то еще в голову это пришло): Можно подменить родительский **DomRenderer** своим же (благо интерфейсы совпадают) на моменте создания через **AnimationRendererFactory**

**WebGL, WebGPU**

Сейчас модель использования, зашита для использования контекста только под 2d, ничего не ограничивает сделать в NgCanvas `canvas.getContext("webgpu");` и это тоже будет работать.

**NodeGUI (Qt), NativeScript**

Так как эти решения таким же образом подменяют рендерер, нет ничего простого занести в них еще один. В принципе любой рендерер поддерживается, главное чтобы исполняемый хост имел возможность работать с Canvas.

**Год в проде**

Изначально эксперимент являлся лишь моим pet project, который я делал в свободное от работы время. Для демонстрации возможностей была создана простейшая игра и обычные графики. Эта попытка доказать что подобные рендереры облегчают жизнь вскоре была взята на вооружение и уже год как работают в проде.

Теперь большинство сложных интерфейсов разрабатываются только на нем. Элементы ввода рисуются в обычном DOM, так как CanvasComponent это не только про CanvasRenderer, но и про поддержку DefaultDomRenderer в нем же.

angular-canvas [3,4 кб](https://bundlephobia.com/package/angular-canvas@0.3.0) 

**Ссылки:**

Github: [https://github.com/irustm/angular-canvas](https://github.com/irustm/angular-canvas)

Демка игры из превью: [Game](https://irustm.github.io/angular-canvas/#/game)

Демка графики: [Graph](https://irustm.github.io/angular-canvas/#/demo)