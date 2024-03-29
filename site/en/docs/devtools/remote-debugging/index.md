---
layout: "layouts/doc-post.njk"
title: "Remote debug Android devices"
authors:
  - kaycebasques
  - sofiayem
date: 2015-04-13
updated: 2023-08-09
description:
  "Remote debug live content on an Android device from a Windows, Mac, or Linux computer."
---

Remote debug live content on an Android device from your Windows, Mac, or Linux computer. This
tutorial teaches you how to:

- Set up your Android device for remote debugging, and discover it from your development machine.
- Inspect and debug live content on your Android device from your development machine.
- Screencast content from your Android device onto a DevTools instance on your development machine.

{% Img src="image/BrQidfK9jaQyIHwdw91aVpkPiib2/NIeeTMc8eH6hDiA3IX0Y.png", alt="Remote debugging diagram", width="800", height="317" %}

## Step 1: Discover your Android device {: #discover }

The workflow below works for most users. See [Troubleshooting: DevTools is not detecting the Android
device][1] for more help.

1.  Open the **Developer Options** screen on your Android. See [Configure on-device developer
    Options][2].
1.  Select **Enable USB Debugging**.
1.  On your development machine, open Chrome.
1.  Go to `chrome://inspect#devices`.
1.  Make sure {% Img src="image/NJdAV9UgKuN8AhoaPBquL7giZQo1/hmp8j3HiLMCcqPArD9yt.svg", alt="Checkbox.", width="22", height="22" %} **Discover USB devices** is enabled.

    {% Img src="image/NJdAV9UgKuN8AhoaPBquL7giZQo1/T15tQxPKcF8J298dVbtX.png", alt="The Discover USB Devices checkbox is enabled.", width="800", height="499" %}

1.  Connect your Android device directly to your development machine using a USB cable.

1. If you are connecting your device for the first time, the device will show up as "Offline" and pending authentication.

   {% Img src="image/NJdAV9UgKuN8AhoaPBquL7giZQo1/sGg5nvooStgbsEpHvuAb.png", alt="Offline device pending authentication.", width="800", height="565" %}
   
   In this case, accept the debugging session prompt on your device's screen.

1. If you see the model name of your Android device, DevTools has successfully established the connection to your device.

   {% Img src="image/NJdAV9UgKuN8AhoaPBquL7giZQo1/Zw9XmFNCGGzqGrQyjXUo.png", alt="A successfully connected device designated with a model name.", width="800", height="515" %}

1.  Continue to [Step 2][3].

### Troubleshooting: DevTools is not detecting the Android device {: #troubleshooting }

Make sure that your hardware is set up correctly:

- If you're using a USB hub, try connecting your Android device directly to your development machine
  instead.
- Try unplugging the USB cable between your Android device and development machine, and then
  plugging it back in. Do it while your Android and development machine screens are unlocked.
- Make sure that your USB cable works. You should be able to inspect files on your Android device
  from your development machine.

Make sure that your software is set up correctly:

- If your development machine is running Windows, try manually installing the USB drivers for your
  Android device. See [Install OEM USB Drivers][4].
- Some combinations of Windows and Android devices (especially Samsung) require extra set up. See
  [Chrome DevTools Devices does not detect device when plugged in][5].

If you don't see the **Allow USB Debugging** prompt on your Android device try:

- Disconnecting and then re-connecting the USB cable while DevTools is in focus on your development
  machine and your Android home screen is showing. In other words, sometimes the prompt doesn't show
  up when your Android or development machine screens are locked.
- Updating the display settings for your Android device and development machine so that they never
  go to sleep.
- Setting Android's USB mode to PTP. See [Galaxy S4 does not show Authorize USB debugging dialog
  box][6].
- Select **Revoke USB Debugging Authorizations** from the **Developer Options** screen on your
  Android device to reset it to a fresh state.

If you find a solution that is not mentioned in this section or in [Chrome DevTools Devices does not
detect device when plugged in][7], please add an answer to that Stack Overflow question, or [open an
issue in the developer.chrome.com repository][8]!

## Step 2: Debug content on your Android device from your development machine {: #debug }

1.  Open Chrome on your Android device.
1.  In **`chrome://inspect/#devices`** on your development machine, you see your Android device's model name, followed by
    its serial number. Below that, you can see the version of Chrome that's running on the device,
    with the version number in parentheses.

    {% Img src="image/NJdAV9UgKuN8AhoaPBquL7giZQo1/ZEBcG20eKrwsjOI7YobB.png", alt="The version of Chrome that runs on the device.", width="800", height="560" %}

