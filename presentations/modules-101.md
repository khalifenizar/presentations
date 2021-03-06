Modules 101
===========

A quick guide to understanding and making modules in Node.js.

By [Nizar](https://github.com/khalifenizar).

----

Modules 101
===========

Let's go through this quickly because together we are going to do a special activity.


---


Modules *are* Node.js
---------------------

Node's design [relies heavily on "userland"](https://github.com/joyent/node/wiki/node-core-vs-userland) -- user modules.

----

Modules *are* Node.js
---------------------

The standard library (a.k.a. node-core) is a very minimal set of building blocks.

----

Modules *are* Node.js
---------------------

This empowers *the community*, that is to say us, to participate in a big way.

----

Modules *are* Node.js
---------------------

By writing modules and publishing them for the whole community on...


---


[npm](https://www.npmjs.org/)
-----------------------------

The giant registry for user modules for Node.js (and other things too).

----

[npm](https://www.npmjs.org/)
-----------------------------

This is awesome because we, *the community*, have made a ton of modules.

----

[npm](https://www.npmjs.org/)
-----------------------------

Need a function that does a thing? If it's not already made, make it!

----

[npm](https://www.npmjs.org/)
-----------------------------

Hell, if it's already made make it anyway! It's easy!

----

[npm](https://www.npmjs.org/)
-----------------------------

How easy is it?


---


It's really really easy
-----------------------

Two weeks ago I had *zero* modules in `npm`.

----

It's really really easy
-----------------------

Now I have [one whole module](https://www.npmjs.org/~khalifenizar) and another one almost done!

----

It's really really easy
-----------------------

*It's so easy*, in fact, that we are about to make one and publish it to `npm`.


---


That's right. You. Me. Us.
--------------------------


---


<('.'<) (>'.')> <('.'<)
-----------------------

Who knows what this is?


---


kirby-dance
-----------

We are making it.

----

kirby-dance
-----------

Here's what I want it to look like:

```js
var kirbyDance = require('kirby-dance');

console.log(kirbyDance(4)); // "<('.'<) (>'.')> <('.'<) (>'.')>"
```


---


Module basics
-------------

Before coding, let's go over some basics.

----

Module basics
-------------

A *module* is a file that contains a piece of code (ideally small and self-contained) that can be pulled into a program.

----

Module basics
-------------

In Node.js, programs use the `require()` function to pull in modules.

```js
// Your program

var thing = require('./thing');
```

----

Module basics
-------------

Modules use `exports` or `module.exports` to make things available.

```js
// thing.js module

function thing () {
    // do things
}

module.exports = thing;
```

----

Module basics
-------------

The things you make available can be anything that you can store in a JavaScript variable!

- objects
- functions
- prototypes (i.e. constructor/class equivalent in JavaScript)
- arrays
- numbers
- strings
- whatever!


---


require
-------

The `require()` function can pull in modules from three different places.

----

require
-------

[Node core](http://nodejs.org/api/):

```js
var crypto = require('crypto'),
    http = require('http'),
    fs = require('fs')
;
```

----

require
-------

Files and folders (automatically adds the `.js` extension):

```js
var thing = require('./thing'),                // ./thing.js
    arrayStuff = require('./stuff/array'),     // ./stuff/array.js
    stuff = require('./stuff'),                // ./stuff/index.js
    up = require('../up'),                     // ../up.js
    absolute = require('/usr/nizar/absolute')  // /usr/nizar/absolute.js
;
```

----

require
-------

The `node_modules` directory (where `npm` puts modules):

```bash
npm install moment
npm install chalk
```

```js
var moment = require('moment'),
    chalk = require('chalk')
;
```

----

require
-------

The `require()` function caches the value of `module.exports`.

```js
// thing.js

var thing = {};
thing.greeting = 'o hai';

module.exports = thing;
```

```js
// Your program

var thing1,
    thing2
;

thing1 = require('./thing');
thing1.greeting = 'Hello, sir.';

thing2 = require('./thing');
console.log(thing2.greeting); // "Hello, sir."
```


---


Basic module patterns
---------------------

Expose a function.

```js
// do-stuff.js

function doStuff (thing) {
    return 'Stuff done with ' + thing + '!';
}

module.exports = doStuff;
```

```js
// Your program

var doStuff = require('./do-stuff');

console.log(doStuff('thing'));  // "Stuff done with thing!"
```

----

Basic module patterns
---------------------

Expose a "namespace" object.

```js
// stuff.js

var stuff = {};
stuff.version = '0.0.1';
stuff.do = function (thing) {
    return 'Stuff done with ' + thing + '!';
};

module.exports = stuff;
```

```js
// Your program

var stuff = require('./stuff');

console.log(stuff.version);      // 0.0.1
console.log(stuff.do('thing'));  // "Stuff done with thing!"
```


---


Anti-patterns
------------

Some things to avoid when you are first starting to write modules.


---


What's wrong with this module?
------------------------------

```js
// hello.js
console.log('Hello world!');
```

----

Side-effects
------------

It does stuff automatically when requiring it. We call this a side-effect.

```js
// Your program
require('./hello'); // Logs "Hello world!"
```

----

Side-effects
------------

Your modules destined for `npm` should expose functionality via `exports` and only do actual work when the user invokes the functionality.

----

Side-effects
------------

By "actual work" I mean:

- Logging to `stdout` or `stderr` (i.e. `console.log()` and family)
- Writing or reading the file system
- Connecting, writing or reading to/from databases
- HTTP requests
- DOM operations (on browsers)
- Any other expensive computation

----

Side-effects
------------

Here's a `hello` module with no side-effects:

```js
// hello.js

function hello () {
    console.log('Hello world!');
}

module.exports = hello;
```


---


What's wrong with this module?
------------------------------

```js
// hello.js

hello = function () {
    console.log('Hello world!');
};
```

----

Global variables
----------------

It pollutes the global namespace with a global variable `hello`.

```js
// Your program

require('./hello');

hello();
```

----

Global variables
----------------

Your modules should use local variables and again use `exports` to expose functionality.

```js
// hello.js

var hello = function () {
    console.log('Hello world!');
};

module.exports = hello;
```

----

Global variables
----------------

This way other progams can use them in the manner of their choosing.

```js
// Your program

var i_can_call_this_whatever_i_like = require('./hello');

i_can_call_this_whatever_i_like();
```


---


Now let's get to coding...
--------------------------


---


Good job!
---------

Now help me make it better! Send me some pull requests!


---


Further reading
---------------

- Post from the Node team about userland: https://github.com/joyent/node/wiki/node-core-vs-userland
- In-depth module patterns: http://bites.goodeggs.com/posts/export-this/
- Substack on writing modules: http://substack.net/how_I_write_modules


---


Modules 101
===========

[Thanks for listening!](https://www.npmjs.org/~khalifenizar)
------------------------------------------------------------
