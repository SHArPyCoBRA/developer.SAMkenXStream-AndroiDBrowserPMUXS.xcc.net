---
# Required
layout: 'layouts/blog-post.njk'

# Required
title: Gaining security and privacy by partitioning the cache


# Required
description: >
  Chrome's HTTP cache partitioning helps with better security and privacy.

# Optional
# How to add a new author
# https://developer.chrome.com/docs/handbook/how-to/add-an-author/
authors:
  - agektmr

# Required
date: 2020-10-06

# Optional
# Include an updated date when you update your post
updated: 2020-10-24

# Optional
# How to add a new tag
# https://developer.chrome.com/docs/handbook/how-to/add-a-tag/
#tags:


---

In general, caching can improve performance by storing data so future requests
for the same data are served faster. For example, a cached resource from the
network can avoid a round trip to the server. A cached computational result can
omit the time to do the same calculation.

In Chrome, the cache mechanism is used in various ways and HTTP Cache is one example.

## How Chrome's HTTP Cache currently works

As of version 85, Chrome caches resources fetched from the network, using their
respective resource URLs as the cache key. (A cache key is used to identify a
cached resource.)

The following example illustrates how a single image is cached and treated in
three different contexts:

<figure class="float-left">
  {% Img src="image/T4FyVKpzu4WKF1kBNvXepbi08t52/zqkRCKG9jR3uBtcEwPgV.png", alt="Cache Key: https://x.example/doge.png", width="570", height="433" %}
  <figcaption>
    <b>Cache Key</b>: { <code>https://x.example/doge.png</code> }
  </figcaption>
</figure>

A user visits a page (`https://a.example`) that requests an image
(`https://x.example/doge.png`). The image is requested from the network and
cached using `https://x.example/doge.png` as the key.


<figure class="float-left">
  {% Img src="image/T4FyVKpzu4WKF1kBNvXepbi08t52/sXZTOs9iABokE7VsOoXT.png", alt="Cache Key: https://x.example/doge.png", width="570", height="433" %}
  <figcaption>
    <b>Cache Key</b>: { <code>https://x.example/doge.png</code> }
  </figcaption>
</figure>

The same user visits another page (`https://b.example`), which requests the same
image (`https://x.example/doge.png`).
The browser checks its HTTP Cache to see
if it already has this resource cached, using the image URL as the key. The
browser finds a match in its Cache, so it uses the cached version of the
resource.


<figure class="float-left">
  {% Img src="image/T4FyVKpzu4WKF1kBNvXepbi08t52/c8jEGuxXemlwMbezevOc.png", alt="Cache Key: https://x.example/doge.png", width="570", height="433" %}
  <figcaption>
    <b>Cache Key</b>: { <code>https://x.example/doge.png</code> }
  </figcaption>
</figure>

It doesn't matter if the image is loaded from within an iframe. If the user
visits another website (`https://c.example`) with an iframe
(`https://d.example`) and the iframe requests the same image
(`https://x.example/doge.png`), the browser can still load the image from its
cache because the cache key is the same across all of the pages.


This mechanism has been working well from a performance perspective for a long
time. However, the time a website takes to respond to HTTP requests can reveal
that the browser has accessed the same resource in the past, which opens the
browser to security and privacy attacks, like the following:

- **Detect if a user has visited a specific site**: An adversary can detect a
  user's browsing history by checking if the cache has a resource which might be
  specific to a particular site or cohort of sites.
- **[Cross-site search
  attack](https://portswigger.net/daily-swig/new-xs-leak-techniques-reveal-fresh-ways-to-expose-user-information)**:
  An adversary can detect if an arbitrary string is in the user's search results
  by checking whether a 'no search results' image used by a particular website
  is in the browser's cache.
- **Cross-site tracking**: The cache can be used to store cookie-like
  identifiers as a cross-site tracking mechanism.

To mitigate these risks, Chrome will partition its HTTP cache starting in Chrome 86.

## How will cache partitioning affect Chrome's HTTP Cache?

With cache partitioning, cached resources will be keyed using a new "Network
Isolation Key" in addition to the resource URL. The Network Isolation Key is
composed of the top-level site and the current-frame site.

{% Aside %}
The "site" is recognized using "[scheme://eTLD+1](https://web.dev/articles/same-site-same-origin)" so if requests are from
different pages, but they have the same scheme and effective top-level domain+1
they will use the same cache partition. To learn more about this, read
[Understanding "same-site" and
"same-origin"](https://web.dev/articles/same-site-same-origin).
{% endAside %}

Look again at the previous example to see how cache partitioning works in
different contexts:

<figure class="float-left">
  {% Img src="image/T4FyVKpzu4WKF1kBNvXepbi08t52/zqkRCKG9jR3uBtcEwPgV.png", alt="Cache Key { https://a.example, https://a.example, https://x.example/doge.png}", width="570", height="433" %}
  <figcaption>
    <b>Cache Key</b>: { <code>https://a.example</code>, <code>https://a.example</code>, <code>https://x.example/doge.png</code> }
  </figcaption>
</figure>

A user visits a page (`https://a.example`) which requests an image
(`https://x.example/doge.png`). In this case, the image is requested from the
network and cached using a tuple consisting of `https://a.example` (the top-level site),
`https://a.example` (the current-frame site), and `https://x.example/doge.png` (the
resource URL) as the key. (Note that when the resource request is from the
top-level -frame, the top-level site and current-frame site in the Network
Isolation Key are the same.)


<figure class="float-left">
  {% Img src="image/T4FyVKpzu4WKF1kBNvXepbi08t52/sXZTOs9iABokE7VsOoXT.png", alt="Cache Key { https://a.example, https://a.example, https://x.example/doge.png}", width="570", height="433" %}
  <figcaption>
    <b>Cache Key</b>: { <code>https://b.example</code>, <code>https://b.example</code>, <code>https://x.example/doge.png</code> }
  </figcaption>
</figure>

The same user visits a different page (`https://b.example`) which requests the
same image (`https://x.example/doge.png`). Though the same image was loaded in
the previous example, since the key doesn't match it will not be a cache hit.

The image is requested from the network and cached using a tuple consisting of `https://b.example`,
`https://b.example`, and `https://x.example/doge.png` as the key.


<figure class="float-left">
  {% Img src="image/T4FyVKpzu4WKF1kBNvXepbi08t52/kr9TtbDQPQfR86rNSrJX.png", alt="Cache Key { https://a.example, https://a.example, https://x.example/doge.png}", width="570", height="433" %}
  <figcaption>
    <b>Cache Key</b>: { <code>https://a.example</code>, <code>https://a.example</code>, <code>https://x.example/doge.png</code> }
  </figcaption>
</figure>

Now the user comes back to `https://a.example` but this time the image
(`https://x.example/doge.png`) is embedded in an iframe. In this case, the
key is a tuple containing `https://a.example`, `https://a.example`, and `https://x.example/doge.png`
and a cache hit occurs. (Note that when the top-level site and the iframe are
the same site, the resource cached with the top-level frame can be used.

<div class="clearfix"></div>

<figure class="float-left">
  {% Img src="image/T4FyVKpzu4WKF1kBNvXepbi08t52/BIJfNKd7YfXuXR3xdafb.png", alt="Cache Key { https://a.example, https://a.example, https://x.example/doge.png}", width="570", height="433" %}
  <figcaption>
    <b>Cache Key</b>: { <code>https://a.example</code>, <code>https://c.example</code>, <code>https://x.example/doge.png</code> }
  </figcaption>
</figure>

The user is back at `https://a.example` but this time the image is hosted in an
iframe from `https://c.example`.

In this case, the image is downloaded from the network because there is no
resource in the cache that matches the key consisting of `https://a.example`,
`https://c.example`, and `https://x.example/doge.png`.


<figure class="float-left">
  {% Img src="image/T4FyVKpzu4WKF1kBNvXepbi08t52/Gg99hTwbcxc3DdUtgdnM.png", alt="Cache Key { https://a.example, https://a.example, https://x.example/doge.png}", width="570", height="433" %}
  <figcaption>
    <b>Cache Key</b>: { <code>https://a.example</code>, <code>https://c.example</code>, <code>https://x.example/doge.png</code> }
  </figcaption>
</figure>

What if the domain contains a subdomain or a port number? The user visits
`https://subdomain.a.example`, which embeds an iframe
(`https://c.example:8080`), which requests the image.

Because the key is created based on "scheme://eTLD+1", subdomains and port
numbers are ignored. Hence a cache hit occurs.


<figure class="float-left">
  {% Img src="image/T4FyVKpzu4WKF1kBNvXepbi08t52/47mGHE7I9qlFpPER12CL.png", alt="Cache Key { https://a.example, https://a.example, https://x.example/doge.png}", width="570", height="433" %}
  <figcaption>
    <b>Cache Key</b>: { <code>https://a.example</code>, <code>https://c.example</code>, <code>https://x.example/doge.png</code> }
  </figcaption>
</figure>

What if the iframe is nested multiple times? The user visits
`https://a.example`, which embeds an iframe (`https://b.example`), which embeds
yet another iframe (`https://c.example`), which finally requests the image.

Because the key is taken from the top-frame (`https://a.example`) and the
immediate frame which loads the resource (`https://c.example`), a cache hit
occurs.


## FAQs

### Is it already enabled on my Chrome? How can I check?

The feature is being rolled out through late 2020. To check whether your Chrome instance
already supports it:

1. Open `chrome://net-export/` and press **Start Logging to Disk**.
2. Specify where to save the log file on your computer.
3. Browse the web on Chrome for a minute.
4. Go back to `chrome://net-export/` and press **Stop Logging**.
5. Go to `https://netlog-viewer.appspot.com/#import`.
6. Press **Choose File** and pass the log file you saved.

You will see the output of the log file.

On the same page, find `SplitCacheByNetworkIsolationKey`. If it is followed by
`Experiment_[****]`, HTTP Cache partitioning is enabled on your Chrome. If it is
followed by `Control_[****]` or `Default_[****]`, it is not enabled.

### How can I test HTTP Cache partitioning on my Chrome?

To test HTTP Cache partitioning on your Chrome, you need to launch Chrome with a
command line flag: `--enable-features=SplitCacheByNetworkIsolationKey`. Follow
the instruction at [Run Chromium with
flags](https://www.chromium.org/developers/how-tos/run-chromium-with-flags) to
learn how to launch Chrome with a command line flag on your platform.

### As a web developer, are there any action I should take in response to this change?

This is not a breaking change, but it may impose performance considerations for
some web services.

For example, those that serve large volumes of highly cacheable resources across
many sites (such as fonts and popular scripts) may see an increase in their
traffic. Also, those who consume such services may have an increased reliance on
them.

(There's a proposal to enable shared libraries in a privacy-preserving way
called [Web Shared
Libraries](https://docs.google.com/document/d/1lQykm9HgzkPlaKXwpQ9vNc3m2Eq2hF4TY-Vup5wg4qg/edit#)
([presentation video](https://www.youtube.com/watch?v=cBY3ZcHifXw)), but it's
still under consideration.)

### What is the impact of this behavioral change?

The overall cache miss rate increases by about 3.6%, changes to the FCP (First
Contentful Paint) are modest (~0.3%), and the overall fraction of bytes loaded
from the network increases by around 4%. You can learn more about the impact on
performance in [the HTTP cache partitioning
explainer](https://github.com/shivanigithub/http-cache-partitioning#impact-on-metrics).

### Is this standardized? Do other browsers behave differently?

"HTTP cache partitions" is [standardized in the fetch
spec](https://fetch.spec.whatwg.org/#http-cache-partitions) though browsers
behave differently:

- **Chrome**: Uses top-level scheme://eTLD+1 and frame scheme://eTLD+1
- **Safari**: Uses [top-level eTLD+1](https://webkit.org/blog/8613/intelligent-tracking-prevention-2-1/)
- **Firefox**: [Planning to
  implement](https://bugzilla.mozilla.org/show_bug.cgi?id=1536058) with
  top-level scheme://eTLD+1 and considering including a second key like Chrome

### How is fetch from workers treated?

Dedicated workers use the same key as their current frame. Service workers and
shared workers are more complicated since they may be shared among multiple
top-level sites. The solution for them is currently under discussion.

## Resources

- [Storage Isolation
  Project](https://docs.google.com/document/d/1V8sFDCEYTXZmwKa_qWUfTVNAuBcPsu6FC0PhqMD6KKQ/edit#heading=h.oixrt0wpp8h5)
- [Explainer - Partition the HTTP Cache](https://github.com/shivanigithub/http-cache-partitioning)

