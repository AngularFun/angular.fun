---
title: 'Angular i18n & l10n with Ivy'
date: 2020-01-11T00:00:00Z
draft: false
tags: ['blog', 'ivy']
author: 'Rustam'
twitter: 'irustm'
---

Internationalization and localization are the things that are available in Angular by default.
In previous versions of Angular i18n was static and had to generate bundles for each language.
But with Angular Ivy we will be able to dynamically load the language.

<!--more-->

### 1. Internationalization with @angular/localize

https://blog.ninja-squad.com/2019/12/10/angular-localize/

Cédric Exbrayat
[@cedric_exbrayat](https://twitter.com/cedric_exbrayat)

i18n in templates, as with previous versions of Angular, with attribute i18n

```html
<h1 i18n>Hello</h1>
```

i18n with \$localize in TypeScript code.

```ts
@Component({
  template: '{{ title }}'
})
export class HomeComponent {
  title = $localize`Hi ${this.name}:name:! You have ${this.users.length}:userCount: users.`;
}
```

Runtime translations

You can load your translations in polyfills.ts with:

```ts
import '@angular/localize/init';
import { loadTranslations } from '@angular/localize';

loadTranslations({
  '1815172606781074132':
    'Bonjour {$name}\xa0! Vous avez {$userCount} utilisateurs.'
});
```

---

### 2. Lazy Loading Locales with Angular

https://www.softwarearchitekt.at/aktuelles/lazy-loading-locals-with-angular/

Manfred Steyer [@ManfredSteyer](https://twitter.com/ManfredSteyer)

Before:

main.ts

```ts
import { registerLocaleData } from '@angular/common';
import localeDe from '@angular/common/locales/de';
import localeDeAt from '@angular/common/locales/de-AT';
import localeEs from '@angular/common/locales/es';

registerLocaleData(localeDe); // de-DE
registerLocaleData(localeDeAt); // de-AT
registerLocaleData(localeEs); // es-ES
```

After:

Global locales (`global.ng.common.locales`)

```ts
 loader.loadScript('assets/de.js')
  .then(_ => this.lang = 'de-DE')

  ---

  {{ date | date:'long':'':lang }}
```

---

### 3. @locl/cli @locl/core

Oliver Combe [@ocombe](https://twitter.com/ocombe)

@locl/cli @locl/core - libraries made by Oliver Combe, author ngx-translate and ex-Angular team (contributor Anuglar i18n)

https://www.npmjs.com/package/@locl/cli

#### Locl CLI

Locl CLI - Dev tools to help you with \$localize and Angular i18n.

You can use locl extract to extract translations from your ivy application:

```
locl extract -s=dist/apps/demo/*.js -f=json -o=dist/i18n/en.json
```

Extractor support formats: xlf, xlf2, xmb, json.

#### Locl Core

Locl Core - A library with various utility functions to help you with \$localize. Loading the translations at runtime.

```ts
// Load `$localize` onto the global scope.
import '@angular/localize/init';

import { loadTranslations } from '@angular/localize';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { getTranslations, ParsedTranslationBundle } from '@locl/core';
import { AppModule } from './app/app.module';

getTranslations('/assets/i18n/fr.json').then(
  (data: ParsedTranslationBundle) => {
    loadTranslations(data.translations);
    platformBrowserDynamic()
      .bootstrapModule(AppModule)
      .catch(err => console.error(err));
  }
);
```

And this library have 2 util functions:

- getBrowserLang()
- getBrowserCultureLang()