1.  In the **Open tab with url** text box, enter a URL and then click **Open**. The page opens in a
    new tab on your Android device.

    {% Img src="image/NJdAV9UgKuN8AhoaPBquL7giZQo1/9YE6lG9VNFreINQguxZR.png", alt="A remote tab listed in a section.", width="800", height="608" %}

    Each remote Chrome tab gets its own section in **`chrome://inspect/#devices`**. You can [interact with that tab](#more-actions) from this section. If there are any apps using [WebView](https://developer.android.com/reference/android/webkit/WebView), you see a section for each of those apps, too. In this example, there's only one tab open.

1.  Click **Inspect** next to the URL that you just opened. A new DevTools instance opens.

{% Img src="image/NJdAV9UgKuN8AhoaPBquL7giZQo1/bxzg7qiUJ1E5gV2iWpj4.png", alt="A new DevTools instance for the remote tab.", width="800", height="719" %}

The version of Chrome running on your Android device determines the version of DevTools that opens on your development machine. So, if your Android device is running a very old version of Chrome, the DevTools instance may look very different than what you're used to.

### More actions: pause, focus, reload, or close a tab {: #more-actions }

Below the URL you can find a menu to pause, focus, reload or close a tab.

{% Img src="image/NJdAV9UgKuN8AhoaPBquL7giZQo1/OafrezvPwWg6p7hRvADQ.png", alt="The menu for pausing, reloading, focusing, or closing a tab.", width="500", height="180", class="screenshot" %}

### Inspect elements {: #inspect }

Go to the **Elements** panel of your DevTools instance, and hover over an element to highlight it in
the viewport of your Android device.

You can also tap an element on your Android device screen to select it in the **Elements** panel.
Click **Select Element** {% Img src="image/BrQidfK9jaQyIHwdw91aVpkPiib2/y9AaD4jeSmPRG4sQSylM.png", alt="Select Element", width="22", height="20" %} on your DevTools instance, and then tap the element on your Android device screen. Note that **Select Element** is disabled after the first touch, so you need to re-enable it every time you want to use this feature.

### Screencast your Android screen to your development machine {: #screencast }

Click **Toggle Screencast**
{% Img src="image/BrQidfK9jaQyIHwdw91aVpkPiib2/A5AtRECWSgsdtMZkI6g5.png", alt="Toggle Screencast", width="22", height="22" %} to view
the content of your Android device in your DevTools instance.

You can interact with the screencast in multiple ways:

- Clicks are translated into taps, firing proper touch events on the device.
- Keystrokes on your computer are sent to the device.
- To simulate a pinch gesture, hold Shift while dragging.
- To scroll, use your trackpad or mouse wheel, or fling with your mouse pointer.

Some notes on screencasts:

- Screencasts only display page content. Transparent portions of the screencast represent device
  interfaces, such as the Chrome address bar, the Android status bar, or the Android keyboard.
- Screencasts negatively affect frame rates. Disable screencasting while measuring scrolls or
  animations to get a more accurate picture of your page's performance.
- If your Android device screen locks, the content of your screencast disappears. Unlock your
  Android device screen to automatically resume the screencast.

## Debug manually through Android Debug Bridge (adb)  {: #adb }

In some rare cases, an alternative method of remote debugging may be useful. For example, you may want to connect directly to the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) (CDP) of your Chrome on Android.

To do this, you can use the [Android Debug Bridge (adb)](https://developer.android.com/tools/adb):

1. Make sure to enable [Developer options](https://developer.android.com/studio/debug/dev-options#enable) and [USB debugging](https://developer.android.com/studio/debug/dev-options#Enable-debugging) on your Android device.
1. Open up Chrome on your Android Device.
1. Connect the Android device to your development machine through:

   - A USB cable (straightforward).
   - Alternatively, [adb Wi-Fi connection](https://developer.android.com/tools/adb#wireless-android11-command-line).

1. In your development machine's command line, run [`adb devices -l`](https://developer.android.com/tools/adb#devicestatus) and check if your device is present in the list.
1. Forward the CDP socket on the device to your machine's local port, for example, `9222`. To do this, run:

   ```bash
   adb forward tcp:9222 localabstract:chrome_devtools_remote
   ```
   
1. Once successfully connected, see that:

    - `http://localhost:9222/json` lists your `page` targets.
    - `http://localhost:9222/json/version` exposes the `browser` target endpoint, as the [CDP documentation](https://chromedevtools.github.io/devtools-protocol/) indicates.
    - `chrome://inspect/#devices` is populated, even without the _Discover USB devices_ setting checked.

For troubleshooting, see:

- [`adb` documentation](https://developer.android.com/tools/adb)
- Optionally, you can read older guides:

  - [remote-debugging-legacy](https://web.archive.org/web/20140909210640/https://developer.chrome.com/devtools/docs/remote-debugging-legacy)
  - [remote-debugging](https://web.archive.org/web/20140913083903/https://developer.chrome.com/devtools/docs/remote-debugging)
  - [girish.in/…how](https://www.girish.in/how-remote-debugging-works-in-chrome/)

[1]: #troubleshooting
[2]: https://developer.android.com/studio/debug/dev-options.html
[3]: #debug
[4]: https://developer.android.com/tools/extras/oem-usb.html
[5]: https://stackoverflow.com/questions/21925992
[6]: https://android.stackexchange.com/questions/101933
[7]: https://stackoverflow.com/questions/21925992
[8]: https://github.com/GoogleChrome/developer.chrome.com/issues/new?assignees=&labels=feature+request%2CP2&template=feature_request.md&title=
