---
title: '🅰️ Что нам готовит Angular 13.'
date: 2021-08-18T00:00:00Z
draft: false
tags: ['blog']
author: 'Rustam'
twitter: 'irustm'
---

Пока изменений на первый взгляд не так уж и много, думаю добавят много при 13.1+2 как это было с 12. Я бы назвал что релиз Angular 13 это про окончательный дроп IE. 

<!--more-->

**Angular:**

- Поддержка number separators:

<div>Total: \${{ 1_000_000 * multiplier }}</div>

- Form status протипизировали
- Forms: Добавили методы hasValidators, addValidators, и removeValidators


**Angular/CLI:**

- webpack-dev-server upgrade в 4 версию
- deprecate deployUrl
- esbuild для оптимизации css глобальных стилей
- <script type=module во все скрипты, так как поддерживать IE больше нет необходимости, ранее был defer
- так как IE все, удалили сборку под es5, и из полифилов убрали
- extractCss убрали флаг и удалили не нужный loader, теперь css всегда extract
- inline google шрифтов тоже удалили в woff, (ну из за IE) 
- удалены: node-sass, tslint 

**Angular/components:**

- datepicker: добавлены material-date-fns-adapter и material-luxon-adapter, ранее был только moment-adapter
- CdkOverlay: расширение, поддержка больше типов, x,y
- cdk/table: добавили output event когда таблица отрендерена,
- virtualScroll: добавили флаг appendOnly, за счет чего не будут удалятся элементы из поля зрения, при необходимости.
- dialog: autofocus к определенному field

ну и много fix, которых долго ждали. 
