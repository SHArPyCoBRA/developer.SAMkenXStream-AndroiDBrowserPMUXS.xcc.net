---
layout: "layouts/blog-post.njk"
title: Native App Install Prompt
description: >
  Native app install banners give you the ability to let users quickly and seamlessly install your native app on their device from the app store, without leaving the browser.
date: 2014-12-16
updated: 2023-01-20
---

The native app install prompt gives you the ability to let users quickly and
seamlessly install your native app on their device directly from the app store,
without leaving the browser, and without showing an annoying interstitial.

## What are the criteria?

In order to show the native app install prompt to the user, your site must
meet the following criteria:

* Neither the web app nor the native app currently installed on the device.
* Includes a [Web App Manifest](https://web.dev/add-manifest) that includes:
    - `short_name`
    - `name` (used in the banner prompt)
    - `icons` including a 192px and a 512px version
    - [`prefer_related_applications`](#prefer_related_applications) is `true`
    - [`related_applications`](#related_applications) object with information
      about the app
* Served over **HTTPS**

A `beforeinstallprompt` event will fire when you meet these criteria. You
can use it to prompt the user to install your native app.

{% Aside %}
Chrome only supports the native app install prompt in the
current stable release. On other Chrome channels (Beta/Dev/Canary), the
`beforeinstallprompt` event will **not** fire, even if the above criteria
are met.
{% endAside %}

### Required manifest properties

To prompt the user to install your native app, you need to add two properties
to your web app manifest, `prefer_related_applications` and
`related_applications`.

```json
"prefer_related_applications": true,
"related_applications": [
    {
    "platform": "play",
    "id": "com.google.samples.apps.iosched"
    }
]
```

### `prefer_related_applications`

The `prefer_related_applications` property tells the browser to prompt the
user with your native app instead of the web app. If you leave this value unset,
or `false`, the browser will prompt the user to install the web app instead.

### `related_applications`

`related_applications` is an array with a list of objects that tell the
browser about your preferred native application. Each object must include
a `platform` property and an `id` property. Where the `platform` is `play`
and the `id` is your Play Store app ID.

## Show the install prompt

In order to show the install dialog, you need to:

1. Listen for the `beforeinstallprompt` event.
1. Notify the user your native app can be installed with a button or other
   element that will generate a user gesture event.
1. Show the prompt by calling `prompt()` on the saved `beforeinstallprompt`
   event.

### Listen for `beforeinstallprompt`

If the [criteria](#what-are-the-criteria) are met, Chrome will fire a `beforeinstallprompt`
event. You can use it to indicate your app can be installed, and then prompt
the user to install it.

When the `beforeinstallprompt` event has fired, save a reference to the event,
and update your user interface to indicate that the user can install your app.

```js
let deferredPrompt;

window.addEventListener('beforeinstallprompt', (e) => {
  // Prevent Chrome 67 and earlier from automatically showing the prompt
  e.preventDefault();
  // Stash the event so it can be triggered later.
  deferredPrompt = e;
});
```

### Notify the user your app can be installed

The best way to notify the user your app can be installed is by adding a button
or other element to your user interface. **Don't show a full page [interstitial](https://support.google.com/webdesigner/answer/7334234)
or other elements that may be annoying or distracting.**

```js
window.addEventListener('beforeinstallprompt', (e) => {
  // Prevent Chrome 67 and earlier from automatically showing the prompt
  e.preventDefault();
  // Stash the event so it can be triggered later.
  deferredPrompt = e;
  // Update UI notify the user they can add to home screen
  btnAdd.style.display = 'block';
});
```

{% Aside 'success' %}
You may want to wait before showing the prompt to the user,
so you don't distract them from what they're doing. For example, if the user
is in a check-out flow, or creating their account, let them complete that
before interrupting them with the prompt.
{% endAside %}

### Show the prompt

To show the install prompt, call `prompt()` on the saved event
from within a user gesture. It will show a modal dialog, asking the user
to add your app to their home screen.

Then, listen for the promise returned by the `userChoice` property. The
promise returns an object with an `outcome` property after the prompt has
shown and the user has responded to it.

```js
btnAdd.addEventListener('click', (e) => {
  // hide our user interface that shows our A2HS button
  btnAdd.style.display = 'none';
  // Show the prompt
  deferredPrompt.prompt();
  // Wait for the user to respond to the prompt
  deferredPrompt.userChoice
    .then((choiceResult) => {
      if (choiceResult.outcome === 'accepted') {
        console.log('User accepted the A2HS prompt');
      } else {
        console.log('User dismissed the A2HS prompt');
      }
      deferredPrompt = null;
    });
});
```

You can only call `prompt()` on the deferred event once. If the user dismisses
it, you'll need to wait until the `beforeinstallprompt` event fires on
the next page navigation.

## Special considerations for when using content security policy

If your site has a restrictive [Content Security Policy](https://web.dev/articles/csp),
make sure to add `*.googleusercontent.com` to the `img-src` directive so Chrome
can download the icon associated with your app from the Play Store.

In some cases `*.googleusercontent.com` maybe more verbose than desired. It's
possible to narrow this down by [remote debugging](/docs/devtools/remote-debugging/)
an Android device to determine the URL of the app icon.
