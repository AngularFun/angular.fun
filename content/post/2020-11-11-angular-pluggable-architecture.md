---
title: 'Pluggable Angular Architecture with Webpack 5 Module Federation'
date: 2020-11-11T00:00:00Z
draft: false
tags: ['blog']
author: 'Rustam'
twitter: 'irustm'
---

Реализация плагинной архитектуры с Angular 11 станет еще более удобной, с использованием фич Webpack 5. В этом примере Alexey Zuev показывает как это можно будет применить.

<!--more-->
Pluggable Angular Architecture with Webpack 5 Module Federation

2 года назад [я рассказывал](https://www.youtube.com/watch?v=aTnZyu53zSE) на одном из митапов Angular Moscow, про плагинную архитектуру (youtube) как это было во времена AngularJS, и как должно было быть круто с приходом Angular IVY. И вот этот день можно сказать почти настал.

https://github.com/alexzuza/angular-plugin-architecture-with-module-federation

Реализация плагинной архитектуры с Angular 11 станет еще более удобной, с использованием фич Webpack 5. В этом примере Alexey Zuev показывает как это можно будет применить.

В основе как всегда есть 3 слоя:
- config плагинов
- loader service
- render

Но прежде чем загрузить и показать что то, нужно создать что то.
Для начала необходимо добавить в angular.json конфиг вебака
 `"extraWebpackConfig": "webpack.config.js"`

И разметить shared области, чтобы не дублировать код angular/core и остального в плагинах.

```ts
new ModuleFederationPlugin({
      shared: {
        '@angular/core': {
          singleton: true,
        },
// и тд
```

Для создания плагинов будем использовать другой webpack.config и другой проект, в рамках angular library.
В данном случае 2 плагина будут жить в одном бандле.

```ts
new ModuleFederationPlugin({
      name: 'plugins',
      library: { type: 'var', name: 'plugins' },
      filename: 'remoteEntry.js',
      exposes: {
        './Plugin1': './projects/plugins/src/app/plugin1/plugin1.component.ts',
        './Plugin2': './projects/plugins/src/app/plugin2/plugin2.component.ts'
      },
      shared: {
        '@angular/core': {
          singleton: true,
```

Ну и сам loader service разместившийся в main project (plugin-loader.service.ts) :

```ts
async load<T>(pluginName): Promise<Type<T>> {
    const { name, mfEntry, exposedModule, ngModuleName } = config[pluginName];
    await loadRemoteEntry(mfEntry);
    await __webpack_init_sharing__('default');
    const container = window[name] as Container;

    // Initialize the container, it may provide shared modules
    await container.init(__webpack_share_scopes__.default);
    const factory = await container.get(exposedModule);
    const Module = factory();

    return Module[ngModuleName];
  }
```

Cоздание компонента после загрузки (app.component.ts): 

```ts
loadPlugin(pluginName: string): void {
    this.pluginLoader.load(pluginName).then((component: Type<any>) => {
      const compFactory = this.factoryResolver.resolveComponentFactory(
        component
      );
      this.vcRef.createComponent(compFactory);
    });
  }
```
