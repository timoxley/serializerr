# serializerr

Convert Errors & Objects into an easily-serialized vanilla Object.

Resulting object has a flattened prototype chain & includes
non-enumerable properties. Ignores properties on `Object.prototype`.

## Why

If you've ever tried to send an Error over a JSON-encoded connection
you've probably been surprised to find all the useful information is
sapped out of it; all the juicy properties like `name`, `message` &
`stack` are non-enumerable thus they are not included in the
stringified JSON. This may be non-standard behaviour, as I could not
locate any mention in either the ES5.1 or the ES6 spec about it, but
Error properties are non-enumerable both in V8 (Chrome/io.js/Node.js) &
SpiderMonkey (Firefox).

I believe Error property non-enumerability was added as a security
measure to prevent stack traces and other sensitive information
accidentally leaking, but it's not uncommon to actually want to send
the data in Error objects over the wire.

`serializerr` makes an Object suitable for serializing to & from
JSON. Not restricted to use with Errors, will work with any Object.

## Installation

```
npm install serializerr
```

## Usage

`serializerr` creates a vanilla Object with the prototype chain
flattened & any non-enumberable properties as regular enumerable properties.
This allows it to be serialised to JSON without losing important data.

```js

var wellSerializedError = JSON.parse(JSON.stringify(
  serializerr(error)
))

console.log(wellSerializedError.name) // Error
console.log(wellSerializedError.message) // an error occurred
console.log(wellSerializedError.stack) // Error: an error occurred\n  at Test.<anonymous> ...

```

## Example

```js

var serializerr = require('serializerr')

var error = new Error('an error')

// simulate transferring an Error object over the wire as JSON
// without first passing through serializerr
var poorlySerializedError = JSON.parse(JSON.stringify(error))

// oh dear:
console.log(poorlySerializedError.name) // undefined
console.log(poorlySerializedError.message) // undefined
console.log(poorlySerializedError.stack) // undefined

// bring forth the serializerr
var errorObject = serializerr(error)

var wellSerializedError = JSON.parse(JSON.stringify(errorObject))

// properties are conserved!
console.log(wellSerializedError.name) // Error
console.log(wellSerializedError.message) // an error occurred
console.log(wellSerializedError.stack) // Error: an error occurred\n  at Test.<anonymous> ...

// note errorObject is a vanilla Object, not an Error
errorObject instanceof Error // false
```

## License

MIT
