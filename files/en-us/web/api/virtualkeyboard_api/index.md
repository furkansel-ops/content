---
title: VirtualKeyboard API
slug: Web/API/VirtualKeyboard_API
page-type: web-api-overview
status:
  - experimental
browser-compat: api.VirtualKeyboard
---

{{SeeCompatTable}}{{DefaultAPISidebar("VirtualKeyboard API")}}{{securecontext_header}}

The VirtualKeyboard API provides developers control over the layout of their applications when the on-screen virtual keyboard appears and disappears on devices such as tablets, mobile phones, or other devices where a hardware keyboard may not be available.

Web browsers usually handle virtual keyboards by resizing the {{Glossary("visual viewport")}} and scrolling focused input fields into view. The visual viewport is the currently visible part of the page, while the {{Glossary("layout viewport")}} is the area used to lay out its content. Resizing the visual viewport does not necessarily resize the layout viewport.

The default `interactive-widget=resizes-visual` behavior leaves the layout viewport unchanged when the keyboard appears. Other resize behaviors can be selected using the [viewport `<meta>` element](/en-US/docs/Web/HTML/Reference/Elements/meta/name/viewport#the_effect_of_interactive_ui_widgets), in browsers that support the `interactive-widget` setting.

The figure below illustrates the difference in visual viewport height and scroll position on a web page when the device has its on-screen virtual keyboard hidden, and when it is shown.

![Two devices, one without a virtual keyboard, showing that the webpage can use most of the device's vertical space, and one with a virtual keyboard, showing that the webpage can only be displayed in the remaining space](viewport-height.png)

More complex applications or specific devices such as multi-screen mobile phones may require more control of the layout when the virtual keyboard appears.

The figure below shows what happens on a dual-screen device when the virtual keyboard appears on just one of the two screens. The visual viewport becomes smaller on both screens to accommodate for the virtual keyboard, leaving wasted space on the screen where the virtual keyboard is not displayed.

![A dual-screen device, with its virtual keyboard displayed on one screen, showing that the webpage can only use the vertical space that remains after the keyboard was displayed, even if that leaves empty space on the other screen](dual-screen.png)

The VirtualKeyboard API can be used to opt out of the way the browser automatically handles the virtual keyboard, and take full control of it instead. With the VirtualKeyboard API, the keyboard still appears and disappears as necessary when form controls are focused, but neither the visual viewport nor the layout viewport is resized to accommodate it, and you can use JavaScript and CSS to adapt your layout.

## Concept

The VirtualKeyboard API consists of three parts:

- The {{domxref("VirtualKeyboard")}} interface, accessed through {{domxref('navigator.virtualKeyboard')}}, is used to opt out of the automatic virtual keyboard behavior, show or hide the virtual keyboard programmatically, as well as get the current position and size of the virtual keyboard.
- The `keyboard-inset-*` CSS environment variables provide information about the virtual keyboard's position and size.
- The [`virtualkeyboardpolicy`](/en-US/docs/Web/HTML/Reference/Global_attributes/virtualkeyboardpolicy) attribute specifies whether the virtual keyboard should appear on `contenteditable` elements.

### Opt out of the automatic virtual keyboard behavior

To opt out of the automatic virtual keyboard behavior of the browser, use `navigator.virtualKeyboard.overlaysContent = true`. The browser will no longer automatically resize either the visual viewport or the layout viewport to make space for the virtual keyboard, which will overlay your content instead.

Alternatively, in browsers that support it, setting `interactive-widget=overlays-content` in the [viewport `<meta>` element](/en-US/docs/Web/HTML/Reference/Elements/meta/name/viewport#the_effect_of_interactive_ui_widgets) requests the same overlay behavior without JavaScript. Setting `navigator.virtualKeyboard.overlaysContent` to `true` takes precedence over the `interactive-widget` setting.

### Detect the virtual keyboard geometry using JavaScript

Once you've opted out of the default browser behavior, you can get the current geometry of the virtual keyboard using `navigator.virtualKeyboard.boundingRect`, and adapt the layout as appropriate using CSS and JavaScript. In addition, you can listen to geometry changes, such as when the keyboard is shown or hidden, by using the `geometrychange` event.

This is useful for positioning important UI elements in the area that's not covered by the virtual keyboard.

The code snippet below uses the `geometrychange` event to detect when the virtual keyboard geometry changes; it then accesses the `boundingRect` property to query the size and position of the virtual keyboard:

```js
if ("virtualKeyboard" in navigator) {
  navigator.virtualKeyboard.overlaysContent = true;

  navigator.virtualKeyboard.addEventListener("geometrychange", (event) => {
    const { x, y, width, height } = event.target.boundingRect;
  });
}
```

### Detect the virtual keyboard geometry using CSS environment variables

The VirtualKeyboard API also exposes the following [CSS environment variables](/en-US/docs/Web/CSS/Reference/Values/env): `keyboard-inset-top`, `keyboard-inset-right`, `keyboard-inset-bottom`, `keyboard-inset-left`, `keyboard-inset-width`, and `keyboard-inset-height`.

The `keyboard-inset-*` CSS environment variables are useful to adapt your layout to the virtual keyboard appearance using CSS. They define a rectangle by its top, right, bottom, and left insets from the edge of the viewport. The `width` and `height` variables are also available if needed.

The code snippet below uses the `keyboard-inset-height` CSS variable to reserve space for the virtual keyboard to appear below the list of messages and input field in a chat-like application. When the virtual keyboard is hidden, the `env()` function returns `0px` and the `keyboard` grid area is hidden. The messages and input elements can occupy the full height of the viewport. When the virtual keyboard appears, the `keyboard` grid area gets the height of the virtual keyboard.

```html
<ul id="messages"></ul>
<input type="text" />
```

```css
body {
  display: grid;
  margin: 0;
  height: 100vh;
  grid-template:
    "messages" 1fr
    "input" auto
    "keyboard" env(keyboard-inset-height, 0px);
}
```

```js
if ("virtualKeyboard" in navigator) {
  navigator.virtualKeyboard.overlaysContent = true;
}
```

### Control the virtual keyboard on `contenteditable` elements

By default, elements using the [`contenteditable`](/en-US/docs/Web/HTML/Reference/Global_attributes/contenteditable) attribute also trigger the virtual keyboard when tapped or clicked. In certain situations, it may be desirable to prevent this behavior and instead show the virtual keyboard after a different event.

Set the [`virtualkeyboardpolicy`](/en-US/docs/Web/HTML/Reference/Global_attributes/virtualkeyboardpolicy) attribute to `manual` to prevent the default handling of the virtual keyboard in the browser, and instead handle it yourself by using the {{domxref("VirtualKeyboard")}} interface's `show()` and `hide()` methods.

The code snippet below shows how to use the `virtualkeyboardpolicy` attribute and the `navigator.virtualKeyboard.show()` method to show the virtual keyboard on double-click instead:

```html
<div contenteditable virtualkeyboardpolicy="manual" id="editor"></div>
```

```js
if ("virtualKeyboard" in navigator) {
  navigator.virtualKeyboard.overlaysContent = true;

  const editor = document.getElementById("editor");
  editor.addEventListener("dblclick", () => {
    navigator.virtualKeyboard.show();
  });
}
```

## Interfaces

- {{domxref('VirtualKeyboard')}} {{experimental_inline}}
  - : Provides functions that retrieve keyboard layout maps and toggle capturing of key presses from the physical keyboard.

## Extensions to other interfaces

- {{domxref("Navigator.virtualKeyboard")}} {{ReadOnlyInline}} {{experimental_inline}}
  - : Returns a reference to the {{DOMxRef("VirtualKeyboard")}} API, to take control of the on-screen virtual keyboard.
- {{domxref("HTMLElement.virtualkeyboardpolicy")}} {{experimental_inline}}
  - : A string indicating whether to use the browser's default policy for showing the virtual keyboard when the element is focused, or to handle showing the virtual keyboard manually.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Full control with the VirtualKeyboard API](https://developer.chrome.com/docs/web-platform/virtual-keyboard/)
