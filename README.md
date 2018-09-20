# momi

[![Greenkeeper badge](https://badges.greenkeeper.io/Avaq/momi.svg)](https://greenkeeper.io/)

**Mo**nadic **Mi**ddleware

> `npm install --save fantasy-states fluture momi`

Middleware - specifically in the case of Connect, Express and Koa - is a
mechanism which encodes several effects:

- **A build-up of state** through mutation of the `req` parameter
- **An eventual response** through mutation of the `res` parameter
- **Inversion of control** over the continuation by means of calling the `next` parameter
- **Possible error branch** by means of calling the `next` parameter with a value

If we would want to encode all of these effects into a data-structure, we could
use a `StateT(Future) -> StateT(Future)` structure:

- **A build-up of state** through the `State` monad
- **An eventual response** through the right-sided value of `Future`
- **Inversion of control** by passing the whole structure into a function
- **Possible error branch** through the left-sided value of `Future`

In other words, the `StateT(Future)`-structure might be considered the
Middleware monad. This packages exposes the Middleware monad, comprised of
`State` from [fantasy-states][] and `Future` from [Fluture][]. Besides the
monad itself, it also exposes some utility functions and structures for
practically applying Middleware. One such utility is the `App` class,
which allows composition of functions over Middleware to be written more like
what you are used to from middleware as it comes with Connect, Express or Koa.

## Usage

```js
import {App, Middleware} from '../../';
import qs from 'querystring';

const queryParseMiddleware = App.do(function*(next) {
  const req = yield Middleware.get;
  const query = qs.parse(req.url.split('?')[1]);
  yield Middleware.put(Object.assign({query}, req));
  return yield next;
});

const echoMiddleware = Middleware.get.map(req => ({
  status: 200,
  headers: {'X-Powered-By': 'momi'},
  body: req.query.echo
}));

const app = App.empty()
.use(queryParseMiddleware)
.use(_ => echoMiddleware);

App.mount(app, 3000);
```

## Examples

- **[Readme][example-1]** the code from [Usage](#usage), ready to run.
- **[Express][example-2]** shows how to embed Momi within Express.
- **[Bootstrap][example-3]** an extensive example showing application structure.
- **[Real World][example-4]** how momi is being used in real life.

[fantasy-states]: https://github.com/fantasyland/fantasy-states
[Fluture]: https://github.com/Avaq/Fluture
[example-1]: https://github.com/Avaq/momi/tree/master/examples/readme
[example-2]: https://github.com/Avaq/momi/tree/master/examples/express
[example-3]: https://github.com/Avaq/momi/tree/master/examples/bootstrap
[example-4]: https://github.com/Avaq/node-server-skeleton/tree/master/src/bootstrap
