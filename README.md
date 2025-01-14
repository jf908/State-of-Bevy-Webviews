# State of Bevy Webviews

This document hopes to be an updated outline of the state of embedding webviews in native [bevy](https://github.com/bevyengine/bevy) applications.

> [!NOTE]
> If you would like to contribute any information here, feel free to leave an issue or PR.

## Motivation

Games can often benefit from being able to embed webviews to display online stores, profiles, information boards, or other UI elements.
There are obvious limitations to using embedded webviews, most notably performance and binary size, but in some situations this tradeoff can be worth it because being able to display a website that already exists will be easier than rewriting it for bevy.

An alternative solution to embedding a webview in bevy is to compile bevy to wasm which is already fairly well documented.
It is then trivial to overlay UI elements on top but you will run into the compatibility, performance and security limitations of browser APIs.
For applications that want to use bevy for its performance, while still utilizing web technologies for parts of the application that do not require high performance, it can be useful to embed webviews inside bevy apps.

## Crates

### Bevy

This is a list experimental bevy crates which are currently tackling this problem.

| Crate | Bevy version |
| --- | --- |
| [bevy_webview_wry](https://github.com/not-elm/bevy_webview_projects) | 0.15 |
| [bevy_wry](https://github.com/PawelBis/bevy_wry) | 0.14 |
| [bevy_webview](https://github.com/blaind/bevy_webview) | 0.7 |

## Approaches

[Wry](https://github.com/tauri-apps/wry), a dependency of Tauri, is used in most of the bevy crates currently available.
I have created [an example](https://github.com/jf908/bevy-webview-test) that showcases a minimal example of integrating it into a Bevy app.
From testing Wry-based solutions, its clear that since wry is designed to take over the window, its common to run into bugs from edge case configurations of OS, GPU, transparency, and window mode.

**Wry on Windows**

* Wry uses Webview2 which does not support offscreen rendering (you cannot render the page to an intermediary texture) which makes it difficult to work around glitchy window interactions. See:
  * https://github.com/MicrosoftEdge/WebView2Feedback/issues/20
  * https://github.com/MicrosoftEdge/WebView2Feedback/issues/547
* When Webview2 is in focus, it intercepts all of winit's keyboard events making it difficult to choose what inputs are processed in bevy vs in the webview. See:
  * https://github.com/tauri-apps/wry/issues/902
  * https://github.com/MicrosoftEdge/WebView2Feedback/issues/468#issuecomment-1016993455

### Other approaches

[Tauri](https://github.com/tauri-apps/tauri) would seem like a good library to integrate with, being the most mature Rust webview library.
It includes processes for deploying applications with desktop icons, installers, and code signing, which would all be useful for developing bevy apps.
Since Bevy and Tauri both assume control of the window though, and use different windowing libraries (although Tao is a fork of Winit), they are mostly incompatible, at least without a custom implementation of bevy_winit.

[Servo](https://github.com/servo/servo) seems like the perfect library to implement a bevy webview, its cross-platform and includes support for offscreen rendering, but it still has long way to go before supporting the majority of websites. 

[cef-rs](https://github.com/wusyong/cef-rs)/[webview-rs](https://github.com/mycrl/webview-rs) are libraries I have not seen being used yet, but could be promising as there are several games that use CEF and it supports offscreen rendering. CEF also powers Steam's browser overlay.

There are many [Electron/CEF alternatives](https://github.com/sudhakar3697/awesome-electron-alternatives) out in the wild (although not many in pure rust) that could prove to be useful or inspirational.
