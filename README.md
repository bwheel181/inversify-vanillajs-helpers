# inversify-vanillajs-helpers

[![Join the chat at https://gitter.im/inversify/inversify-vanillajs-helpers](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/inversify/inversify-vanillajs-helpers?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Build Status](https://secure.travis-ci.org/inversify/inversify-vanillajs-helpers.svg?branch=master)](https://travis-ci.org/inversify/inversify-vanillajs-helpers)
[![Test Coverage](https://codeclimate.com/github/inversify/inversify-vanillajs-helpers/badges/coverage.svg)](https://codeclimate.com/github/inversify/inversify-vanillajs-helpers/coverage)
[![npm version](https://badge.fury.io/js/inversify-vanillajs-helpers.svg)](http://badge.fury.io/js/inversify-vanillajs-helpers)
[![Dependencies](https://david-dm.org/inversify/inversify-vanillajs-helpers.svg)](https://david-dm.org/inversify/inversify-vanillajs-helpers#info=dependencies)
[![img](https://david-dm.org/inversify/inversify-vanillajs-helpers/dev-status.svg)](https://david-dm.org/inversify/inversify-vanillajs-helpers/#info=devDependencies)
[![img](https://david-dm.org/inversify/inversify-vanillajs-helpers/peer-status.svg)](https://david-dm.org/inversify/inversify-vanillajs-helpers/#info=peerDependenciess)
[![Known Vulnerabilities](https://snyk.io/test/github/inversify/inversify-vanillajs-helpers/badge.svg)](https://snyk.io/test/github/inversify/inversify-vanillajs-helpers)

[![NPM](https://nodei.co/npm/inversify-vanillajs-helpers.png?downloads=true&downloadRank=true)](https://nodei.co/npm/inversify-vanillajs-helpers/)
[![NPM](https://nodei.co/npm-dl/inversify-vanillajs-helpers.png?months=9&height=3)](https://nodei.co/npm/inversify-vanillajs-helpers/)

Some helper for the development of InversifyJS applications with VanillaJS

## Installation
```
$ npm install inversify-vanillajs-helpers
```

## Annotation helper
Helps you to reduce annotation boilerplate when working with VanillaJS so instead of writting:

```js
inversify.decorate(inversify.injectable(), Ninja);
inversify.decorate(inversify.inject(TYPES.Katana), Ninja, 0);
inversify.decorate(inversify.inject(TYPES.Shuriken), Ninja, 1);
```
You can just write:

```js
helpers.annotate(Ninja, [TYPES.Katana, TYPES.Shuriken]);
```

Let's take a look to an example:

```js
var inversify = require("inversify");
var helpers =  require("inversify-vanillajs-helpers");
require("reflect-metadata");

var TYPES = {
  Ninja: 'Ninja',
  Katana: 'Katana',
  Shuriken: 'Shuriken'
}

class Katana {
  hit () {
    return 'cut!'
  }
}

helpers.annotate(Katana);

class Shuriken {
  throw () {
    return 'hit!'
  }
}

helpers.annotate(Shuriken);

class Ninja {

  constructor(katana, shuriken) {
      this._katana = katana;
      this._shuriken = shuriken;
  }

  fight () { return this._katana.hit() }
  sneak () { return this._shuriken.throw() }

}

helpers.annotate(Ninja, [TYPES.Katana, TYPES.Shuriken]);

// Declare bindings
var kernel = new inversify.Kernel()
kernel.bind(TYPES.Ninja).to(Ninja);
kernel.bind(TYPES.Katana).to(Katana);
kernel.bind(TYPES.Shuriken).to(Shuriken);

// Resolve dependencies
var ninja = kernel.get(TYPES.Ninja);
console.log(ninja.fight(), ninja.sneak());
```
## Named annotations

```js
helpers.annotate(
    Ninja,
    [
        { type: TYPES.Katana, named: "not-throwable" },
        { type: TYPES.Shuriken, named: "throwable" }
    ]
);
```

## Tagged annotations

```js
helpers.annotate(
    Ninja,
    [
        { type: TYPES.Katana, tagged: { key: "throwable", val: false } },
        { type: TYPES.Shuriken, tagged: { key: "throwable", val: true } }
    ]
);
```

## Registration helper
Helps you to reduce annotation and registration boilerplate when working with VanillaJS so instead of writting:

```js
inversify.decorate(inversify.injectable(), Ninja);
inversify.decorate(inversify.inject(TYPES.Katana), Ninja, 0);
inversify.decorate(inversify.inject(TYPES.Shuriken), Ninja, 1);
kernel.bind(TYPES.Ninja).to(Ninja);
```
You can just write:

```js
helpers.register(kernel, TYPES.Ninja, Ninja, [TYPES.Katana, TYPES.Shuriken]);
```

Let's take a look to an example:
```js
var inversify = require("inversify");
var helpers =  require("inversify-vanillajs-helpers");
require("reflect-metadata");

var TYPES = {
  Ninja: 'Ninja',
  Katana: 'Katana',
  Shuriken: 'Shuriken'
}

class Katana {
  hit () {
    return 'cut!'
  }
}

class Shuriken {
  throw () {
    return 'hit!'
  }
}

class Ninja {

  constructor(katana, shuriken) {
      this._katana = katana;
      this._shuriken = shuriken;
  }

  fight () { return this._katana.hit() }
  sneak () { return this._shuriken.throw() }

}

// Declare bindings
var kernel = new inversify.Kernel()
helpers.register(kernel, TYPES.Katana, Katana);
helpers.register(kernel, TYPES.Shuriken, Shuriken);
helpers.register(kernel, TYPES.Ninja, Ninja, [TYPES.Katana, TYPES.Shuriken]);

// Resolve dependencies
var ninja = kernel.get(TYPES.Ninja);
console.log(ninja.fight(), ninja.sneak());
```

Just like when we use the binding API:
```ts
interface BindingToSyntax<T> {
  to(constructor: { new (...args: any[]): T; }): BindingInWhenOnSyntax<T>;
  toSelf(): BindingInWhenOnSyntax<T>;
  toConstantValue(value: T): BindingWhenOnSyntax<T>;
  toDynamicValue(func: (context: Context) => T): BindingInWhenOnSyntax<T>;
  toConstructor<T2>(constructor: Newable<T2>): BindingWhenOnSyntax<T>;
  toFactory<T2>(factory: FactoryCreator<T2>): BindingWhenOnSyntax<T>;
  toFunction(func: T): BindingWhenOnSyntax<T>;
  toAutoFactory<T2>(serviceIdentifier: ServiceIdentifier<T2>): BindingWhenOnSyntax<T>;
  toProvider<T2>(provider: ProviderCreator<T2>): BindingWhenOnSyntax<T>;
}
```
We can use the helpers to register many types of bindings.

### registerSelf

```js
helpers.registerSelf(kernel, Katana);
```

### registerConstantValue

```js
helpers.registerConstantValue(kernel, TYPES.Katana, new Katana());
```

### registerDynamicValue

```js
helpers.registerDynamicValue(kernel, TYPES.Katana, (context) => {
  new Katana();
});
```

### registerConstructor

```js
helpers.registerConstructor(kernel, TYPES.Katana, Katana);
```

### registerFunction

```js
helpers.registerFunction(kernel, TYPES.SomeFunction, () {
  console.log("I'm doing something...");
});
```

### registerAutoFactory

```js
helpers.registerAutoFactory(kernel, TYPES.KatanaFactory, TYPES.Katana);
```

### registerFactory

```js
helpers.registerFactory(kernel, TYPES.KatanaFactory, (context: interfaces.Context) => {
  return () => {
    return context.kernel.get<Katana>("Katana");
  };
});
```

### registerProvider

```js
helpers.registerProvider(kernel, TYPES.KatanaProvider, (context) => {
    return () => {
        return new Promise<Katana>((resolve) => {
            let katana = context.kernel.get<Katana>("Katana");
            resolve(katana);
        });
    };
});
```

## Declaring binding constraints

The register helper allows access to the fluent binding declaration API:

```js
helpers.registerClass(kernel, TYPES.Weapon, new Katana()).whenTargetTagged("throwable", false);
helpers.registerClass(kernel, TYPES.Weapon, new Shuriken()).whenTargetTagged("throwable", true);
```
