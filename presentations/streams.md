Node.js Streams
===============

An introduction to streams in Node.js.

By [Nizar](https://github.com/khalifenizar).

----

Node.js Streams
===============

Let's go over the basics of what streams are and why they are cool.

----

Node.js Streams
===============

Then let's do some [`stream-adventure`](https://www.npmjs.org/package/stream-adventure)!

----

Node.js Streams
===============

Please interrupt me if you have any questions or if I say something totally wrong!

----

Node.js Streams
===============

How many of you are new to Node.js?

----

Node.js Streams
===============

How many of you have used streams before?


---


What's a stream?
----------------

Streams are Node.js' unified interface for I/O.

----

What's a stream?
----------------

They are [part of Node.js core](http://nodejs.org/api/stream.html), Node's standard library.

----

What's a stream?
----------------

A stream can be *readable* (provides data), *writable* (expects data) or *both* (called a *transform* stream).

----

What's a stream?
----------------

Just like when you stream a video, Node.js streams send, receive and operate on data in *small chunks* at a time.

----

What's a stream?
----------------

They are designed to emulate Unix in that streams can be *piped* together to compose more complex operations.


---


How streams work
----------------

Here's the most basic example of streams in action:

```js
readbleStream.pipe(writableStream);
```

----

How streams work
----------------

But let's look to Unix first to give this a little bit of context.

```js
readbleStream.pipe(writableStream);
```


---


Piping in Unix
--------------

Take the `history` command, for example:

```bash
$ history
```

----

Piping in Unix
--------------

It gives you a huge list of the last commands you've run.

```bash
$ history
```

----

Piping in Unix
--------------

By itself that isn't very useful.

```bash
$ history
```

----

Piping in Unix
--------------

But if we *pipe* that data into another command, say `grep`:

```bash
$ history | grep 'git diff'
```

----

Piping in Unix
--------------

Now we can filter that list into something meaningful.

```bash
$ history | grep 'git diff'
```

----

Piping in Unix
--------------

And we can add even more functionality by adding more pipes:

```bash
$ history | grep 'git diff' | pbcopy
```

----

Piping in Unix
--------------

The `pbcopy` command puts the data it receives on your clipboard. Try pasting!

```bash
$ history | grep 'git diff' | pbcopy
```

----

Piping in Unix
--------------

What we've got here is a *pipeline*: functionality composed together by piping.

```bash
$ history | grep 'git diff' | pbcopy
```


---


How streams work
----------------

Back in JavaScript-land, we can also build *pipelines*.

----

How streams work
----------------

Instead of using commands and the pipe character `|`, we build them using stream objects and the `.pipe()` method:

```js
readbleStream.pipe(writableStream);
```

----

How streams work
----------------

It starts with a *readable* stream providing a flow of data:

```js
readbleStream;    // has some cool data
```

----

How streams work
----------------

Then a *writable* stream can receive that data through the `.pipe()` method and *act upon* it:

```js
readbleStream    // has some cool data
    .pipe( writableStream )    // does stuff with the cool data
;
```

----

How streams work
----------------

If desired, *transform* streams (i.e. both *readable* and *writable*) can be piped in between to modify the data in some way:

```js
readbleStream    // has some cool data
    .pipe( transformStream )    // transforms the cool data written to it
    .pipe( writableStream )     // does stuff with transformed cool data
;
```

----

How streams work
----------------

This works because `.pipe()` returns a stream:

```js
readbleStream
    .pipe( transformStream )    // returns a stream
    .pipe( writableStream )     // returns a stream
;
```

----

How streams work
----------------

Specifically, `.pipe()` returns the stream that is sent as the argument to the method call.

```js
readbleStream
    .pipe( transformStream )    // returns `transformStream`
    .pipe( writableStream )     // returns `writableStream`
;
```

----

How streams work
----------------

So a stream pipeline takes *data* from a source, *transforms* it and/or otherwise *acts upon* it.


---


Code example
------------

Now let's see a code example for CSV parsing.


----

Code example
------------

First let's start with a `sentences.csv` file:

```csv
I,like,pizza.,,,
I,also,like,cookies.,,
The,occasional,beer,doesn't,hurt,either.
```

----

Code example
------------

Step 1: Open a readable stream of the file using the `fs` module:

```js
var fs = require('fs');

fs.createReadStream(__dirname + '/sentences.csv');
```

----

Code example
------------

[Step 2](examples/streams/csv-step2.js): Now that we have our data, we need to decide how to *act upon* it with a writable stream.

----

Code example
------------

[Step 2](examples/streams/csv-step2.js): Let's go with our basic `process.stdout` to write to the console:

```js
// examples/streams/csv-step2.js
var fs = require('fs');

fs.createReadStream(__dirname + '/sentences.csv')
    .pipe(process.stdout)
;
```

----

Code example
------------

Here's what [Step 2](examples/streams/csv-step2.js) gives us:

```bash
$ node examples/streams/csv-step2.js
I,like,pizza.,,,
I,also,like,cookies.,,
The,occasional,beer,doesn't,hurt,either.
```

----

Code example
------------

[Step 3](examples/streams/csv-step3.js): Next up is parsing the comma-separated format, so we need to *transform* the data now.

----

Code example
------------

[Step 3](examples/streams/csv-step3.js): Let's use the [`split`](https://www.npmjs.org/package/split) module from npm to split by comma:

```js
var split = require('split');
var fs = require('fs');

fs.createReadStream(__dirname + '/sentences.csv')
    .pipe(split(','))
    .pipe(process.stdout)
;
```

----

Code example
------------

Here's what [Step 3](examples/streams/csv-step3.js) leaves us with:

```bash
$ node examples/streams/csv-step3.js
Ilikepizza.
Ialsolikecookies.
Theoccasionalbeerdoesn'thurteither.
```

----

Code example
------------

[Step 4](examples/streams/csv-step4.js): We extracted the words from the commas but we want our words to be separated by spaces.

----

Code example
------------

[Step 4](examples/streams/csv-step4.js): Let's use the [`appendage`](https://www.npmjs.org/package/appendage) module from npm to prepend a space:

```js
var append = require('appendage');
var split = require('split');
var fs = require('fs');

fs.createReadStream(__dirname + '/sentences.csv')
    .pipe(split(','))
    .pipe(append({ before: ' ' }))
    .pipe(process.stdout)
;
```

----

Code example
------------

[Step 4](examples/streams/csv-step4.js) finally leaves us with something nice to read:

```bash
$ node examples/streams/csv.js
 I like pizza.
I also like cookies.
The occasional beer doesn't hurt either.
```


---


Streams are good
----------------

With that example under our belt, we can begin to see a couple of reasons why streams are nice to work with.


---


Separation of concerns
----------------------

Streams help separate code concerns.

----

Separation of concerns
----------------------

We saw in the example that when we need to perform a transformation or other action with our data we pipe a stream:

```js
fs.createReadStream(__dirname + '/sentences.csv')
    .pipe(split(','))
    .pipe(append({ before: ' ' }))
    .pipe(process.stdout)
;
```

----

Separation of concerns
----------------------

Each action is performed by its own module that can be tested, improved and debugged separately.

```js
fs.createReadStream(__dirname + '/sentences.csv')
    .pipe(split(','))
    .pipe(append({ before: ' ' }))
    .pipe(process.stdout)
;
```

----

Separation of concerns
----------------------

It's also pretty clear what each stream is intending to do, making it easy to reason about what the code is doing as a whole.

```js
fs.createReadStream(__dirname + '/sentences.csv')
    .pipe(split(','))
    .pipe(append({ before: ' ' }))
    .pipe(process.stdout)
;
```


---


Composability
-------------

By a similar token, streams help make your code more composable.

----

Composability
-------------

The unified interface makes it easy to `.pipe()` in a bunch of streams to that work together to perform higher-level functionality, again, like in our example:

```js
fs.createReadStream(__dirname + '/sentences.csv')
    .pipe(split(','))
    .pipe(append({ before: ' ' }))
    .pipe(process.stdout)
;
```


---


Lots of streams in the wild
---------------------------

A lot of modules in Node.js core and npm are already streams.

----

Lots of streams in the wild
---------------------------

This doesn't have to do with any intrinsic quality of streams as an interface, but you can take advantage of it nonetheless by using and writing more streams.


---


Readable streams
----------------

To implement a readable stream you can either use directly or extend the `Readable` prototype in the `stream` module:

```js
var stream = require('stream');

var readableStream = new stream.Readable();
```

----

Readable streams
----------------

Then use the `.push()` method of your new stream to give it data:

```js
var stream = require('stream');

var readableStream = new stream.Readable();
readableStream.push('pew ');
readableStream.push('pow\n');
```

----

Readable streams
----------------

Finally call `.push()` with `null` when there is no more data and pipe it:

```js
var stream = require('stream');

var readableStream = new stream.Readable();
readableStream.push('pew ');
readableStream.push('pow\n');
readableStream.push(null);

readableStream.pipe(process.stdout);
```

----

Readable streams
----------------

While `.pipe()` normally does this for you, you can consume data directly from a readable stream by first listening for the `readable` event:

```js
var fs = require('fs');

var fileStream = fs.createReadStream(__dirname + '/sentences.csv');
fileStream.on('readable', function () {});
```

The `readable` event is emitted when the stream has a small chunk of data ready for reading.

----

Readable streams
----------------

The `readable` event is emitted when the stream has a small chunk of data ready for reading. To read that chunk, call the `.read()` method *inside* the `readable` callback:

```js
var fs = require('fs');

var fileStream = fs.createReadStream(__dirname + '/sentences.csv');
fileStream.on('readable', function () {
    console.log('Chunk:', fileStream.read());
});
```

----

Readable streams
----------------

What we get from `.read()` is a `Buffer` object, Node's way of representing arbitrary data. In this case we know it's a string so let's convert it:

```js
var fs = require('fs');

var fileStream = fs.createReadStream(__dirname + '/sentences.csv');
fileStream.on('readable', function () {
    console.log('Chunk:', fileStream.read().toString());
});
```

----

Readable streams
----------------

Since data is read in small chunks, the `readable` event can (and likely will) be emitted several times: once for each chunk.


---


Streams are good, the sequel
----------------------------

Which brings us to a couple more cool benefits of streams.


---


Resource efficiency
-------------------

Since streams deal in small chunks of data at a time, they use resources (namely, memory) way more efficiently.

----

Resource efficiency
-------------------

This enables stream interfaces to handle extremely large data sets (such as a huge file or a database with a huge amount of records) very well without consuming all of your system's memory.


---


Less waiting
------------

Dealing with small chunks also allows you to start acting upon and responding to data more quickly.

----

Less waiting
------------

You can start building and sending your JSON response or HTML data to the client sooner.


---


Node.js Streams
===============

Questions
---------

----

Node.js Streams
===============

Further reading
---------------

- Deeper dive into streams by Substack: https://github.com/substack/stream-handbook
- Good stuff about back pressure by Tim Caswell: http://howtonode.org/streams-explained
- More examples and differences between Streams 1, 2 and 3 by Bryce Baril: http://brycebaril.github.io/streams2-presentation/

----

Node.js Streams
===============

[Thanks for listening!](https://github.com/khalifenizar)
--------------------------------------------------------
