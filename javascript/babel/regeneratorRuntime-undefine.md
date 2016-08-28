# Undefined 'regeneratorRuntime'

Suppose babel is configured correctly.

Original source code:

```javascript
import 'babel-polyfill';
import request from 'request';

function getPage(url) {
  return new Promise((resolve, reject) => {
    request({
      method: 'GET',
      url: url
    }, (error, response, body) => {
      if (error) {
        return reject(error);
      }
      return resolve(body);
    });
  });
}

async function download(url) {
  try {
    const page = await getPage(url);
    console.log(page);
  } catch (error) {
    console.error(error);
  }
}

download('http://www.google.com');
```

Transpiled code:

```javascript
'use strict';

var download = function () {
  var _ref = _asyncToGenerator(regeneratorRuntime.mark(function _callee(url) {
    var page;
    return regeneratorRuntime.wrap(function _callee$(_context) {
      while (1) {
        switch (_context.prev = _context.next) {
          case 0:
            _context.prev = 0;
            _context.next = 3;
            return getPage(url);

          case 3:
            page = _context.sent;

            console.log(page);
            _context.next = 10;
            break;

          case 7:
            _context.prev = 7;
            _context.t0 = _context['catch'](0);

            console.error(_context.t0);

          case 10:
          case 'end':
            return _context.stop();
        }
      }
    }, _callee, this, [[0, 7]]);
  }));

  return function download(_x) {
    return _ref.apply(this, arguments);
  };
}();

require('babel-polyfill');

var _request = require('request');

var _request2 = _interopRequireDefault(_request);

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

function _asyncToGenerator(fn) { return function () { var gen = fn.apply(this, arguments); return new Promise(function (resolve, reject) { function step(key, arg) { try { var info = gen[key](arg); var value = info.value; } catch (error) { reject(error); return; } if (info.done) { resolve(value); } else { return Promise.resolve(value).then(function (value) { return step("next", value); }, function (err) { return step("throw", err); }); } } return step("next"); }); }; }

function getPage(url) {
  return new Promise(function (resolve, reject) {
    (0, _request2.default)({
      method: 'GET',
      url: url
    }, function (error, response, body) {
      if (error) {
        return reject(error);
      }
      return resolve(body);
    });
  });
}

download('http://www.google.com');
```

As you can see the line to include ```babel-polyfill``` is below the line where ```regeneratorRuntime``` is used.

The solution is to separate the original source code into 2 files.

index.js

```javascript
import 'babel-polyfill';
import './app'
```

app.js

```javascript
import request from 'request';

function getPage(url) {
  return new Promise((resolve, reject) => {
    request({
      method: 'GET',
      url: url
    }, (error, response, body) => {
      if (error) {
        return reject(error);
      }
      return resolve(body);
    });
  });
}

async function download(url) {
  try {
    const page = await getPage(url);
    console.log(page);
  } catch (error) {
    console.error(error);
  }
}

download('http://www.google.com');
```


