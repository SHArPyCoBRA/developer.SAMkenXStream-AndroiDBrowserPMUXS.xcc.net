---
layout: 'layouts/doc-post.njk'
title: workbox-window
date: 2019-02-24
updated: 2021-03-19
description: >
  A module that helps with registering a service worker, managing updates, and responding to lifecycle events.
---

The `workbox-window` package is a set of modules that are intended to run in the
[`window` context](https://developer.mozilla.org/docs/Web/API/Window), which
is to say, inside of your web pages. They're a complement to the other workbox
packages that run in the service worker.

The key features/goals of `workbox-window` are:

- To simplify the process of service worker registration and updates by helping
  developers identify the [most critical moments in the service worker
  lifecycle](#important-service-worker-lifecycle-moments), and making it easier
  to respond to those moments.
- To help prevent developers from making the [most common
  mistakes](#avoiding-common-mistakes).
- To enable [easier communication](#window-to-service-worker-communication)
  between code running in the service worker and code running in the window.

## Importing and using workbox-window

The primary entry point for `workbox-window` package is the `Workbox` class, and
you can import it in your code either from our CDN or using any of the popular
JavaScript bundling tools.

### Using our CDN

The easiest way to import the `Workbox` class on your site is from our CDN:

```html
<script type="module">
  import {Workbox} from 'https://storage.googleapis.com/workbox-cdn/releases/6.4.1/workbox-window.prod.mjs';

  if ('serviceWorker' in navigator) {
    const wb = new Workbox('/sw.js');

    wb.register();
  }
</script>
```

Note that this example uses [`<script type="module">` and the `import`
statement](https://v8.dev/features/modules) to
load the `Workbox` class. While you might think that you need to transpile this
code to get it working in older browsers, that's actually not necessary.

All major browsers that [support
service](https://caniuse.com/#feat=serviceworkers) worker also [support native
JavaScript modules](https://caniuse.com/#feat=es6-module), so it's perfectly
fine to serve this code to any browsers (older browsers will just ignore it).

### Loading Workbox with JavaScript bundlers

While absolutely no tooling is required to use `workbox-window`, if your
development infrastructure already includes a bundler like
[webpack](https://webpack.js.org/) or [Rollup](https://rollupjs.org) that works
with [npm](https://www.npmjs.com/) dependencies, it's possible to use them to
load `workbox-window`.

The first step is to
[install](https://docs.npmjs.com/downloading-and-installing-packages-locally)
`workbox-window` as a dependency of your application:

```shell
npm install workbox-window
```

Then, in one of your application's JavaScript files, `import` workbox by
referencing the `workbox-window` package name:

```js
import {Workbox} from 'workbox-window';

if ('serviceWorker' in navigator) {
  const wb = new Workbox('/sw.js');

  wb.register();
}
```

If your bundler supports [code splitting via dynamic import
statements](https://v8.dev/features/dynamic-import),
you can also conditionally load `workbox-window`, which should help reduce the
size of your page's main bundle.

Even though `workbox-window` is quite small, there's no reason it
needs to be loaded with your site's core application logic, as service workers,
by their very nature, are a progressive enhancement.

```js
if ('serviceWorker' in navigator) {
  const {Workbox} = await import('workbox-window');

  const wb = new Workbox('/sw.js');
  wb.register();
}
```

### Advanced bundling concepts

Unlike the Workbox packages that run in the service worker, the build files
referenced by `workbox-window`'s
[`main`](https://docs.npmjs.com/files/package.json#main) and
[`module`](https://github.com/rollup/rollup/wiki/pkg.module) fields in
`package.json` are transpiled to ES5. This makes them compatible with today's
build tools&mdash;some of which do not allow developers to transpile anything of
their `node_module` dependencies.

If your build system _does_ allow you to transpile your dependencies (or if you
don't need to transpile any of your code), then it's better to import a specific
source file rather than the package itself.

Here are the various ways you can import `Workbox`, along with an explanation of
what each will return:

```js
// Imports a UMD version with ES5 syntax
// (pkg.main: "build/workbox-window.prod.umd.js")
const {Workbox} = require('workbox-window');

// Imports the module version with ES5 syntax
// (pkg.module: "build/workbox-window.prod.es5.mjs")
import {Workbox} from 'workbox-window';

// Imports the module source file with ES2015+ syntax
import {Workbox} from 'workbox-window/Workbox.mjs';
```

{% Aside 'caution' %}
If you're importing the source file directly, you'll also need to configure
your build process to minify the file, and remove development-only code when
you deploy it to production. See the guide
[Using Bundlers (webpack/Rollup) with Workbox](/docs/workbox/the-ways-of-workbox/#using-a-bundler)
for more details.
{% endAside %}

### Examples

Once you've imported the `Workbox` class, you can use it to register and
interact with your service worker. Here are some examples of ways you might use
`Workbox` in your application:

#### Register a service worker and notify the user the very first time that service worker is active

Many web applications user service worker to precache assets so their app works
offline on subsequent page loads. In some cases it could make sense to inform
the user that the app is now available offline.

```js
const wb = new Workbox('/sw.js');

wb.addEventListener('activated', event => {
  // `event.isUpdate` will be true if another version of the service
  // worker was controlling the page when this version was registered.
  if (!event.isUpdate) {
    console.log('Service worker activated for the first time!');

    // If your service worker is configured to precache assets, those
    // assets should all be available now.
  }
});

// Register the service worker after event listeners have been added.
wb.register();
```

#### Notify the user if a service worker has installed but is stuck waiting to activate

When a page controlled by an existing service worker registers a new service
worker, by default that service worker will not activate until all clients
controlled by the initial service worker have fully unloaded.

This is a common source of confusion for developers, especially in cases where
[reloading the current page doesn't cause the new service worker to
activate](https://web.dev/service-worker-lifecycle/#waiting).

To help minimize confusion and make it clear when this situation is happening,
the `Workbox` class provides a `waiting` event that you can listen for:

```js
const wb = new Workbox('/sw.js');

wb.addEventListener('waiting', event => {
  console.log(
    `A new service worker has installed, but it can't activate` +
      `until all tabs running the current version have fully unloaded.`
  );
});

// Register the service worker after event listeners have been added.
wb.register();
```

#### Notify the user of cache updates from the `workbox-broadcast-update` package

The [`workbox-broadcast-update`
package](/docs/workbox/modules/workbox-broadcast-update) is a great way to be able to serve content from the cache (for fast delivery) while also
being able to inform the user of updates to that content (using the
[stale-while-revalidate
strategy](/docs/workbox/modules/workbox-strategies#stale-while-revalidate)).

To receive those updates from the window, you can listen to `message` events of
type `CACHE_UPDATED`:

```js
const wb = new Workbox('/sw.js');

wb.addEventListener('message', event => {
  if (event.data.type === 'CACHE_UPDATED') {
    const {updatedURL} = event.data.payload;

    console.log(`A newer version of ${updatedURL} is available!`);
  }
});

// Register the service worker after event listeners have been added.
wb.register();
```

#### Send the service worker a list of URLs to cache

For some applications, it's possible to know all the assets that need to be
precached at build time, but some applications serve completely different pages,
based on what URL the user lands on first.

For apps in the latter category, it might make sense to only cache the assets
the user needed for the particular page they visited. When using the
[`workbox-routing` package](/docs/workbox/modules/workbox-routing), you can
send your router a list of URLs to cache, and it will cache those URLs according
to the rules defined on the router itself.

This example sends a list of URLs loaded by the page to the router any time a
new service worker is activated. Note, it's fine to send _all_ URLs because only
the URLs that match a defined route in the service worker will be cached:

```js
const wb = new Workbox('/sw.js');

wb.addEventListener('activated', event => {
  // Get the current page URL + all resources the page loaded.
  const urlsToCache = [
    location.href,
    ...performance.getEntriesByType('resource').map(r => r.name),
  ];
  // Send that list of URLs to your router in the service worker.
  wb.messageSW({
    type: 'CACHE_URLS',
    payload: {urlsToCache},
  });
});

// Register the service worker after event listeners have been added.
wb.register();
```

{% Aside %}
The above technique works for any route defined via the
[`registerRoute()`](/docs/workbox/reference/workbox-routing/#method-registerRoute)
method on the default router. If you're creating your own [`Router`](/docs/workbox/reference/workbox-routing/#type-Router) instance,
you'll need to call its `addCacheListener()` method manually.
{% endAside %}

## Important service worker lifecycle moments

The [service worker
lifecycle](https://web.dev/articles/service-worker-lifecycle)
is complex and can be a challenge to fully understand. Part of the reason it's
so complex is it must handle all the edge cases for all possible usages of
service worker (e.g. registering more than one service worker, registering
different service workers in different frames, registering service workers with
different names, etc.).

But most developers implementing service worker should not need to worry about
all these edge cases because their usage is quite simple. Most developer
register just one service worker per page load, and they [don't change the name
of the service
worker](https://web.dev/articles/service-worker-lifecycle#avoid_url_change)
file they deploy to their server.

The `Workbox` class embraces this simpler view for the service worker lifecycle
by breaking all service worker registrations into two categories: the instance's
own, registered service worker and an external service worker:

- **Registered service worker**: a service worker that started installing as a
  result of the `Workbox` instance calling `register()` or the already-active
  service worker if calling `register()` did not trigger an [`updatefound`](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration/onupdatefound) event on the registration.
- **External service worker:** a service worker that started installing
  independently of the `Workbox` instance calling `register()`. This typically
  happens when a user has a new version of your site open in another tab. When an
  event originates from an external service worker, the event's `isExternal`
  property will be set to `true`.

With these two types of service workers in mind, here is a breakdown of all the
important service worker lifecycle moments, along with developer recommendations
for how to handle them:

### The very first time a service worker is installed

You'll probably want to treat the very first time a service worker install
differently from how you treat all future updates.

In `workbox-window`, you can differentiate between the version first
installation and future updates by checking the `isUpdate` property on any of
the following events. For the very first installation, `isUpdate` will be
`false`.

```js
const wb = new Workbox('/sw.js');

wb.addEventListener('installed', event => {
  if (!event.isUpdate) {
    // First-installed code goes here...
  }
});

wb.register();
```

<div class="table-wrapper scrollbar">
  <table>
    <thead>
      <tr>
        <th>Moment</th>
        <th>Event</th>
        <th>Recommended action</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>A new service worker has installed (for the first time)</td>
        <td><code>installed</code></td>
        <td>
          <p>The very first time a service worker installs, it's common to precache
          all the assets needed for the site to work offline. You might consider
          informing the user that their site can now function offline.</p>
          <p>Also, since the very first time a service worker installs it won't have
          intercepted fetch events for that page load, you may also consider caching
          assets that have already been loaded (though this is not needed if those
          assets are already being precached). The <a href="#cache-urls">send the
          service worker a list of URLs to cache</a> example above shows how to do
          this.</p>
        </td>
      </tr>
      <tr>
        <td>The service worker has started controlling the page</td>
        <td><code>controlling</code></td>
        <td>
          <p>Once a new service worker is installed and starts controlling the page,
          all subsequent fetch events will go through that service worker. If your
          service worker adds any special logic to handle particular fetch event,
          this is the point when you know that logic will run.</p>
          <p>Note that the very first time you install a service worker, it <em>will
          not</em> start controlling the current page unless that service worker
          calls <a href="https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#clientsclaim">
          <code>clients.claim()</code></a> in its activate event. The default
          behavior is to wait until the next page load to start controlling.</p>
          <p>From the <code>workbox-window</code> perspective, this means the
          <code>controlling</code> event is only dispatched in cases where the
          service worker calls <code>clients.claim()</code>. This event is not
          dispatched if the page was already controlled prior to registration.</p>
       </td>
      </tr>
      <tr>
        <td>The service worker has finished activating</td>
        <td><code>activated</code></td>
        <td>
          <p>As mentioned above, the very first time a service worker finishes
          activating it may (or may not) have started controlling the page.
          <p>For this reason, you should not listen for the activate event as a way
          of knowing when the service worker is in control of the page. However, if
          you're running logic in the active event (in the service worker) and you
          need to know when that logic is complete, the activated event will let you
          know that.</p>
       </td>
      </tr>
    </tbody>
  </table>
</div>

### When an updated version of the service worker is found

When a new service worker starts installing but an existing version is currently
controlling the page, the `isUpdate` property of all the following events will
be `true`.

How you react in this situation is typically different from the very first
installation because you have to manage when and how the user gets this update.

<div class="table-wrapper scrollbar">
  <table>
    <thead>
      <tr>
        <th>Moment</th>
        <th>Event</th>
        <th>Recommended action</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>A new service worker has installed (updating a previous one)</td>
        <td><code>installed</code></td>
        <td>
          <p>If this is not the very first service worker install
          (<code>event.isUpdate === true</code>), it means a newer version of the
          service worker has been found and installed (that is, a different version
          from the one currently controlling the page).</p>
          <p>This typically means a newer version of the site has been deployed to
          your server, and new assets may have just finished precaching.</p>
          <p>Note: some developers use the <code>installed</code> event to inform
          users that a new version of their site is available. However, depending on
          whether you call
          <a href="https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase">
          <code>skipWaiting()</code></a> in the installing service worker, that
          installed service worker may or may not become active right away. If you
          <em>do</em> call <code>skipWaiting()</code> then it's best to inform users
          of the update once the new service worker has activated, and if you
          <em>don't</em> call <code>skipWaiting</code> it's better to inform them of
          the pending update in the waiting event (see below for more details).</p>
       </td>
      </tr>
      <tr>
        <td>A service worker has installed but it's stuck in the waiting phase</td>
        <td><code>waiting</code></td>
        <td>
          <p>If the updated version of your service worker does not call <a
          href="https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase">
          <code>skipWaiting()</code></a> while it's being installed, it will not
          activate until all pages controlled by the currently active service worker
          have unloaded. You may want to inform the user that an update is available
          and will be applied the next time they visit.</p>
          <p><strong>Warning!</strong> it's common for developers to prompt users
          reload to get the update, but in many cases <a
          href="https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase">
          refreshing the page will not activate the installed worker</a>. If the
          user refreshes the page and the service worker is <em>still</em> waiting,
          the <code>waiting</code> event will fire again and the
          <code>event.wasWaitingBeforeRegister</code> property will be true. Note,
          we plan to improve this experience in a future release. Follow <a
          href="https://github.com/GoogleChrome/workbox/issues/1848">issue #1848</a>
          for updates.</p>
          <p>Another option is to prompt the user and ask whether they want to get
          the update or continue waiting. If the choose to get the update, you can
          use <code>postMessage()</code> to tell the service worker to run
          <code>skipWaiting()</code>. See the advanced recipe
          <a href="https://developers.google.com/web/tools/workbox/guides/advanced-recipes#offer_a_page_reload_for_users">
          offer a page reload for users</a> for an example of that.</p>
       </td>
      </tr>
      <tr>
        <td>The service worker has started controlling the page</td>
        <td><code>controlling</code></td>
        <td>
          <p>When an updated service worker starts controlling the page, it means
          the version of your service worker currently controlling is different from
          the version that was in control when the page was loaded. In some cases
          that may be fine, but it could also mean some assets referenced by the
          current page are no longer in the cache (and possibly also not on server).
          You may want to consider informing the user that some parts of the page
          may not work correctly.</p>
          <p><strong>Note:</strong> the <code>controlling</code> event will not fire
          if you don't call <code>skipWaiting()</code> in your service worker.</p>
       </td>
      </tr>
      <tr>
        <td>The service worker has finished activating</td>
        <td><code>activated</code></td>
        <td>When an updated service worker has finished activating, it means any
        logic you were running in the service worker's <code>activate</code> has
        completed. If there's anything you need to defer until that logic has
        finished, this is the time to run it.
       </td>
      </tr>
    </tbody>
  </table>
</div>

### When an unexpected version of the service worker is found

Sometimes users will keep your site open in a background tab for a very long
time. They might even open a new tab and navigate to your site without realizing
they already have your site open in a background tab. In such cases it's
possible to have two versions of your site running at the same time, and that
can present some interesting problems for you as the developer.

Consider a scenario where you have tab A running v1 of your site and tab B
running v2. When tab B loads, it'll be controlled by the version of your service
worker that shipped with v1, but the page returned by the server (if using a
[network-first caching
strategy](/docs/workbox/modules/workbox-strategies#network-first-network-falling-back-to-cache)
for your navigation requests) will contain all your v2 assets.

This is generally not a problem for tab B though, since when you wrote your v2
code, you were aware of how your v1 code worked. **However, it could be a
problem for tab A,** since your v1 code could not have possibly predicted what
changes your v2 code might introduce.

To help handle these situations, `workbox-window` also dispatches lifecycle
events when it detects an update from an "external" service worker, where
external just means any version that is not the version the current `Workbox`
instance registered.

As of Workbox v6 and later, these events are equivalent to the events documented
above, with the addition of an `isExternal: true` property set on each event
object. If your web application needs to implement specific logic to handle an
"external" service worker, you can check for that property in your event handlers.

## Avoiding common mistakes

One of the most helpful features Workbox provides is it's developer logging. And
this is especially true for `workbox-window`.

We know developing with service worker can often be confusing, and when things
happen contrary to what you'd expect, it can be hard to know why.

For example, when you make a change to your service worker and reload the page,
you might not see that change in your browser. The most likely reason for this,
is your service worker is still waiting to activate.

But when registering a service worker with the `Workbox` class, you'll be
informed of all lifecycle state changes in the developer console, which should
help with debugging why things aren't as you'd expect.

{% Img src="image/jL3OLOhcWUQDnR4XjewLBx4e3PC3/WwEuBm5rIc0snWQRAz5d.png", alt="workbox-window console warning for waiting worker", width="800", height="168" %}

In addition, a common mistake developers make when first using service worker is
to register a service worker in the
[wrong scope](https://web.dev/learn/pwa/service-workers#registering_a_service_worker).

To help prevent this from happening, the `Workbox` class will warn you if the
page registering the service worker is not in that service worker's scope. It'll
also warning you in cases where your service worker is active but not yet
controlling the page:

{% Img src="image/jL3OLOhcWUQDnR4XjewLBx4e3PC3/LHaO36EnHxm429ZPo2Ak.png", alt="workbox-window console warning for non-controlling worker", width="800", height="153" %}

## Window to service worker communication

Most advanced service worker usage involves a lots of messaging between the
service worker and the window. The `Workbox` class helps with this as well by
providing a `messageSW()` method, which will `postMessage()` the instance's
registered service worker and await a response.

While you can send data to the service worker in any format, the format shared
by all Workbox packages is an object with three properties (the latter two being
optional):

<div class="table-wrapper scrollbar">
  <table>
    <thead>
      <tr>
        <th>Property</th>
        <th>Required?</th>
        <th>Type</th>
        <th>Description</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td><strong><code>type</code></strong></td>
        <td><strong>Yes</strong></td>
        <td><code>string</code></td>
        <td>
          <p>A unique string, identifying this message.</p>
          <p>By convention, types are all uppercase with underscores separating
          words. If a type represents an action to be taken, it should be a command
          in present tense (e.g. <code>CACHE_URLS</code>), if type represent
          information being reported, it should be in past tense (e.g.
          <code>URLS_CACHED</code>).</p>
       </td>
      </tr>
      <tr>
        <td><strong><code>meta</code></strong></td>
        <td>no</td>
        <td><code>string</code></td>
        <td>In Workbox this is always the name of the Workbox package sending the
        message. When sending message yourself, you can either omit this property or
        set it to whatever you like.</td>
      </tr>
      <tr>
        <td><strong><code>payload</code></strong></td>
        <td>no</td>
        <td><code>*</code></td>
        <td>The data being sent. Usually this is an object, but it does not have to be.</td>
      </tr>
    </tbody>
  </table>
</div>

Messages sent via the `messageSW()` method use `MessageChannel` so the receiver
can respond to them. To respond to a message you can call
`event.ports[0].postMessage(response)` in your message event listener. The
`messageSW()` method returns a promise that will resolve to whatever `response`
you reply with.

Here's an example of sending messages from the window to the service worker and
getting a response back. The first code block is the message listener in the
service worker, and the second block uses the `Workbox` class to send the
message and await the response:

**Code in sw.js:**

```js
const SW_VERSION = '1.0.0';

addEventListener('message', event => {
  if (event.data.type === 'GET_VERSION') {
    event.ports[0].postMessage(SW_VERSION);
  }
});
```

**Code in main.js (running in the window):**

```js
const wb = new Workbox('/sw.js');
wb.register();

const swVersion = await wb.messageSW({type: 'GET_VERSION'});
console.log('Service Worker version:', swVersion);
```

### Managing version incompatibilities

The example above show how you might implement checking the service worker
version from the window. This example is used because when you're sending
messages back and forth between the window and the service worker, it's critical
to be aware that your service worker might not be running the same version of
your site that your page code is running, and the solution for dealing with this
problem is different depending on whether your serving your pages network-first
or cache-first.

#### Network first

When serving your pages network first, your users will always be getting the
latest version of your HTML from your server. However, the first time a user
revisits your site (after you've deployed an update) the HTML they get will be
for the latest version, but the service worker running in their browser will be
a version installed previously (possibly many versions old).

It's important to understand this possibility because if the JavaScript loaded
by the current version of your page sends a message to an older version of your
service worker, that version may not know how to respond (or it may respond with
an incompatible format).

As a result, it's a good idea to always version your service worker and check
for compatible versions before doing any critical work.

For example, in the code above, if the service worker version returned by that
`messageSW()` call is older than the expected version, it would be wise to wait
until an update is found (which should happen when you call `register()`). At
that point you can either notify the user or an update, or you can manually
[skip the waiting
phase](https://web.dev/articles/service-worker-lifecycle#skip_the_waiting_phase)
to activate the new service worker right away.

#### Cache first

As opposed to when you serve pages network-first, when serving your pages cache-
first, you know your page is initially always going to be the same version as
your service worker (because that's what served it). And as a result, it's safe
to use `messageSW()` right away.

However, if an updated version of your service worker is found and activates
when your page calls `register()` (i.e. you intentionally [skip the waiting
phase](https://web.dev/articles/service-worker-lifecycle#skip_the_waiting_phase)),
it may no longer be safe to send messages to it.

One strategy for managing this possibility is to use a versioning scheme that
allows you to differentiate between breaking updates and non-breaking updates,
and in the case of a breaking update you'd know it's not safe to message the
service worker. Instead you'd want to warn the user that they're running an old
version of the page, and suggest they reload to get the update.

### Skip waiting helper

A common use convention for window to service worker messaging is send a
`{type: 'SKIP_WAITING'}` message to instruct a service worker that's installed to
[skip the waiting phase](https://web.dev/articles/service-worker-lifecycle#skip_the_waiting_phase)
and activate.

Starting with Workbox v6, the `messageSkipWaiting()` method can be used to send a
`{type: 'SKIP_WAITING'}` message to the waiting service worker associated with the
current service worker registration. It will silently do nothing if there isn't a
waiting service worker.

{% Aside %}
For further guidance on using `messageSkipWaiting()`, see the
"[Handling service worker updates with immediacy](/docs/workbox/handling-service-worker-updates/)" recipe.
{% endAside %}
