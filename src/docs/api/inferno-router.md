# inferno-router

Inferno Router is a routing library for **Inferno**

Usage of `inferno-router` is similar to that of [react-router](https://github.com/ReactTraining/react-router/blob/master/docs/API.md).  

## Install

```
npm install inferno-router@beta37
```

## Features

* Router / RouterContext
* Route / IndexRoute
* Link / IndexLink
* browserHistory / memoryHistory
* onEnter / onLeave hooks
* params / querystring parsing

## Usage (client-side)

```js
import Inferno from 'inferno';
import { Router, Route, IndexRoute } from 'inferno-router';
import createBrowserHistory from 'history/createBrowserHistory';

const browserHistory = createBrowserHistory();

function App({ children }) {
  // ...
}

function NoMatch({ children }) {
  // ...
}

function Home({ children }) {
  // ...
}

// `children` in this case will be the `User` component
function Users({ children, params }) {
  return <div>{ children }</div>
}

function User({ params }) {
  return <div>{ params.username }</div>
}

const routes = (
  <Router history={ browserHistory }>
    <Route component={ App }>
      <IndexRoute component={ Home }/>
      <Route path="users" component={ Users }>
        <Route path="/user/:username" component={ User }/>
      </Route>
      <Route path="*" component={ NoMatch }/>
    </Route>
  </Router>
);

// Render HTML on the browser
Inferno.render(routes, document.getElementById('root'));
```

## Server-side rendering (express)

```js
import Inferno from 'inferno';
import { renderToString } from 'inferno-server'
import { RouterContext, match } from 'inferno-router';
import express from 'express';
import routes from './routes';

function Html({ children }) {
  return (
    <html>
      <head>
        <title>My Application</title>
      </head>
      <body>
        <div id="root">{children}</div>
      </body>
  </html>
  );
}

const app = express();

app.use((req, res) => {
  const renderProps = match(routes, req.originalUrl);
  const content = (<Html><RouterContext {...renderProps}/></Html>);

  res.send('<!DOCTYPE html>\n' + renderToString(content));
});
```

## Server-side rendering (koa v2)

```js
import Inferno from 'inferno';
import { renderToString } from 'inferno-server'
import { RouterContext, match } from 'inferno-router';
import Koa from 'koa';
import routes from './routes';

function Html({ children }) {
  return (
    <html>
      <head>
        <title>My Application</title>
      </head>
      <body>
        <div id="root">{children}</div>
      </body>
  </html>
  );
}

const app = new Koa()

app.use(async(ctx, next) => { 
  const renderProps = match(routes, ctx.url);
  const content = (<Html><RouterContext {...renderProps}/></Html>);
  
  ctx.body = '<!DOCTYPE html>\n' + renderToString(content);
  await next();
});
```

## onEnter / onLeave hooks

In some cases, you may need to execute some logic before or after routing.
You can easily do this by passing a `function` to the `Route` component via a prop, as shown below:

```js
import Inferno from 'inferno';
import { Router, IndexRoute } from 'inferno-router';
import createBrowserHistory from 'history/createBrowserHistory';

function Home({ params }) {
  // ...
}

function authorizedOnly({ props, router }) {
  if (!props.loggedIn) {
    router.push('/login');
  }
}

function sayGoodBye({ props, router }) {
  alert('Good bye!')
}

Inferno.render((
  <Router history={ createBrowserHistory() }>
    <IndexRoute component={ Home } onEnter={ authorizedOnly } onLeave={ sayGoodBye } />
  </Router>
), container);
```

## Notes

* `<IndexRoute>` is the same as `<Route path="/">"`
* `<IndexLink>` is the same as `<Link to="/">`

