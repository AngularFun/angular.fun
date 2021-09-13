---
title: 'Делаем Angular снова меньше!'
date: 2020-04-12T00:00:00Z
draft: false
tags: ['blog']
author: 'Rustam'
twitter: 'irustm'
---

Делаем Angular снова меньше!

Интересная попытка скрестить lit-element и angular elements PolymerLabs/angular-element

Которая к тому же поможет избавиться от огромных бандлов в стандартной обертке AngularElements.

На данный момент следуя стандартному руководству в angular.io конечный бандл Angular Element будет занимать 65kb.

Для сравнения, бандл lit-element весит 6.98kb ([All the Ways to Make a Web Component](https://webcomponents.dev/blog/all-the-ways-to-make-a-web-component/) - конечно же Angular не попал в эту сравнительную таблицу).
Как я уже [писал](https://t.me/ngFanatic/159) можно предельно ужать рантайм и в 256b

Но если у вас нет желания писать свои обертки для Angular Elements, то можно воспользоваться [ngx-elements](https://github.com/aelbore/ngx-elements) который будет давать в итоге 6кб.
