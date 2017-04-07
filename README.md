# api documentation for  [socketio-jwt (v4.5.0)](https://github.com/auth0/socketio-jwt#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-socketio-jwt.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-socketio-jwt) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-socketio-jwt.svg)](https://travis-ci.org/npmdoc/node-npmdoc-socketio-jwt)
#### authenticate socket.io connections using JWTs

[![NPM](https://nodei.co/npm/socketio-jwt.png?downloads=true)](https://www.npmjs.com/package/socketio-jwt)

[![apidoc](https://npmdoc.github.io/node-npmdoc-socketio-jwt/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-socketio-jwt_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-socketio-jwt/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-socketio-jwt/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-socketio-jwt/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "José F. Romaniello",
        "email": "jfromaniello@gmail.com",
        "url": "http://joseoncode.com"
    },
    "bugs": {
        "url": "https://github.com/auth0/socketio-jwt/issues"
    },
    "dependencies": {
        "jsonwebtoken": "^5.0.0",
        "xtend": "~2.1.2"
    },
    "description": "authenticate socket.io connections using JWTs",
    "devDependencies": {
        "body-parser": "~1.13.3",
        "express": "~4.10.6",
        "mocha": "~1.17.0",
        "passport-local": "~0.1.6",
        "request": "~2.19.0",
        "serve-static": "^1.7.1",
        "server-destroy": "~1.0.1",
        "should": "~1.2.2",
        "socket.io": "^1.0.4",
        "socket.io-client": "^1.0.4"
    },
    "directories": {},
    "dist": {
        "shasum": "eab894c2593dfa358622b96d79b8c1888d76a7cf",
        "tarball": "https://registry.npmjs.org/socketio-jwt/-/socketio-jwt-4.5.0.tgz"
    },
    "gitHead": "fc68f5651c1dada42e43caf3a947d4a63dd0a4b2",
    "homepage": "https://github.com/auth0/socketio-jwt#readme",
    "keywords": [
        "socket",
        "socket.io",
        "jwt"
    ],
    "license": "MIT",
    "main": "lib/index.js",
    "maintainers": [
        {
            "name": "jfromaniello",
            "email": "jfromaniello@gmail.com"
        },
        {
            "name": "pose",
            "email": "albertopose@gmail.com"
        }
    ],
    "name": "socketio-jwt",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/auth0/socketio-jwt.git"
    },
    "scripts": {
        "test": "mocha"
    },
    "version": "4.5.0"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module socketio-jwt](#apidoc.module.socketio-jwt)
1.  [function <span class="apidocSignatureSpan">socketio-jwt.</span>UnauthorizedError (code, error)](#apidoc.element.socketio-jwt.UnauthorizedError)
1.  [function <span class="apidocSignatureSpan">socketio-jwt.</span>authorize (options, onConnection)](#apidoc.element.socketio-jwt.authorize)
1.  object <span class="apidocSignatureSpan">socketio-jwt.</span>UnauthorizedError.prototype

#### [module socketio-jwt.UnauthorizedError](#apidoc.module.socketio-jwt.UnauthorizedError)
1.  [function <span class="apidocSignatureSpan">socketio-jwt.</span>UnauthorizedError (code, error)](#apidoc.element.socketio-jwt.UnauthorizedError.UnauthorizedError)

#### [module socketio-jwt.UnauthorizedError.prototype](#apidoc.module.socketio-jwt.UnauthorizedError.prototype)
1.  [function <span class="apidocSignatureSpan">socketio-jwt.UnauthorizedError.prototype.</span>constructor (code, error)](#apidoc.element.socketio-jwt.UnauthorizedError.prototype.constructor)



# <a name="apidoc.module.socketio-jwt"></a>[module socketio-jwt](#apidoc.module.socketio-jwt)

#### <a name="apidoc.element.socketio-jwt.UnauthorizedError"></a>[function <span class="apidocSignatureSpan">socketio-jwt.</span>UnauthorizedError (code, error)](#apidoc.element.socketio-jwt.UnauthorizedError)
- description and source-code
```javascript
function UnauthorizedError(code, error) {
  Error.call(this, error.message);
  this.message = error.message;
  this.inner = error;
  this.data = {
    message: this.message,
    code: code,
    type: "UnauthorizedError"
  };
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.socketio-jwt.authorize"></a>[function <span class="apidocSignatureSpan">socketio-jwt.</span>authorize (options, onConnection)](#apidoc.element.socketio-jwt.authorize)
- description and source-code
```javascript
function authorize(options, onConnection) {
  options = xtend({ decodedPropertyName: 'decoded_token' }, options);

  if (!options.handshake) {
    return noQsMethod(options);
  }

  var defaults = {
    success: function(data, accept){
      if (data.request) {
        accept();
      } else {
        accept(null, true);
      }
    },
    fail: function(error, data, accept){
      if (data.request) {
        accept(error);
      } else {
        accept(null, false);
      }
    }
  };

  var auth = xtend(defaults, options);

  return function(data, accept){
    var token, error;
    var req = data.request || data;
    var authorization_header = (req.headers || {}).authorization;

    if (authorization_header) {
      var parts = authorization_header.split(' ');
      if (parts.length == 2) {
        var scheme = parts[0],
          credentials = parts[1];

        if (scheme.toLowerCase() === 'bearer') {
          token = credentials;
        }
      } else {
        error = new UnauthorizedError('credentials_bad_format', {
          message: 'Format is Authorization: Bearer [token]'
        });
        return auth.fail(error, data, accept);
      }
    }

    //get the token from query string
    if (req._query && req._query.token) {
      token = req._query.token;
    }
    else if (req.query && req.query.token) {
      token = req.query.token;
    }

    if (!token) {
      error = new UnauthorizedError('credentials_required', {
        message: 'No Authorization header was found'
      });
      return auth.fail(error, data, accept);
    }

    var onJwtVerificationReady = function(err, decoded) {

      if (err) {
        error = new UnauthorizedError(err.code || 'invalid_token', err);
        return auth.fail(error, data, accept);
      }

      data[options.decodedPropertyName] = decoded;

      return auth.success(data, accept);
    };

    var onSecretReady = function(err, secret) {
      if (err) {
        error = new UnauthorizedError(err.code || 'invalid_secret', err);
        return auth.fail(error, data, accept);
      }

      jwt.verify(token, secret, options, onJwtVerificationReady);
    };

    getSecret(req, options.secret, token, onSecretReady);
  };
}
```
- example usage
```shell
...
'''

## Example usage

'''javascript
// set authorization for socket.io
io.sockets
  .on('connection', socketioJwt.authorize({
    secret: 'your secret or public key',
    timeout: 15000 // 15 seconds to send the authentication message
  })).on('authenticated', function(socket) {
    //this socket is authenticated, we are good to handle more events from it.
    console.log('hello! ' + socket.decoded_token.name);
  });
'''
...
```



# <a name="apidoc.module.socketio-jwt.UnauthorizedError"></a>[module socketio-jwt.UnauthorizedError](#apidoc.module.socketio-jwt.UnauthorizedError)

#### <a name="apidoc.element.socketio-jwt.UnauthorizedError.UnauthorizedError"></a>[function <span class="apidocSignatureSpan">socketio-jwt.</span>UnauthorizedError (code, error)](#apidoc.element.socketio-jwt.UnauthorizedError.UnauthorizedError)
- description and source-code
```javascript
function UnauthorizedError(code, error) {
  Error.call(this, error.message);
  this.message = error.message;
  this.inner = error;
  this.data = {
    message: this.message,
    code: code,
    type: "UnauthorizedError"
  };
}
```
- example usage
```shell
n/a
```



# <a name="apidoc.module.socketio-jwt.UnauthorizedError.prototype"></a>[module socketio-jwt.UnauthorizedError.prototype](#apidoc.module.socketio-jwt.UnauthorizedError.prototype)

#### <a name="apidoc.element.socketio-jwt.UnauthorizedError.prototype.constructor"></a>[function <span class="apidocSignatureSpan">socketio-jwt.UnauthorizedError.prototype.</span>constructor (code, error)](#apidoc.element.socketio-jwt.UnauthorizedError.prototype.constructor)
- description and source-code
```javascript
function UnauthorizedError(code, error) {
  Error.call(this, error.message);
  this.message = error.message;
  this.inner = error;
  this.data = {
    message: this.message,
    code: code,
    type: "UnauthorizedError"
  };
}
```
- example usage
```shell
n/a
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
