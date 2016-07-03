<h1 align="center">choo</h1>

<div align="center">
  :steam_locomotive::train::train::train::train::train:
</div>
<div align="center">
  <strong>Fun functional programming</strong>
</div>
<div align="center">
  A <code>5kb</code> framework for creating sturdy frontend applications
</div>

<br />

<div align="center">
  <!-- Stability -->
  <a href="https://nodejs.org/api/documentation.html#documentation_stability_index">
    <img src="https://img.shields.io/badge/stability-experimental-orange.svg?style=flat-square"
      alt="API stability" />
  </a>
  <!-- NPM version -->
  <a href="https://npmjs.org/package/choo">
    <img src="https://img.shields.io/npm/v/choo.svg?style=flat-square"
      alt="NPM version" />
  </a>
  <!-- Build Status -->
  <a href="https://travis-ci.org/yoshuawuyts/choo">
    <img src="https://img.shields.io/travis/yoshuawuyts/choo/master.svg?style=flat-square"
      alt="Build Status" />
  </a>
  <!-- Test Coverage -->
  <a href="https://codecov.io/github/yoshuawuyts/choo">
    <img src="https://img.shields.io/codecov/c/github/yoshuawuyts/choo/master.svg?style=flat-square"
      alt="Test Coverage" />
  </a>
  <!-- Downloads -->
  <a href="https://npmjs.org/package/choo">
    <img src="https://img.shields.io/npm/dm/choo.svg?style=flat-square"
      alt="Downloads" />
  </a>
  <!-- Standard -->
  <a href="https://codecov.io/github/yoshuawuyts/choo">
    <img src="https://img.shields.io/badge/code%20style-standard-brightgreen.svg?style=flat-square"
      alt="Standard" />
  </a>
  <!-- IRC -->
  <a href="https://webchat.freenode.net/?channels=choo">
    <img src="https://img.shields.io/badge/irc-freenode-blue.svg?style=flat-square"
      alt="IRC - Freenode" />
  </a>
</div>

<div align="center">
  <h3>
    Workshop
    <span>|</span>
    Packages
    <span>|</span>
    <a href="https://github.com/yoshuawuyts/choo/blob/master/.github/CONTRIBUTING.md">
      Contributing
    </a>
  </h3>
</div>

<div align="center">
  <sub>The little framework that could. Built with ❤︎ by
  <a href="https://twitter.com/yoshuawuyts">Yoshua Wuyts</a> and
  <a href="https://github.com/yoshuawuyts/choo/graphs/contributors">
    contributors
  </a>
</div>

<h2>Table of Contents</h2>
<details>
  <summary>Table of Contents</summary>
  <li><a href="#features">Features</a></li>
  <li><a href="#demos">Demos</a></li>
  <li><a href="#example">Example</a></li>
  <li><a href="#concepts">Concepts</a></li>
  <li><a href="#api">API</a></li>
  <li><a href="#errors">Errors</a></li>
  <li><a href="#faq">FAQ</a></li>
  <li><a href="#installation">Installation</a></li>
  <li><a href="#see-also">See Also</a></li>
</details>

## Features
- __minimal size:__ weighing `5kb`, `choo` is a tiny little framework
- __single state:__ immutable single state helps reason about changes
- __small api:__ with only 6 methods, there's not a lot to learn
- __minimal tooling:__ built for the cutting edge `browserify` compiler
- __transparent side effects:__ using `effects` and `subscriptions` brings
  clarity to IO
- __omakase:__ composed out of a balanced selection of open source packages
- __isomorphic:__ renders seamlessly in both Node and browsers
- __very cute:__ choo choo!

