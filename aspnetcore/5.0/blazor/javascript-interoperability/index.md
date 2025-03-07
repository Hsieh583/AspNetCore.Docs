---
title: Blazor JavaScript interoperability (JS interop)
author: guardrex
description: Learn how to interact with JavaScript in Blazor apps.
monikerRange: '>= aspnetcore-5.0 < aspnetcore-6.0'
ms.author: riande
ms.custom: mvc, devx-track-js 
ms.date: 05/12/2021
no-loc: [Home, Privacy, Kestrel, appsettings.json, "ASP.NET Core Identity", cookie, Cookie, Blazor, "Blazor Server", "Blazor WebAssembly", "Identity", "Let's Encrypt", Razor, SignalR, JS, Promise]
uid: blazor/js-interop/index
---
# Blazor JavaScript interoperability (JS interop)

A Blazor app can invoke JavaScript (JS) functions from .NET methods and .NET methods from JS functions. These scenarios are called *JavaScript interoperability* (*JS interop*).

This overview article covers general concepts. Further JS interop guidance is provided in the following articles:

* <xref:blazor/js-interop/call-javascript-from-dotnet>
* <xref:blazor/js-interop/call-dotnet-from-javascript>

## Interaction with the Document Object Model (DOM)

Only mutate the Document Object Model (DOM) with JavaScript (JS) when the object doesn't interact with Blazor. Blazor maintains representations of the DOM and interacts directly with DOM objects. If an element rendered by Blazor is modified externally using JS directly or via JS Interop, the DOM may no longer match Blazor's internal representation, which can result in undefined behavior. Undefined behavior may merely interfere with the presentation of elements or their functions but may also introduce security risks to the app or server.

This guidance not only applies to your own JS interop code but also to any JS libraries that the app uses, including anything provided by a third-party framework, such as [Bootstrap JS](https://getbootstrap.com/) and [jQuery](https://jquery.com/).

In a few documentation examples, JS interop is used to mutate an element *purely for demonstration purposes* as part of an example. In those cases, a warning appears in the text.

For more information, see <xref:blazor/js-interop/call-javascript-from-dotnet#capture-references-to-elements>.

## Location of JavaScipt

Load JavaScript (JS) code using any of the following approaches:

* [Load a script in `<head>` markup](#load-a-script-in-head-markup) (*Not generally recommended*)
* [Load a script in `<body>` markup](#load-a-script-in-body-markup)
* [Load a script from an external JS file (`.js`)](#load-a-script-from-an-external-js-file-js)
* [Inject a script after Blazor starts](#inject-a-script-after-blazor-starts)

> [!WARNING]
> Don't place a `<script>` tag in a Razor component file (`.razor`) because the `<script>` tag can't be updated dynamically by Blazor.

> [!NOTE]
> Documentation examples usually place scripts in a `<script>` tag or load global scripts from external files. These approaches pollute the client with global functions. For production apps, we recommend placing JavaScript into separate [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) that can be imported when needed. For more information, see the [JavaScript isolation in JavaScript modules](#javascript-isolation-in-javascript-modules) section.

### Load a script in `<head>` markup

*The approach in this section isn't generally recommended.*

Place the script  (`<script>...</script>`) in the `<head>` element markup of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server):

```html
<head>
    ...

    <script>
      window.jsMethod = (methodParameter) => {
        ...
      };
    </script>
</head>
```

Loading JS from the `<head>` isn't the best approach for the following reasons:

* JS interop may fail if the script depends on Blazor. We recommend loading scripts using one of the other approaches, not via the `<head>` markup.
* The page may become interactive slower due to the time it takes to parse the JS in the script.

### Load a script in `<body>` markup

Place the script  (`<script>...</script>`) inside the closing `</body>` element markup of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server):

```html
<body>
    ...

    <script src="_framework/blazor.{webassembly|server}.js"></script>
    <script>
      window.jsMethod = (methodParameter) => {
        ...
      };
    </script>
</body>
```

The `{webassembly|server}` placeholder in the preceding markup is either `webassembly` for a Blazor WebAssembly app (`blazor.webassembly.js`) or `server` for a Blazor Server app (`blazor.server.js`).

### Load a script from an external JS file (`.js`)

Place the script  (`<script>...</script>`) with a script `src` path inside the closing `</body>` tag after the Blazor script reference.

In `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server):

```html
<body>
    ...

    <script src="_framework/blazor.{webassembly|server}.js"></script>
    <script src="{SCRIPT PATH AND FILE NAME (.js)}"></script>
</body>
```

The `{webassembly|server}` placeholder in the preceding markup is either `webassembly` for a Blazor WebAssembly app (`blazor.webassembly.js`) or `server` for a Blazor Server app (`blazor.server.js`). The `{SCRIPT PATH AND FILE NAME (.js)}` placeholder is the path and script file name under `wwwroot`.

In the following example of the preceding `<script>` tag, the `scripts.js` file is in the `wwwroot/js` folder of the app:

```html
<script src="js/scripts.js"></script>
```

When the external JS file is supplied by a [Razor class library](xref:blazor/components/class-libraries), specify the JS file using its stable static web asset path: `./_content/{LIBRARY NAME}/{SCRIPT PATH AND FILENAME (.js)}`:

* The path segment for the current directory (`./`) is required in order to create the correct static asset path to the JS file.
* The `{LIBRARY NAME}` placeholder is the library's assembly name.
* The `{SCRIPT PATH AND FILENAME (.js)}` placeholder is the path and file name under `wwwroot`.

```html
<body>
    ...

    <script src="_framework/blazor.{webassembly|server}.js"></script>
    <script src="./_content/{LIBRARY NAME}/{SCRIPT PATH AND FILENAME (.js)}"></script>
</body>
```

In the following example of the preceding `<script>` tag:

* The Razor class library has an assembly name of `ComponentLibrary`.
* The `scripts.js` file is in the class library's `wwwroot` folder.

```html
<script src="./_content/ComponentLibrary/scripts.js"></script>
```

For more information, see <xref:blazor/components/class-libraries>.

### Inject a script after Blazor starts

Load JS from an injected script in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server) when the app is initialized:

* Add `autostart="false"` to the `<script>` tag that loads the Blazor script.
* Inject a script into the `<head>` element markup that references a custom JS file after starting Blazor by calling `Blazor.start().then(...)`. Place the script (`<script>...</script>`) inside the closing `</body>` tag after the Blazor script is loaded.

The following example injects the `wwwroot/scripts.js` file after Blazor starts:

```html
<body>
    ...

    <script src="_framework/blazor.{webassembly|server}.js" 
        autostart="false"></script>
    <script>
      Blazor.start().then(function () {
        var customScript = document.createElement('script');
        customScript.setAttribute('src', 'scripts.js');
        document.head.appendChild(customScript);
      });
    </script>
</body>
```

The `{webassembly|server}` placeholder in the preceding markup is either `webassembly` for a Blazor WebAssembly app (`blazor.webassembly.js`) or `server` for a Blazor Server app (`blazor.server.js`).

For more information on Blazor startup, see <xref:blazor/fundamentals/startup>.

## JavaScript isolation in JavaScript modules

Blazor enables JavaScript (JS) isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).

JS isolation provides the following benefits:

* Imported JS no longer pollutes the global namespace.
* Consumers of a library and components aren't required to import the related JS.

For more information, see <xref:blazor/js-interop/call-javascript-from-dotnet#javascript-isolation-in-javascript-modules>.
