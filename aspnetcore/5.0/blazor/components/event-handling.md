---
title: ASP.NET Core Blazor event handling
author: guardrex
description: Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.
monikerRange: '>= aspnetcore-5.0 < aspnetcore-6.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2021
no-loc: [Home, Privacy, Kestrel, appsettings.json, "ASP.NET Core Identity", cookie, Cookie, Blazor, "Blazor Server", "Blazor WebAssembly", "Identity", "Let's Encrypt", Razor, SignalR]
uid: blazor/components/event-handling
---
# ASP.NET Core Blazor event handling

Specify delegate event handlers in Razor component markup with [`@on{DOM EVENT}="{DELEGATE}"`](xref:mvc/views/razor#onevent) Razor syntax:

* The `{DOM EVENT}` placeholder is a [Document Object Model (DOM) event](https://developer.mozilla.org/docs/Web/Events) (for example, `click`).
* The `{DELEGATE}` placeholder is the C# delegate event handler.

For event handling:

* Asynchronous delegate event handlers that return a <xref:System.Threading.Tasks.Task> are supported.
* Delegate event handlers automatically trigger a UI render, so there's no need to manually call [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes-statehaschanged).
* Exceptions are logged.

The following code:

* Calls the `UpdateHeading` method when the button is selected in the UI.
* Calls the `CheckChanged` method when the checkbox is changed in the UI.

`Pages/EventHandlerExample1.razor`:

[!code-razor[](~/5.0/blazor/samples/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample1.razor?highlight=10,17,27-30,32-35)]

In the following example, `UpdateHeading`:

* Is called asynchronously when the button is selected.
* Waits two seconds before updating the heading.

`Pages/EventHandlerExample2.razor`:

[!code-razor[](~/5.0/blazor/samples/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample2.razor?highlight=10,19-24)]

## Event arguments

For events that support an event argument type, specifying an event parameter in the event method definition is only necessary if the event type is used in the method. In the following example, <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> is used in the `ReportPointerLocation` method to set message text that reports the mouse coordinates when the user selects a button in the UI.

`Pages/EventHandlerExample3.razor`:

[!code-razor[](~/5.0/blazor/samples/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample3.razor?highlight=17-20)]

Supported <xref:System.EventArgs> are shown in the following table.

| Event            | Class  | [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) events and notes |
| ---------------- | ------ | --- |
| Clipboard        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` |
| Drag             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.<br><br>Implement drag and drop in Blazor apps using [JS interop](xref:blazor/js-interop/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API). |
| Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| Event            | <xref:System.EventArgs> | *General*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Clipboard*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Input*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Media*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types. |
| Focus            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Doesn't include support for `relatedTarget`. |
| Input            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` |
| Keyboard         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` |
| Mouse            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Mouse pointer    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Mouse wheel      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` |
| Progress         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Touch            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device. |

For more information, see the following resources:

* [`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `main` branch)](https://github.com/dotnet/aspnetcore/tree/main/src/Components/Web/src/Web)

  [!INCLUDE[](../includes/aspnetcore-repo-ref-source-links.md)]

* [MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.

## Lambda expressions

[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) are supported as the delegate event handler.

`Pages/EventHandlerExample4.razor`:

[!code-razor[](~/5.0/blazor/samples/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample4.razor?highlight=6)]

It's often convenient to close over additional values using C# method parameters, such as when iterating over a set of elements. The following example creates three buttons, each of which calls `UpdateHeading` and passes the following data:

* An event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) in `e`.
* The button number in `buttonNumber`.

`Pages/EventHandlerExample5.razor`:

[!code-razor[](~/5.0/blazor/samples/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample5.razor?highlight=10,19)]

> [!NOTE]
> Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example. Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas. Always capture the variable's value in a local variable and then use it. In the preceding example:
>
> * The loop variable `i` is assigned to `buttonNumber`.
> * `buttonNumber` is used in the lambda expression.

> [!NOTE]
> Use of the approach in this section can lead to poor performance with many rendered components. For more information, see [Blazor Binary message size send from server to client increases (dotnet/aspnetcore #17886)](https://github.com/dotnet/aspnetcore/issues/17886).

## EventCallback

A common scenario with nested components executes a parent component's method when a child component event occurs. An `onclick` event occurring in the child component is a common use case. To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>. A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.

The following `Child` component demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`. The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device.

`Shared/Child.razor`:

[!code-razor[](~/5.0/blazor/samples/BlazorSample_WebAssembly/Shared/event-handling/Child.razor)]

The `Parent` component sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.

`Pages/Parent.razor`:

[!code-razor[](~/5.0/blazor/samples/BlazorSample_WebAssembly/Pages/event-handling/Parent.razor)]

When the button is selected in the `ChildComponent`:

* The `Parent` component's `ShowMessage` method is called. `message` is updated and displayed in the `Parent` component.
* A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes-statehaschanged) isn't required in the callback's method (`ShowMessage`). <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `Parent` component, just as child events trigger component rerendering in event handlers that execute within the child. For more information, see <xref:blazor/components/rendering>.

<xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates. <xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`. <xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:

```csharp
await OnClickCallback.InvokeAsync(arg);
```

Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.

Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>. <xref:Microsoft.AspNetCore.Components.EventCallback%601> provides enhanced error feedback to users of the component. Similar to other UI event handlers, specifying the event parameter is optional. Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.

## Prevent default actions

Use the [`@on{DOM EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event, where the `{DOM EVENT}` placeholder is a [Document Object Model (DOM) event](https://developer.mozilla.org/docs/Web/Events).

When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box. In the following example, the default behavior is prevented by specifying the `@onkeydown:preventDefault` directive attribute. When the focus is on the `<input>` element, the counter increments with the key sequence <kbd>Shift</kbd>+<kbd>+</kbd>. The `+` character isn't assigned to the `<input>` element's value. For more information on `keydown`, see [`MDN Web Docs: Document: keydown` event](https://developer.mozilla.org/docs/Web/API/Document/keydown_event).

`Pages/EventHandlerExample6.razor`:

[!code-razor[](~/5.0/blazor/samples/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample6.razor?highlight=4)]

Specifying the `@on{DOM EVENT}:preventDefault` attribute without a value is equivalent to `@on{DOM EVENT}:preventDefault="true"`.

An expression is also a permitted value of the attribute. In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:

```razor
<input @onkeydown:preventDefault="shouldPreventDefault" />

...

@code {
    private bool shouldPreventDefault = true;
}
```

## Stop event propagation

Use the [`@on{DOM EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation, where the `{DOM EVENT}` placeholder is a [Document Object Model (DOM) event](https://developer.mozilla.org/docs/Web/Events).

In the following example, selecting the checkbox prevents click events from the second child `<div>` from propagating to the parent `<div>`. Since propagated click events normally fire the `OnSelectParentDiv` method, selecting the second child `<div>` results in the parent div message appearing unless the checkbox is selected.

`Pages/EventHandlerExample7.razor`:

[!code-razor[](~/5.0/blazor/samples/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample7.razor?highlight=4,15-16)]

## Focus an element

Call <xref:Microsoft.AspNetCore.Components.ElementReferenceExtensions.FocusAsync%2A> on an [element reference](xref:blazor/js-interop/call-javascript-from-dotnet#capture-references-to-elements) to focus an element in code. In the following example, select the button to focus the `<input>` element.

`Pages/EventHandlerExample8.razor`:

[!code-razor[](~/5.0/blazor/samples/BlazorSample_WebAssembly/Pages/event-handling/EventHandlerExample8.razor?highlight=16)]
