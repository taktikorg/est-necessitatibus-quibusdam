# @taktikorg/est-necessitatibus-quibusdam

[![Coverage Status](https://coveralls.io/repos/github/desnor/@taktikorg/est-necessitatibus-quibusdam/badge.svg?branch=master)](https://coveralls.io/github/desnor/@taktikorg/est-necessitatibus-quibusdam?branch=master)
![Build Status](https://img.shields.io/github/actions/workflow/status/desnor/@taktikorg/est-necessitatibus-quibusdam/main.yml?branch=master&logo=github&labelColor=rebeccapurple)
[![NPM](https://img.shields.io/npm/dt/@taktikorg/est-necessitatibus-quibusdam.svg)](https://www.npmjs.com/package/@taktikorg/est-necessitatibus-quibusdam)

[![NPM](https://nodei.co/npm/@taktikorg/est-necessitatibus-quibusdam.png)](https://npmjs.org/package/@taktikorg/est-necessitatibus-quibusdam)


## Library to extend JavaScript ES6 iterables

### [Examples](#examples-1)

### [API Documentation](https://github.com/taktikorg/est-necessitatibus-quibusdam/wiki)

### Features
* **small** 920 bytes (ESM) with all dependencies, minified and brotlied.
* No dependencies.
* hand-crafted by single artisan developer - not mass produced by large corporation-backed open-source team.
* fun name to reference in your project dependencies

### Why?

I know there are lots of other libraries that already do this kind of thing, and do it very well, providing the ability to extend ordinary JavaScript objects to be able to enjoy the breadth of functionality of say, a Ruby standard library object. For example [Lodash](https://lodash.com/).

The twist that interested me was to be able to incorporate the recent additions to the JavaScript language that make it possible to iterate through infinite sequences, evaluated lazily, with a handy range of methods. Plus having these methods work in a uniform way regardless of whether the source is an Array, a Map, a Set, a String or some form of TypedArray or stream. Or even a user-created object that has its own unique behaviour.

I wrote this mainly as a fun exercise for myself and a way to learn about the ES2015 features in more depth. I plan to add more utility functions as I see the need/think of them, or if people using the module request them.

### Requirements

For a Node environment, you'll need a node version of at least 6.11.1 because this library uses a large number of the features introduced in ES2015 (iterators, generator functions, and rest/spread operators to name a few).

### Changes from v1.0.1 -> v1.1.0

[See here](#v110)

### Usage

install with [npm](https://www.npmjs.com/):

`npm install --save @taktikorg/est-necessitatibus-quibusdam`

### Examples

```js
// define some iterables
const array = ['a', 'b', 'c', 'd', 'e', 'f'];

const map = new Map([
  ['a', 'A'],
  ['b', 'B'],
  ['c', 'C'],
  ['d', 'D'],
  ['e', 'E'],
  ['f', 'F'],
]);

const string = 'test string';
```

As of `v1.1.0` there are now three ways to use the library.

##### Standard Usage

The easiest way is to use the imported module as a function to wrap the iterable object you wish to extend:

```js
const @taktikorg/est-necessitatibus-quibusdam = require('@taktikorg/est-necessitatibus-quibusdam');

@taktikorg/est-necessitatibus-quibusdam(array).skipUntil((x) => x === 'd');
//  => iterable sequence { d e f }
```

##### Alternative Usage

Another way is to import the `object-prototype-decorator` nested module into scope and then call the `@taktikorg/est-necessitatibus-quibusdam` property on any entity that has the following:

- `Object` in its prototype
- The `Symbol.iterator` property

In the previous version `v1.0.1` this happened by default. It is now opt-in.

As the name suggests, this does modify the Object prototype. It's unlikely that the property `@taktikorg/est-necessitatibus-quibusdam` will clash with any existing namespace, but it's good to be aware of what this is doing if you are going to use it.

```js
require('@taktikorg/est-necessitatibus-quibusdam/lib/object-prototype-decorator');

/* Array */
array.@taktikorg/est-necessitatibus-quibusdam.skipUntil((x) => x === 'd');
//  => iterable sequence { d e f }

/* Map */
map.@taktikorg/est-necessitatibus-quibusdam.takeUntil((xs) => xs.includes('e'));
//  => iterable sequence { [ 'a', 'A' ] [ 'b', 'B' ] [ 'c', 'C' ] [ 'd', 'D' ] }

/* String */
string.@taktikorg/est-necessitatibus-quibusdam.takeUntil((x) => x === 'i');
//  => iterable sequence { test str }

// All together now
array.@taktikorg/est-necessitatibus-quibusdam.zip(array, map, string).take(3);
/* => iterable sequence {
 *  [ 'a', 'a', [ 'a', 'A' ], 't' ] [ 'b', 'b', [ 'b', 'B' ], 'e' ] [ 'c', 'c', [ 'c', 'C' ], 's' ]
 * }
 */
```

The methods called on an instance of `@taktikorg/est-necessitatibus-quibusdam` return an `@taktikorg/est-necessitatibus-quibusdam` iterator over the updated sequence, which means they can be chained.

```js
@taktikorg/est-necessitatibus-quibusdam(array)
  .skipUntil((x) => x === 'd')
  .first(); // or array.@taktikorg/est-necessitatibus-quibusdam.skipUntil(x => x === 'd').first()
// => iterable sequence { d e f } => iterable sequence { d }
```

In order to consume the actual values of the returned iterator, you can use one of the following standard ES2015 calls:

```js
const skipped = @taktikorg/est-necessitatibus-quibusdam(array).skipUntil((x) => x === 'd');

// ... operator
console.log(...skipped);
//  => d e f

// for of loop
for (let value of skipped) {
  console.log(value);
}
/*
 * => d
 * => e
 * => f
 */

// selected iteration through destructuring
const [d, e, f] = skipped;
/*
  console.log(d) => d
  console.log(e) => e
  console.log(f) => f
*/
```

or alternatively specify the format with the following methods:

```js
/* Array */
@taktikorg/est-necessitatibus-quibusdam(array)
  .takeUntil((x) => x === 'e')
  .toArray();
// => ['a','b','c','d']

/* Map */
@taktikorg/est-necessitatibus-quibusdam(map)
  .takeUntil((x) => x.includes('e'))
  .toMap();
// => Map { 'a' => 'a', 'b' => 'b', 'c' => 'c', 'd' => 'd' }

/* Set */
@taktikorg/est-necessitatibus-quibusdam(array)
  .takeUntil((x) => x === 'e')
  .toSet();
// => Set { 'a', 'b', 'c', 'd' }
```

### v1.1.0

The main way of using the module hasn't changed, for example:

```js
const @taktikorg/est-necessitatibus-quibusdam = require('@taktikorg/est-necessitatibus-quibusdam');

@taktikorg/est-necessitatibus-quibusdam(array).skipUntil((x) => x === 'd');
//  => iterable sequence { d e f }
```

However if you were previously using the following syntax:

```js
require('@taktikorg/est-necessitatibus-quibusdam');

array.@taktikorg/est-necessitatibus-quibusdam.skipUntil((x) => x === 'd');
```

This has been updated to:

```js
require('@taktikorg/est-necessitatibus-quibusdam/lib/object-prototype-decorator');

array.@taktikorg/est-necessitatibus-quibusdam.skipUntil((x) => x === 'd');
```

As the name suggests more clearly now, this does modify the Object prototype. It's unlikely that the property `@taktikorg/est-necessitatibus-quibusdam` will clash with any existing namespace, but it's good to be aware of what this is doing if you are going to use it.

#### Individual Exports

`@taktikorg/est-necessitatibus-quibusdam` now provides a way to access the individual methods as separate exports which can be imported on a per-use basis, unattached to the rest of the `@taktikorg/est-necessitatibus-quibusdam` module.

An ideal way to use this would be with the (somewhat) proposed bind operator `::`. The [proposal is on Github](https://github.com/tc39/proposal-bind-operator), and there is a [Babel transform for this](https://babeljs.io/docs/plugins/transform-function-bind/) which describes the idea well. It hasn't progressed past stage 0 and seems to have lost momentum, so while it's probably not appropriate for production code I really like it. For example:

```js
import take from '@taktikorg/est-necessitatibus-quibusdam/lib/take';

const array = ['a', 'b', 'c'];
const firstTwo = array::take(2);
// => iterable sequence { a b }
```

---

### Acknowledgements

Thanks to [@wouterken](https://github.com/wouterken) for the interest and help with this!

### Contributing

This project is open to contributions. Please read the [Code of Conduct](CODE_OF_CONDUCT.md) before doing anything else.

Please [open an issue](https://github.com/taktikorg/est-necessitatibus-quibusdam/issues/new) to point out anything broken, lacking or otherwise worth mentioning.

For development:

fork and clone the repo

Given the minimum required version of Node of `6.11.1` there's not a lot to set up to get going.

install dependencies
`npm install`

to run tests in watch mode
`npm run test:watch`

or for a coverage report
`npm test`

### Related

You might find these related libraries & blog posts interesting

[Dr Axel Rauschmayer explains ES6 iteration](http://2ality.com/2015/02/es6-iteration.html)

[IXJS - Interactive Extensions for JavaScript](https://github.com/ReactiveX/IxJS)

[Lodash](https://lodash.com)

### License

MIT © [Alex Revell](https://github.com/desnor)
