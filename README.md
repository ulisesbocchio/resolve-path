# resolve-path

[![NPM Version][npm-image]][npm-url]
[![NPM Downloads][downloads-image]][downloads-url]
[![Node.js Version][node-image]][node-url]
[![Build Status][travis-image]][travis-url]
[![Test Coverage][coveralls-image]][coveralls-url]

Resolve a relative path against a root path with validation.

This module would protect against commons attacks like `GET /../file.js`
which reaches outside the root folder.

## Installation

```sh
$ npm install resolve-path
```

## API

```js
var resolvePath = require('resolve-path')
```

### resolvePath(relativePath)

Resolve a relative path against `process.cwd()` (the process's current working
directory) and return an absolute path. *This will throw* if the resulting resolution
seems malicious. The following are malicious:

  - The relative path is an absolute path
  - The relative path contains a NULL byte
  - The relative path resolves to a path outside of `process.cwd()`

### resolvePath(rootPath, relativePath)

Resolve a relative path against the provided root path and return an absolute path.
*This will throw* if the resulting resolution seems malicious. The following are
malicious:

  - The relative path is an absolute path
  - The relative path contains a NULL byte
  - The relative path resolves to a path outside of the root path

## Example

### Safely resolve paths in a public directory

```js
var http = require('http')
var parseUrl = require('parseurl')
var path = require('path')
var resolvePath = require('resolve-path')

// the public directory
var publicDir = path.join(__dirname, 'public')

// the server
var server = http.createServer(function onRequest(req, res) {
  try {
    // get the pathname from the URL (decoded)
    var pathname = decodeURIComponent(parseUrl(req).pathname)

    if (!pathname) {
      res.statusCode = 400
      res.end('path required')
      return
    }

    // remove leading slash
    var filename = pathname.substr(1)

    // resolve the full path
    var fullpath = resolvePath(publicDir, filename)

    // echo the resolved path
    res.statusCode = 200
    res.end('resolved to ' + fullpath)
  } catch (err) {
    res.statusCode = err.status || 500
    res.end(err.message)
  }
})

server.listen(3000)
```

## License

[MIT](LICENSE)

[npm-image]: https://img.shields.io/npm/v/resolve-path.svg?style=flat
[npm-url]: https://npmjs.org/package/resolve-path
[node-image]: https://img.shields.io/node/v/resolve-path.svg?style=flat
[node-url]: http://nodejs.org/download/
[travis-image]: https://img.shields.io/travis/pillarjs/resolve-path.svg?style=flat
[travis-url]: https://travis-ci.org/pillarjs/resolve-path
[coveralls-image]: https://img.shields.io/coveralls/pillarjs/resolve-path.svg?style=flat
[coveralls-url]: https://coveralls.io/r/pillarjs/resolve-path?branch=master
[downloads-image]: https://img.shields.io/npm/dm/resolve-path.svg?style=flat
[downloads-url]: https://npmjs.org/package/resolve-path
