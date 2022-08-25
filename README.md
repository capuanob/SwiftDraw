[![Build](https://github.com/swhitty/SwiftDraw/actions/workflows/build.yml/badge.svg)](https://github.com/swhitty/SwiftDraw/actions/workflows/build.yml)
[![CodeCov](https://codecov.io/gh/swhitty/SwiftDraw/graphs/badge.svg)](https://codecov.io/gh/swhitty/SwiftDraw)
[![Platforms](https://img.shields.io/badge/platforms-iOS%20|%20Mac%20|%20Linux-lightgray.svg)](https://github.com/swhitty/SwiftDraw/blob/main/Package.swift)
[![Swift 5.4](https://img.shields.io/badge/swift-5.4-red.svg?style=flat)](https://developer.apple.com/swift)
[![License](https://img.shields.io/badge/license-zlib-lightgrey.svg)](https://opensource.org/licenses/Zlib)
[![Twitter](https://img.shields.io/badge/twitter-@simonwhitty-blue.svg)](http://twitter.com/simonwhitty)

# SwiftDraw

A Swift library for parsing and drawing SVG images. SwiftDraw can also convert SVGs into SFSymbol, PNG, PDF and Swift source code.

## Usage

Vector images can be easily loaded and rasterized to `UIImage` or `NSImage`:

```swift
let svg = SVG(named: "sample.svg", in: .main)!
imageView.image = svg.rasterize()
```

Rasterize to any size:

```swift
let svg = SVG(named: "sample.svg", in: .main)!
imageView.image = svg.rasterize(with: CGSize(width: 640, height: 480))
```

Or crop the image using insets

```swift
let svg = SVG(named: "sample.svg", in: .main)!
imageView.image = svg.rasterize(insets: .init(top: 10, left: 0, bottom: 10, bottom: 0))
```

## iOS

Create a `UIImage` directly from an SVG within a bundle, `Data` or file `URL`.

```swift
import SwiftDraw
let image = UIImage(svgNamed: "sample.svg")
```

## macOS

Create an `NSImage` directly from an SVG within a bundle, `Data` or file `URL`.

```swift
import SwiftDraw
let image = NSImage(svgNamed: "sample.svg")
```

## Command line tool

The command line tool converts SVGs to other formats: PNG, JPEG, SFSymbol and Swift source code.

```
copyright (c) 2022 Simon Whitty

usage: swiftdraw <file.svg> [--format png | pdf | jpeg | swift | sfsymbol] [--size wxh] [--scale 1x | 2x | 3x]

<file> svg file to be processed

Options:
 --format      format to output image: png | pdf | jpeg | swift | sfsymbol
 --size        size of output image: 100x200
 --scale       scale of output image: 1x | 2x | 3x
 --insets      crop inset of output image: top,left,bottom,right
 --precision   maximum number of decimal places

 --hideUnsupportedFilters   hide elements with unsupported filters.

Available keys for --format swift:
 --api                api of generated code:  appkit | uikit

Available keys for --format sfymbol:
 --insets             alignment of regular variant: top,left,bottom,right | auto
 --ultralight         svg file of ultralight variant
 --ultralightInsets   alignment of ultralight variant: top,left,bottom,right | auto
 --black              svg file of black variant
 --blackInsets        alignment of black variant: top,left,bottom,right | auto
```

```bash
$ swiftdraw simple.svg --format png --scale 3x
```

```bash
$ swiftdraw simple.svg --format pdf
```

**Installation:**

You can install the `swiftdraw` command-line tool on macOS using [Homebrew](http://brew.sh/). Assuming you already have Homebrew installed, just type:

```bash
$ brew install swiftdraw
```

To update to the latest version once installed:

```bash
$ brew upgrade swiftdraw
```

Alternatively download the latest command line tool [here](https://github.com/swhitty/SwiftDraw/releases/latest/download/SwiftDraw.dmg).

### SF Symbol

Custom SF Symbols can be generated from a single SVG:

```bash
$ swiftdraw key.svg --format sfsymbol
```

Optional variants `--ultralight` and `--black` can also be provided:

```bash
$ swiftdraw key.svg --format sfsymbol --ultralight key-ultralight.svg --black key-black.svg
```

#### Alignment

By default, SwiftDraw automatically sizes and aligns the content to the template guides.  SwiftDraw will output the alignment insets used:

```bash
$ swiftdraw simple.svg --format sfsymbol --insets auto
Alignment: --insets 30,30,30,30
```

Values can be provided in the form `--insets top,left,bottom,right` specifying an `Double` or `auto` for each inset.

```bash
$ swiftdraw simple.svg --format sfsymbol --insets 40,auto,40,auto
Alignment: --insets 40,30,40,30
```

### Source code generation

Source code can be generated using [www.whileloop.com/swiftdraw](https://www.whileloop.com/swiftdraw).

The command line tool can also convert an SVG image into Swift source code:

```xml
<?xml version="1.0" encoding="utf-8"?>
<svg version="1.1" xmlns="http://www.w3.org/2000/svg" width="160" height="160">
  <rect width="160" height="160" fill="snow" />
  <path d="m 80 20 a 50 50 0 1 0 50 50 h -50 z" fill="pink" stroke="black" stroke-width="2"/>
</svg>
```

```bash
$ swiftdraw simple.svg --format swift
```

```swift
extension UIImage {
  static func svgSimple(size: CGSize = CGSize(width: 160.0, height: 160.0)) -> UIImage {
    let f = UIGraphicsImageRendererFormat.preferred()
    f.opaque = false
    let scale = CGSize(width: size.width / 160.0, height: size.height / 160.0)
    return UIGraphicsImageRenderer(size: size, format: f).image {
      drawSimple(in: $0.cgContext, scale: scale)
    }
  }

  private static func drawSimple(in ctx: CGContext, scale: CGSize) {
    ctx.scaleBy(x: scale.width, y: scale.height)
    let rgb = CGColorSpaceCreateDeviceRGB()
    let color1 = CGColor(colorSpace: rgb, components: [1, 0.98, 0.98, 1])!
    ctx.setFillColor(color1)
    ctx.fill(CGRect(x: 0, y: 0, width: 160, height: 160))
    let color2 = CGColor(colorSpace: rgb, components: [1, 0.753, 0.796, 1])!
    ctx.setFillColor(color2)
    let path = CGMutablePath()
    path.move(to: CGPoint(x: 80, y: 30))
    path.addCurve(to: CGPoint(x: 30, y: 80),
                   control1: CGPoint(x: 52.39, y: 30),
                   control2: CGPoint(x: 30, y: 52.39))
    path.addCurve(to: CGPoint(x: 80, y: 130),
                   control1: CGPoint(x: 30, y: 107.61),
                   control2: CGPoint(x: 52.39, y: 130))
    path.addCurve(to: CGPoint(x: 130, y: 80),
                   control1: CGPoint(x: 107.61, y: 130),
                   control2: CGPoint(x: 130, y: 107.61))
    path.addLine(to: CGPoint(x: 80, y: 80))
    path.closeSubpath()
    ctx.addPath(path)
    ctx.fillPath(using: .evenOdd)
    ctx.setLineCap(.butt)
    ctx.setLineJoin(.miter)
    ctx.setLineWidth(2)
    ctx.setMiterLimit(4)
    let color3 = CGColor(colorSpace: rgb, components: [0, 0, 0, 1])!
    ctx.setStrokeColor(color3)
    ctx.addPath(path)
    ctx.strokePath()
  }
}
```
