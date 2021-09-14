---
title: 'ng-app-counter - Измерьте количество классов в Angular'
date: 2020-09-13T00:00:00Z
draft: false
tags: ['blog']
author: 'Rustam'
twitter: 'irustm'
---

<!--more-->

ngast - удобный API парсера для проектов Angular, недавно обновился на ngtsc  (TraitCompiler).

Поэтому пришло время обновить тулзу для подсчета Angular сущностей. 
В основе ngast + guessjs


Измерить свой проект можно запустив:

```
npx ng-app-counter -p ./project/tsconfig.app.json
```