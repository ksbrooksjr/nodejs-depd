# depd

[![NPM version](https://badge.fury.io/js/depd.svg)](http://badge.fury.io/js/depd)
[![Build Status](https://travis-ci.org/dougwilson/nodejs-depd.svg?branch=master)](https://travis-ci.org/dougwilson/nodejs-depd)
[![Coverage Status](https://img.shields.io/coveralls/dougwilson/nodejs-depd.svg?branch=master)](https://coveralls.io/r/dougwilson/nodejs-depd)

Deprecate all the things

> With great modules comes great responsibility; mark things deprecated!

## Install

```sh
$ npm install depd
```

## API

```js
var depd      = require('depd')
var deprecate = depd('my-module')
```

This library allows you to display deprecation messages to your users.
This library goes above and beyond with deprecation warnings by
introspecting the call stack (but only the bits that it is interested
in).

Instead of just warning on the first invocation of a deprecated
function and never again, this module will warn on the first invocation
of a deprecated function per unique call site, making it ideal to alert
users of all deprecated uses across the code base, rather than just
whatever happens to execute first.

The deprecation warnings from this module also include the file and line
information for the call into the module that the deprecated function was
in.

### depd(namespace)

Create a new deprecate function that uses the given namespace name in the
messages and will display the call site prior to the stack entering the
file this function was called from. It is highly suggested you use the
name of your module as the namespace.

### deprecate(message)

Call this function from deprecated code to display a deprecation message.
This message will appear once per unique caller site. Caller site is the
first call site in the stack in a different file from the caller of this
function.

## Display

When a user calls a function in your library that you mark deprecated, they
will see the following written to STDERR (in the given colors, similar colors
and layout to the `debug` module):

```
bright cyan    bright yellow
|              |          grey        cyan
|              |          |           |
▼              ▼          ▼           ▼
my-cool-module deprecated oldfunction [eval]-wrapper:6:22
▲              ▲          ▲           ▲
|              |          |           |
namespace      |          |           location of mycoolmod.oldfunction() call
               |          deprecation message
               the word "deprecated"
```

If the user redirects their STDERR to a file or somewhere that does not support
colors, they see (similar layout to the `debug` module):

```
Sun, 15 Jun 2014 05:21:37 GMT my-cool-module deprecated oldfunction at [eval]-wrapper:6:22
▲                             ▲              ▲          ▲              ▲
|                             |              |          |              |
timestamp of message          namespace      |          |             location of mycoolmod.oldfunction() call
                                             |          deprecation message
                                             the word "deprecated"
```

## Examples

### Deprecating all calls to a function

This will display a deprecated message about "oldfunction" being deprecated
from "my-module" on STDERR.

```js
var deprecate = require('depd')('my-cool-module')

exports.oldfunction = function () {
  // all calls to function are deprecated
  deprecate('oldfunction')
}
```

### Conditionally deprecating a function call

This will display a deprecated message about "weirdfunction" being deprecated
from "my-module" on STDERR when called with less than 2 arguments.

```js
var deprecate = require('depd')('my-cool-module')

exports.weirdfunction = function () {
  if (arguments.length < 2) {
    // calls with 0 or 1 args are deprecated
    deprecate('weirdfunction args < 2')
  }
}
```

When calling `deprecate` as a function, the warning is counted per call site
within your own module, so you can display different deprecations depending
on different situations and the users will still get all the warnings:

```js
var deprecate = require('depd')('my-cool-module')

exports.weirdfunction = function () {
  if (arguments.length < 2) {
    // calls with 0 or 1 args are deprecated
    deprecate('weirdfunction args < 2')
  } else if (typeof arguments[0] !== 'string') {
    // calls with non-string first argument are deprecated
    deprecate('weirdfunction non-string first arg')
  }
}
```

### Deprecating property access

This will display a deprecated message about "oldprop" being deprecated
from "my-module" on STDERR when accessed. A deprecation will be displayed
when setting the value and when getting the value.

```js
var deprecate = require('depd')('my-cool-module')

;(function () {
  var value = 'something'
  Object.defineProperty(exports, 'oldprop', {
    configurable: true,
    enumerable: true,
    get: function () {
      deprecate('get oldprop')
      return value
    },
    set: function (val) {
      deprecate('set oldprop')
      return value = val
    }
  })
}())
```

## License

The MIT License (MIT)

Copyright (c) 2014 Douglas Christopher Wilson

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
