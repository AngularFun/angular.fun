---
title: 'Angular Deno render engine'
date: 2020-07-03T00:00:00Z
draft: false
tags: ['blog']
author: 'Rustam'
twitter: 'irustm'
---

В Deno сообществе почему то решили что для SSR удобнее всего брать React, и конечно внедрили JSX как часть Deno.core. И даже появились фреймворки на этой основе. Angular остался немного в сторонке в очередной раз =) 

Поэтому решил совместить deno + angular: [Angular Deno Renderer Engine](https://github.com/alosaur/angular_deno).
Сейчас он больше готов для использования как любой другой template engine, например как тот же Handlebars, ejs и тд.

Сравнение Angular Universal на NodeJS и Deno по времени рендеринга (хотя это практически ничего и не значит):

- Deno 1.1.2 Angular JIT:  3-8ms
- Node 12.18.2 Angular JIT:  15-24ms
- Node 12.18.2 Angular AOT: 5-13ms

Если у сообщества Angular будет достаточный интерес к этому проекту, то далее буду внедрять:
- Transfer State, (чтобы был прям настоящий SSR)
- Compiler на Deno, для AOT
- Поддержка различных препроцессоров
- Пререндер как в Scully
- HMR 

https://github.com/alosaur/angular_deno