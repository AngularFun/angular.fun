---
title: 'Что будет в Angular 12'
date: 2021-02-18T00:00:00Z
draft: false
tags: ['blog']
author: 'Rustam'
twitter: 'irustm'
---

Что нас ждет в Angular 12. Заметки из последних changelog и плана релиза.

<!--more-->

✨ Forms:

- Улучшение min, max валидаторов, ломает html min, max аттрибуты в input, теперь Form начнет триггерить только если в input накинули formControl
- Опция emitEvent добавлена в AbstractControl, поэтому поломаются 


FormGroup.addControl, FormGroup.removeControl, FormGroup.setControl, FormArray.push, FormArray.insert, FormArray.removeAt, FormArray.setControl, FormArray.clear


но миграция ng update их устранит. Если только вы не делали своих наследников от AbstractControl и далее.

✨Http:

- Наконец то затипизировали http статусы и сделали их читаемыми
- Зафиксили и добавили request timeout, до 12 версии видимо никого это не беспокоило, хотя это еще добавили в path версию 11.

✨language-service:

- 5 улучшений, интеграция в vscode будет работать немного лучше в части templates.

✨common:

- ICU для DatePipe

Пока все, будем ждать обновленный roadmap и более детальное описание релиза.

-----

Angular CLI тем временем тоже идет к 12 версии. Уже сейчас доступна предрелизная версия 12.0.0-next.1

Из интересного:

- Поддержки npm 7 пока НЕ будет. Команда пытается интегрироваться, но фичу блокируют баги самого npm (один, два)
- Добавлена поддержка Webpack 5 (привет, Module Federation!)
- Strict mode при создании приложения или воркспейса по умолчанию включен

Breaking Changes

- Минимально-поддерживаемая версия Node.js — 12.13
- Удалена поддержка Zone.js 0.10
- Минимально-поддерживаемая версия Karma — 6.0.0

-----

- APP_INITIALIZER научили работать с Observable
- Контракт router-outlet стал публичным, что упростит создание его кастомных имплементаций
- Добавлена поддержка отключения анимации через BrowserAnimationsModule.withConfig
- number и boolean теперь разрешены в качестве http-параметров
- Методы onPopState и onHashChange класса PlatformLocation теперь возвращают функцию, которую можно вызвать для удаления подписки. Благодаря этому исправлено создание лишних подписок при повторном бутстрапинге приложения.

Angular CLI не отстает:

- Объявлена поддержка npm 7, а если быть точнее то npm@7.5.6 и выше
- Флаг inlineCritical установлен в true по умолчанию
- Имплементирован новое свойство defaultConfiguration в angular.json. Это может избавить от передачи лишних флагов при сборке проекта. Например, если указать "defaultConfiguration": "production" , то продуктовую сборку можно запустить уже без флага --prod
- В свою очередь флаг --prod признан устаревшим. Вместо него следует использовать флаг --configuration
- IE 11 признан устаревшим и его поддержка будет удалена в следующих релизах


-----

- common/http: Появился класс HttpContext и HttpContextToken для запросов HttpClient, 4 года ждали эту фичу. Например теперь можно будет дополнительно обработать запрос в interceptor, согласно заданному контексту. Пример

- common: Удалены не используемые методы в DomAdapter, и если у вас были какие либо специфичные вещи связанные с DominoAdapter то кажется у вас будут проблемы с SSR, например у меня это в angular_deno

- compiler: :host-context c нормальным multiple наконец то таки, влита уже в 11, как бага

- platform-browser: XhrFactory  @angular/common/http мигрировал в @angular/common.  Необходимо чтобы можно было настроить токен XhrFactory на уровне платформы, и не тянуть зависимости, а в browser только BrowserXhr  Автоматическая миграция должна пофиксить.

- compiler: пересборка JIT должна стать чуть быстрее за счет переиспользуемых компонентов

- common: historyGo в Location service, забавно что принимает number, то есть можно уйти как на 2 шага назад historyGo(-2) так и вперед historyGo(10), но если не сможет, то просто не сработает без всякой ошибки
- доступна опция forwardRef в  @Injectable({ providedIn: forwardRef(() => MyModule) })

- compiler: появился хук transformResource, а это означает что можно писать свои препроцессоры для стилей, с удобным интерфейсом ResourceHostContext, для меня это означает что можно подрубить легко sass для angular_deno

- compiler: nullish coalescing доступна в Angular templates: {{ a ?? b ?? c}}

- typescript 4.2

-----------

BREAKING

- core: Флаг emitDistinctChangesOnly по умолчанию будет выставлен false в ContentChildren, ViewChildren, а так же будет помечен как deprecated. Неободим так как QueryList.changes вызывался лишний раз.

- animation fix: очистка dom элементов после разрушения AnimationEngine, а это в основном когда root удален, по факту может повлиять на ваши тесты, если вы после destroy проверяете dom элемент

- core: больше нет поддержки node v10, перешли на использование node v14, минималка v12.14.1

- ng_packagr: больше не генерит минифицированный umd


-----

C 12 версии Stylus будет deprecated.

А еще в Angular Material будет переход на sass модули @use вместо @import Если вы используете Angular Material то можете попробовать:
ng g @angular/material:themingApi


-----

Традиционный пост на medium:
https://blog.angular.io/angular-v12-is-now-available-32ed51fbfd49

-----

По поводу изменений поговорили про Angular 12 на NgRuAir:  https://www.youtube.com/watch?v=F3FO4I3anQQ