## Demos
- [Input example](https://github.com/yoshuawuyts/choo/tree/master/examples/title)
  ([requirebin](http://requirebin.com/?gist=e589473373b3100a6ace29f7bbee3186))
- [HTTP effects example](https://fork-fang.hyperdev.space/)
  ([hyperdev](https://hyperdev.com/#!/project/fork-fang))
- [Mailbox routing example](https://github.com/yoshuawuyts/choo/tree/master/examples/mailbox)
  (@examples directory)
- [TodoMVC](http://shuheikagawa.com/todomvc-choo/)
  ([github](https://github.com/shuhei/todomvc-choo))

_note: If you've built something cool using `choo` or are using it in
production, we'd love to hear from you!_

## Getting started
Let's create an input box that changes the content of a textbox in real time.
[Click here to see the final app](http://requirebin.com/?gist=e589473373b3100a6ace29f7bbee3186).

```js
const choo = require('choo')
const html = require('choo/html')
const app = choo()

app.model({
  state: { title: 'Set the title' },
  reducers: {
    update: (action, state) => ({ title: action.value })
  }
})

const mainView = (params, state, send) => html`
  <main>
    <h1>${state.title}</h1>
    <input
      type="text"
      oninput=${(e) => send('update', { value: e.target.value })}>
  </main>
`

app.router((route) => [
  route('/', mainView)
])

const tree = app.start()
document.body.appendChild(tree)
```

## Concepts
`choo` cleanly structures internal data flow, so that all pieces of logic can
be combined into a nice, cohesive machine. Internally all logic lives within
`models` that contain several properties. `subscriptions` are functions that
are called at startup and have `send()` passed in, so they act as read-only
sources of data. `effects` react to changes, perform an `action` and can then
post the results. `reducers` take data, modify it, and update the internal
`state`.

Communication of data is done using objects called `actions`. Each `action` has
any number of properties for data, and a unique `type` that can trigger
properties on the models.

When a `reducer` modifies `state`, the `router` is called, which in turn calls
`views`. `views` take `state` and return [DOM][dom] nodes which are then
efficiently rendered on the screen.

In turn when the `views` are rendered, the `user` can interact with elements by
clicking on them, triggering `actions` which then flow back into the
application logic. This is the _unidirectional_ architecture of `choo`.
```txt
 ┌─────────────────┐
 │  Subscriptions ─┤     User ───┐
 └─ Effects  ◀─────┤             ▼
 ┌─ Reducers ◀─────┴──Actions── DOM ◀┐
 │                                   │
 └▶ Router ─────State ───▶ Views ────┘
```
- __user:__ 🙆
- __DOM:__ the [Document Object Model][dom] is what is currently displayed in
  your browser
- __actions:__ a named event with optional properties attached. Used to call
  `effects` and `reducers` that have been registered in `models`
- __model:__ optionally namespaced object containing `subscriptions`,
  `effects`, `reducers` and initial `state`
- __subscriptions:__ read-only data sources that emit `actions`
- __effects:__ asynchronous functions that emit an `action` when done
- __reducers:__ synchronous functions that modify `state`
- __state:__ a single object that contains __all__ the values used in your
  application
- __router:__ determines which `view` to render
- __views:__ take `state` and returns a new `DOM tree` that is rendered in the
  browser

## Models
`models` are objects that contain initial `state`, `subscriptions`, `effects`
and `reducers`. They're generally grouped around a theme (or domain, if you
like). To provide some sturdiness to your `models`, they can either be
namespaced or not. Namespacing means that only state within the model can be
accessed. Models can still trigger actions on other models, though it's
recommended to keep that to a minimum.

So say we have a `todos` namespace, an `add` reducer and a `todos` model.
Outside the model they're called by `send('todos:add')` and
`state.todos.todos`. Inside the namespaced model they're called by
`send('todos:add')` and `state.todos`. An example namespaced model:
```js
const app = choo()
app.model({
  namespace: 'todos',
  state: { todos: [] },
  reducers: {
    add: (action, state) => ({ todos: state.todos.concat(action.payload) })
  }
})
```

In most cases using namespaces is beneficial, as having clear boundaries makes
it easier to follow logic. But sometimes you need to call `actions` that
operate over multiple domains (such as a "logout" `action`), or have a
`subscription` that might trigger multiple `reducers` (such as a `websocket`
that calls a different `action` based on the incoming data).

In these cases you probably want to have a `model` that doesn't use namespaces,
and has access to the full application state. Try and keep the logic in these
`models` to a minimum, and declare as few `reducers` as possible. That way the
bulk of your logic will safely shielded, with only a few points touching every
part of your application.

## Effects
Side effects are done through `effects` declared in `app.model()`. Unlike
`reducers` they cannot modify the state by returning objects, but get a
callback passed which is used to emit `actions` to handle results. Use effects
every time you don't need to modify the state object directly, but wish to
respond to an action.

A typical `effect` flow looks like:

1. An action is received
2. An effect is triggered
3. The effect performs an async call
4. When the async call is done, either a success or error action is emitted
5. A reducer catches the action and updates the state

## Subscriptions
Subscriptions are a way of receiving data from a source. For example when
listening for events from a server using `SSE` or `Websockets` for a
chat app, or when catching keyboard input for a videogame.

An example subscription that logs `"dog?"` every second:
```js
const app = choo()
app.model({
  namespace: 'app',
  subscriptions: [
    (send) => setInterval(() => send('app:print', { payload: 'dog?' }), 1000)
  ],
  effects: {
    print: (action, state) => console.log(action.payload)
  }
})
```

## Router
The `router` manages which `views` are rendered at any given time. It also
supports rendering a default `view` if no routes match.

```js
const app = choo()
app.router('/404', (route) => [
  route('/', require('./views/empty')),
  route('/404', require('./views/error')),
  route('/:mailbox', require('./views/mailbox'), [
    route('/:message', require('./views/email'))
  ])
])
```

Routes on the `router` are passed in as a nested array. This means that the
entry point of the application also becomes a site map, making it easier to
figure out how views relate to each other.

Under the hood `choo` uses [sheet-router][sheet-router]. Internally the
currently rendered route is kept in `state.app.location`. If you want to modify
the location programmatically the `reducer` for the location can be called
using `send('app:location', { location: href })`. This will not work from
within namespaced `models`, and usage should preferably be kept to a minimum.
Changing views all over the place tends to lead to messiness.

## Views
Views are pure functions that return a DOM tree for the router to render. They’re passed the current state, and any time the state changes they’re run again with the new state.

Views are also passed the `send` function, which they can use to dispatch actions that can update the state. For example, the DOM tree can have an `onclick` handler that dispatches an `add` action.

```javascript
const view = (params, state, send) => {
  return html`
    <div>
      <h1>Total todos: ${state.todos.length}</h1>
      <button onclick=${(e) => send('add', { payload: {title: 'demo'})}>Add</button>
    </div>`
}
```
In this example, when the `Add` button is clicked, the view will dispatch an `add` action that the model’s `add` reducer will receive. [As seen above](#models), the reducer will add an item to the state’s `todos` array. The state change will cause this view to be run again with the new state, and the resulting DOM tree will be used to [efficiently patch the DOM](#does-choo-use-a-virtual-dom).

## API
### app = choo()
Create a new `choo` app

### app.model(obj)
Create a new model. Models modify data and perform IO. Takes the following
arguments:
- __namespace:__ optional namespace that prefixes the keys in `state`,
  `reducers` and `effects`. Also limits `actions` called by `send()` to
  in-namespace only.
- __state:__ object. Key value store of initial values
- __reducers:__ object. Syncronous functions that modify state. Each function
  has a signature of `(action, state)`
- __effects:__ object. Asyncronous functions that perform IO. Each function has
  a signature of `(action, state, send)` where `send` is a reference to
  `app.send()`

### app.router(params, state, send)
Creates a new router. See
[`sheet-router`](https://github.com/yoshuawuyts/sheet-router) for full
documentation. Registered views have a signature of `(params, state, send)`,
where `params` is URI partials.

### html = app.toString(route, state?)
Render the application to a string of HTML. Useful for rendering on the server.
First argument is a path that's passed to the router. Second argument is the
state object. When calling `.toString()` instead of `.start()`, all calls to
`send()` are disabled, and `subscriptions`, `effects` and `reducers` aren't
loaded. See [rendering in Node](#rendering-in-node) for an in-depth guide.

### tree = app.start(rootId?, opts)
Start the application. Returns a tree of DOM nodes that can be mounted using
`document.body.appendChild()`. If a valid `id` selector is passed in as the
first argument, the tree will diff against the selected node rather than be
returned. This is useful for [rehydration](#rehydration). Opts can contain the
following values:
- __opts.history:__ default: `true`. Enable a `subscription` to the browser
  history API. e.g. updates the internal `state.location` state whenever the
  browser "forward" and "backward" buttons are pressed.
- __opts.href:__ default: `true`. Handle all relative `<a
  href="<location>"></a>` clicks and update internal `state.location`
  accordingly.
- __opts.hash:__ default: `false`. Enable a `subscription` to the hash change
  event, updating the internal `state.location` state whenever the URL hash
  changes (eg `localhost/#posts/123`). Enabling this option automatically
  disables `opts.history` and `opts.href`.

### choo/html\`html\`
Tagged template string HTML builder. See
[`yo-yo`](https://github.com/maxogden/yo-yo) for full documentation. Views
should be passed to `app.router()`

## FAQ
### Why did you build this?
`choo` is nothing but a formalization of how I've been building my applications
for the past year. I originally used `virtual-dom` with `virtual-app` and
`wayfarer` where now it's `yo-yo` with `send-action` and `sheet-router`. The
main benefit of using `choo` over these technologies separately is that it
becomes easier for teams to pick up and gather around. The code base for `choo`
itself is super petite (`~200` LOC) and mostly acts to enforce structure around
some excellent npm packages. This is my take on modular frameworks; I hope
you'll find it pleasant.

### Why is it called choo?
Because I thought it sounded cute. All these programs talk about being
"performant", "rigid", "robust" - I like programming to be light, fun and
non-scary. `choo` embraces that.

Also imagine telling some business people you chose to rewrite something
critical to the company using the `choo` framework.
:steam_locomotive::train::train::train:

### Why is it a framework, and not a library?
I love small libraries that do one thing well, but when working in a team,
having an undocumented combination of packages often isn't great. `choo()` is a
small set of packages that work well together, wrapped in an an architectural
pattern. This means you get all the benefits of small packages, but get to be
productive right from the start.

### How does choo compare to X?
Ah, so this is where I get to rant. `choo` (_chugga-chugga-chugga-choo-choo!_)
was built because other options didn't quite cut it for me, so instead of
presenting some faux-objective chart with skewed benchmarks and checklists I'll
give you my opinions directly. Ready?  Here goes:
- __react:__ despite being at the root of a giant paradigm shift for frontend
  (thank you forever!), `react` is kind of big (`155kb` was it?). They also
  like classes a lot, and enforce a _lot_ of abstractions. It also encourages
  the use of `JSX` and `babel` which break _JavaScript, The Language™_. And all
  that without making clear how code should flow, which is crucial in a team
  setting. I don't like complicated things and in my view `react` is one of
  them. `react` is not for me.
- __mithril:__ never used it, never will. I didn't like the API, but if you
  like it maybe it's worth a shot - the API seems small enough. I wouldn't know
  how pleasant it is past face value.
- __preact:__ a pretty cool idea; seems to fix most of what is wrong with
  `react`. However it doesn't fix the large dependencies `react` seems to use
  (e.g. `react-router` and friends) and doesn't help at all with architecture.
  If `react` is your jam, and you will not budge, sitting at `3kb` this is
  probably a welcome gift.
- __angular:__ definitely not for me. I like small things with a clear mental
  model; `angular` doesn't tick any box in my book of nice things.
- __angular2:__ I'm not sure what's exactly changed, but I know the addition of
  `TypeScript` and `RxJS` definitely hasn't made things simpler. Last I checked
  it was `~200kb` in size before including some monstrous extra deps. I guess
  `angular` and I will just never get along.
- __mercury:__ ah, `mercury` is an interesting one. It seemed like a brilliant
  idea until I started using it - the abstractions felt heavy, and it took team
  members a long time to pick up. In the end I think using `mercury` helped
  shaped `choo` greatly, despite not working out for me.
- __deku:__ `deku` is fun. I even contributed a bit in the early days. It could
  probably best be described as "a functional version of `react`". The
  dependence on `JSX` isn't great, but give it a shot if you think it looks
  neat.
- __cycle:__ `cycle`'s pretty good - unlike most frameworks it lays out a clear
  architecture which helps with reasoning about it. That said, it's built on
  `virtual-dom` and `RxJS` which are a bit heavy for my taste. `choo` works
  pretty well for FRP style programming, but something like [inu][inu] might be
  an interesting alternative.
- __vue:__ like `cycle`, `vue` is pretty good. But it also uses tech that
  provides framework lock in, and additionally doesn't have a clean enough
  architecture. I appreciate what it does, but don't think it's the answer.

### Why can't send() be called on the server?
In Node, `reducers`, `effects` and `subscriptions` are disabled for performance
reasons, so if `send()` was called to trigger an action it wouldn't work. Try
finding where in the DOM tree `send()` is called, and disable it when called
from within Node.

### Which packages was choo built on?
- __views:__ [`yo-yo`](https://github.com/maxogden/yo-yo),
  [`bel`](https://github.com/shama/bel)
- __models:__ [`barracks`](https://github.com/yoshuawuyts/barracks),
  [`xtend`](https://github.com/raynos/xtend)
- __routes:__ [`sheet-router`](https://github.com/yoshuawuyts/sheet-router)
- __http:__ [`xhr`](https://github.com/Raynos/xhr)

### Does choo use a virtual-dom?
`choo` uses [morphdom][morphdom], which diffs real DOM nodes instead of virtual
nodes. It turns out that [browsers are actually ridiculously good at dealing
with DOM nodes][morphdom-bench], and it has the added benefit of working with
_any_ library that produces valid DOM nodes. So to put a long answer short:
we're using something even better.

### How can I optimize choo?
`choo` really shines when coupled with `browserify` transforms. They can do
things like reduce file size, prune dependencies and clean up boilerplate code.
Consider running some of the following:
- [unassertify](https://github.com/twada/unassertify) - remove `assert()`
  statements which reduces file size. Use as a `--global` transform
- [es2020](https://github.com/yoshuawuyts/es2020) - backport `const`,
  `fat-arrows` and `template strings` to older browsers
- [yo-yoify](https://github.com/shama/yo-yoify) - replace the internal `hyperx`
  dependency with `document.createElement` calls; greatly speeds up performance
  too
- [uglifyify](https://github.com/hughsk/uglifyify) - minify your code using
  UglifyJS2. Use as a `--global` transform
- [bulkify](https://www.npmjs.com/package/bulkify) - transform inline
  [bulk-require](https://www.npmjs.com/package/bulk-require) calls into
  statically resolvable require maps
- [envify](https://github.com/hughsk/envify) - replace `process.env` values
  with plain strings

### Choo + Internet Explorer &amp; Safari
Out of the box `choo` only supports runtimes which support:
* `const`
* `fat-arrow` functions (e.g. `() => {}`)
* `template-strings`

This does not include Safari 9 or any version of IE. If support for these
platforms is required you will have to provide some sort of transform that
makes this functionality available in older browsers.  The test suite uses
[es2020](https://github.com/yoshuawuyts/es2020) as a global transform, but
anything else which might satisfy this requirement is fair game.

Generally for production builds you'll want to run:
```sh
$ NODE_ENV=production browserify \
  -t envify \
  -g yo-yoify \
  -g unassertify \
  -g es2020 \
  -g uglifyify \
  | uglifyjs
```

## Hey, doesn't this look a lot like Elm?
Yup, it's greatly inspired by the `elm` architecture. But contrary to `elm`,
`choo` doesn't introduce a completely new language to build web applications.

### Is it production ready?
Sure.

## Browser Test Status
<a href="https://saucelabs.com/u/yoshuawuyts">
  <img
    src="https://saucelabs.com/browser-matrix/yoshuawuyts.svg"
    alt="Sauce Test Status"/>
</a>

## Installation
```sh
$ npm install choo
```

## See Also
- [budo](https://github.com/mattdesl/budo) - quick prototyping tool for
  `browserify`
- [stack.gl](http://stack.gl/) - open software ecosystem for WebGL
- [yo-yo](https://github.com/maxogden/yo-yo) - tiny library for modular UI
- [bel](https://github.com/shama/bel) - composable DOM elements using template
  strings
- [tachyons](https://github.com/tachyons-css/tachyons) - functional CSS for
  humans
- [sheetify](https://github.com/stackcss/sheetify) - modular CSS bundler for
  browserify
- [pull-stream](https://github.com/pull-stream/pull-stream) - minimal streams

## License
[MIT](https://tldrlegal.com/license/mit-license)

[dom]: https://en.wikipedia.org/wiki/Document_Object_Model
[keyboard-support]: https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent#Browser_compatibility
[sse]: https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events
[ws]: https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API
[isomorphic]: https://en.wikipedia.org/wiki/Isomorphism
[big-o]: https://rob-bell.net/2009/06/a-beginners-guide-to-big-o-notation/
[qps]: https://en.wikipedia.org/wiki/Queries_per_second
[morphdom]: https://github.com/patrick-steele-idem/morphdom
[morphdom-bench]: https://github.com/patrick-steele-idem/morphdom#benchmarks
[module-parent]: https://nodejs.org/dist/latest-v6.x/docs/api/modules.html#modules_module_parent
[sse-reconnect]: http://stackoverflow.com/questions/24564030/is-an-eventsource-sse-supposed-to-try-to-reconnect-indefinitely
[ws-reconnect]: http://stackoverflow.com/questions/13797262/how-to-reconnect-to-websocket-after-close-connection
[bl]: https://github.com/rvagg/bl
[varnish]: https://varnish-cache.org
[nginx]: http://nginx.org/
[dom]: https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model
[sheet-router]: https://github.com/yoshuawuyts/sheet-router
[html-input]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input
[inu]: https://github.com/ahdinosaur/inu
