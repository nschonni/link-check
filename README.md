# link-check

Checks whether a hyperlink is alive (`200 OK`) or dead.

## Installation

    npm install --save link-check

## Specification

A link is said to be 'alive' if an HTTP HEAD or HTTP GET for the given URL
eventually ends in a `200 OK` response. To minimize bandwidth, an HTTP HEAD
is performed. If that fails (e.g. with a `405 Method Not Allowed`), an HTTP
GET is performed. Redirects are followed.

In the case of `mailto:` links, this module validates the e-mail address
using [isemail](https://www.npmjs.com/package/isemail).

## API

### linkCheck(link, [opts,] callback)

Given a `link` and a `callback`, attempt an HTTP HEAD and possibly an HTTP GET.

Parameters:

 * `url` string containing a URL.
 * `opts` optional options object containing any of the following optional fields:
   * `baseUrl` the base URL for relative links.
   * `timeout` timeout in [zeit/ms](https://www.npmjs.com/package/ms) format. (e.g. `"2000ms"`, `20s`, 1m`). Default `10s`.
   * `aliveStatusCodes` an array of numeric HTTP Response codes which indicate that the link is alive. Entries in this array may also be regular expressions. Example: `[ 200, /^[45][0-9]{2}$/ ]`.  Default `[ 200 ]`.
   * `headers` a string based attribute value object to send custom HTTP headers. Example: `{ 'Authorization' : 'Basic Zm9vOmJhcg==' }`.
 * `callback` function which accepts `(err, result)`.
   * `err` an Error object when the operation cannot be completed, otherwise `null`.
   * `result` an object with the following properties:
     * `link` the `link` provided as input
     * `status` a string set to either `alive` or `dead`.
     * `statusCode` the HTTP status code. Set to `0` if no HTTP status code was returned (e.g. when the server is down).
     * `err` any connection error that occurred, otherwise `null`.

### LinkCheckResult(link, statusCode, err)

If you need the `LinkCheckResult` class to make your own results or to compare an object
to it with `instanceof`, the class is exposed` via `require('link-check').LinkCheckResult`.

Parameters:

* `link` a string representing the link (e.g. `http://example.org`, `mailto:user@example.org`, etc)
* `statusCode` a numeric code that corresponds to an HTTP status code or 0 if there is no code due to a problem.
* `err` an Error object detailing the problem (if applicable).

The resulting object has the above properties as well as `status` which is either `'dead'` or `'alive'`.

## Examples

```js
'use strict';

const linkCheck = require('link-check');

linkCheck('http://example.com', function (err, result) {
    if (err) {
        console.error(err);
        return;
    }
    console.log(`${result.link} is ${result.status}`);
});
```

**With basic authentication:**

```js
'use strict';

const linkCheck = require('link-check');

linkCheck('http://example.com', { headers: { 'Authorization': 'Basic Zm9vOmJhcg==' } }, function (err, result) {
    if (err) {
        console.error(err);
        return;
    }
    console.log(`${result.link} is ${result.status}`);
});
```

## Testing

    npm test

## License

See [LICENSE.md](https://github.com/tcort/link-check/blob/master/LICENSE.md)
