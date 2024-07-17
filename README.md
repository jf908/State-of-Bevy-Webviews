# State of Bevy Webviews

This document hopes to be an updated outline of the state of embedding webviews in native [Bevy](https://github.com/bevyengine/bevy) applications.

## Motivation

Games can often benefit from being able to embed webviews to display online stores, profiles, information boards, or other UI elements.
There are obvious limitations to using webviews, most notably performance and size, but in some situations this tradeoff can be worth it because being able to display a website that already exists will be easier than rewriting it in Bevy UI.

An alternative solution is to compile bevy for the web which is already fairly simple to do.
It is then trivial overlay UI elements on top but you will run into the performance and security limitations of a browser.
For applications that want to use Bevy for its performance, while still utilizing web technologies for parts of the application that do not require high performance, embedding a webview in Bevy would be ideal.

## Crates

### Bevy

This is a list experimental bevy crates which are currently tackling this problem.

| Crate | Bevy version |
| --- | --- |
| [bevy_flurx_wry](https://github.com/not-elm/bevy_flurx_wry) | 0.13 |
| [bevy_wry](https://github.com/PawelBis/bevy_wry) | 0.13 |
| [bevy_wry_webview](https://github.com/hytopiagg/bevy_wry_webview) | 0.12 |
| [bevy_webview](https://github.com/blaind/bevy_webview) | 0.7 |

## Complexity

Tauri would seem like a good library to integrate with, being the most mature Rust webview library.
As it also includes a process for deploying a program with application icons, installers, and code signing, it would be especially advantageous.
Since Bevy and Tauri both assume control of the window though, and use different windowing libraries (although Tao is a fork of Winit), they are mostly incompatible, at least without a custom implementation of bevy_winit.

Wry, a dependency of Tauri, is therefore currently the most investigated library.
The repo includes an example of how to use it with winit where wry is a passed a winit handle, and this solution works as well with Bevy.
The complexity comes from dealing with issues of input and window management across platforms.

> **Wry on Windows**
> 
> Wry uses Webview2 which does not support offscreen rendering, and its window intercepts all of the Winit keyboard events. This makes it difficult to work around glitchy window interactions and choosing what inputs are processed in bevy vs in the webview.

Servo seems like the perfect library to implement a bevy webview, its cross-platform and includes support for offscreen rendering, but it still has long way to go before supporting the majority of websites. 
