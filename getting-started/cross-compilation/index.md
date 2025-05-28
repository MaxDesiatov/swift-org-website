---
layout: page
date: 2025-01-30 12:00:00
title: Cross-Compiling with Swift SDKs
author: [maxdesiatov]
---

Building code for a different platform from the platform a compiler is running on is called *cross-compiling*.
For example, iOS developers use Xcode on macOS to cross-compile projects written in Swift. In that particular case,
macOS running the compiler is *a host platform*, and iOS running compiled code is *a target platform*.

Starting with Swift 6.0, SwiftPM gained support for targeting Linux distributions that have a corresponding
[Swift SDK](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0387-cross-compilation-destinations.md). Additionally,
support for specialized cross-compilation use cases and customized toolchain options is available in Swift 6.1 with the introduction
of the new `--toolset` option to `swift build`, `swift test`, and `swift run` commands.

The Swift project maintains the official [Static Linux SDK](https://www.swift.org/documentation/articles/static-linux-getting-started.html),
and creation of custom Swift SDKs for Linux is now possible with [the Swift SDK Generator project](https://github.com/swiftlang/swift-sdk-generator).

This article provides an overview of common cross-compilation concepts and workflows encountered when using SwiftPM with Swift SDKs.

## Preparing the Host Platform

Follow these steps to prepare your environment before SwiftPM packages can be cross-compiled.

## macOS

On macOS, make sure you have [Xcode](https://apps.apple.com/gb/app/xcode/id497799835?mt=12) installed and selected with this command
executed in a terminal app:

```
xcode-select -p
```

It should print a path similar to this:

```
/Applications/Xcode.app/Contents/Developer
```

Make sure the path _does not contain a `CommandLineTools` component_. Otherwise, point it to the path where Xcode is installed:

```
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
```

Then download [the swift.org `.pkg` toolchain](https://www.swift.org/install/macos/) for the latest release and run the installer.
Choosing "Install for me only" option at the "Destination Select" step is recommended, which makes it easier to remove installed
toolchains in the future if needed.

![Screenshot of the "Destination Select" step of the toolchain installer](/assets/images/cross-compilation/destination-select.png)

```
export TOOLCHAINS=
```

## Linux

On Linux, follow [the installation page](https://www.swift.org/install/linux/#platforms) selecting the distribution that will run
the Swift toolchain. You can use either a Docker image or [install from a tarball](https://www.swift.org/install/linux/tarball/).

## Installing a Swift SDK

Since [the ABI](https://en.wikipedia.org/wiki/Application_binary_interface) of binaries on non-Darwin platforms may differ with
different compiler versions, it's important for toolchain and Swift SDK versions to match. For example, the Static Linux SDK for
Swift 6.0.3 will have `swift-6.0.3` specified in its name, use this Swift SDK version if you've installed Swift 6.0.3 toolchain
previously and intend to use it for cross-compilation.

If a Swift SDK you're going to install is hosted remotely, take note of a checksum string provided with it. Then provide it using
the `--checksum` option to the `swift sdk install` command, together with the Swift SDK archive URL. Here's an example for the
Static Linux SDK:

```
swift sdk install --checksum 67f765e0030e661a7450f7e4877cfe008db4f57f177d5a08a6e26fd661cdd0bd \
    https://download.swift.org/swift-6.0.3-release/static-sdk/swift-6.0.3-RELEASE/swift-6.0.3-RELEASE_static-linux-0.0.1.artifactbundle.tar.gz
```

If the command completed successfully, you'll see a corresponding message in the terminal. You can also check which Swift SDKs
were installed with the following command:

```
swift sdk list
```

which will output a list of newline separated Swift SDK ids.

## Building with Swift SDKs

A lot of compilers identify target platform type and ABI with a string called "triple". Despite its name, it can frequently contain
more than three components, for example Static Linux SDK uses `aarch64-swift-linux-musl` as a target platform triple.

With the SwiftPM's `swift build` command

## Using Cross-Compiled Products

https://github.com/apple/swift-container-plugin

## SwiftPM Toolsets

