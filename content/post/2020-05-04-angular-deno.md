---
title: 'Angular dev with deno'
date: 2020-05-04T00:00:00Z
draft: false
tags: ['blog']
author: 'Rustam'
twitter: 'irustm'
---

Вот и Angular добрался до deno. A именно, [dev_server](https://github.com/zhmushan/dev_server). 

Как он работает? 
При обращении на index.html, запрашивается main.ts, 

Он же транспаилится (в соответствии с заданным tsconfig), и выдает готовый js код по этому url.

Что с зависимостями?

А вот тут конечно приходит на помощь jspm, cdn.pika, unpkg. Поэтому вам придется указать соответствующий importmap.

В итоге у вас на выходе JIT как оно и было во времена systemjs v0.. и 5мб кода. 
Из плюсов, вам не придется ждать пока произойдет dev сборка у вас в консоли.

Но тут конечно же не учтены различные препроцессоры, о которых сейчас большинство из нас  даже не задумывается, используя Angular CLI

Как попробовать:

```
deno run --allow-net --allow-read --allow-write --unstable https://deno.land/x/dev_server/mod.ts --template angular
```

(Предварительно должен быть установлен Deno не ниже v0.42.0)