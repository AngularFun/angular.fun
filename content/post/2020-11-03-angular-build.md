---
title: 'Build Large Angular apps'
date: 2020-11-03T00:00:00Z
draft: false
tags: ['blog']
author: 'Rustam'
twitter: 'irustm'
---

В статье статистика на чем сейчас собирают внутри, и какие инструменты есть для сборки Angular приложений.

<!--more-->


Статья: [Nx: On Bazel Support](https://blog.nrwl.io/on-bazel-support-6be3b3ceba29)

**Computation Caching**

Основной фишкой внедрения Bazel в Nx была реализация распределенной сборки, но эта задача была полностью решена на стороне Nx, поэтому Bazel в Nx не будет.

Bazel and Angular

Действительно после ухода Alex Eagle из Google, больше нет никого кто бы работал с rules_nodejs из Google, Angular в частности, и поэтому как считает Victor Savkin, Bazel team не будут уделять большое внимание на частные проблемы при интеграции с Angular. Считаю что это не так, поскольку Bazel(rules_nodejs) для сборки используется в самом Angular, и других крупных проектах: TensorflowJs, Selenium, NgRX, и тд.

Кстати, недавно появился rules_python.

Bazel or Nx

Bazel хорошая технология, но плохо работает с js тулами. В статье много отсылок к тому что с Nx можно собирать что угодно, и сделать какой угодно сборщик зная в основном только TS.


**Опрос по сборщикам Angular:**

{{< telegram "ngFanatic/189"  >}}

-----

**Что насчет поддержки различных сборок?**

В настоящее время каких либо [планов](https://github.com/angular/angular-cli/issues/18599#issuecomment-678771369) чтобы отклониться у Angular team от webpack нет, и реализуется поддержка новой версии. Но тем не менее есть поддержка rollup от сообщества Angular Ivy, rollup-plugin-ngc (Есть и старший брат - angular2-rollup , но проект сейчас не поддерживается, для примера реализации собственного сборщика ок)

У rollup-plugin-ngc под капотом:

```ts
import { createProgram, CompilerHost  } from '@angular/compiler-cli'
```

Конечно, этот плагин практически ничего не дает, для крупных приложений, но для Angular Elements почему бы нет? Тут даже [пример есть](https://github.com/aelbore/rollup-plugin-ngc/tree/master/example)

Есть у некоторых идеи еще попробовать сделать сборщик на основе vite, ориентирован прежде всего на vue и react. Вроде обещает быть быстрым, который в свою очередь соревнуется с Snowpack.

Напишите в комментариях, довольны ли временем сборки вашего проекта?
(Если нет, то можете следить за этим issue)


------

**Inspector for Angular**

Для тех у кого пересборка serve занимает значительное время, Netanel Basal недавно представил новую утилиту - inspector. Возможность изменения inputs, outputs property в специальном окне.

Features
- Inspect Angular components on the fly
- Change component properties without touching the code
- Simulate Angular events
- See the results in realtime