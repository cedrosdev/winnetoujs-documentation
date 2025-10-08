# WinnetouJS Animations Module

The animations module provides smooth, CSS-based animations for DOM elements in WinnetouJS applications. All functions support multiple selector types and use CSS transitions for optimal performance.

## Import

```javascript
import { fadeIn, fadeOut, slideUp, slideDown } from "winnetoujs/modules/select";
```

## Selector Types

All animation functions accept the following selector types:

- **String**: CSS selector (`"#myId"`, `".myClass"`, `"div"`, `"#id1, #id2"`)
- **Element**: Single DOM element
- **Element[]**: Array of DOM elements

## Functions

### fadeIn(selector, duration?)

Fades in an element by animating its opacity from 0 to 1.

**Parameters:**

- `selector` (Selector): The element(s) to animate
- `duration` (number, optional): Animation duration in milliseconds (default: 300)

**Example:**

```javascript
import { fadeIn } from "winnetoujs/modules/select";

// Fade in with default duration (300ms)
fadeIn("#myElement");

// Fade in with custom duration
fadeIn(".my-cards", 500);

// Fade in multiple elements
fadeIn("#card1, #card2, #card3", 200);

// Fade in using element reference
const element = document.getElementById("myDiv");
fadeIn(element, 400);
```

**Behavior:**

- Sets initial opacity to 0
- Ensures element is visible (`display: ""`)
- Applies CSS transition
- Animates opacity to 1
- Cleans up transition property after completion

### fadeOut(selector, duration?)

Fades out an element by animating its opacity from current value to 0, then hides it.

**Parameters:**

- `selector` (Selector): The element(s) to animate
- `duration` (number, optional): Animation duration in milliseconds (default: 300)

**Example:**

```javascript
import { fadeOut } from "winnetoujs/modules/select";

// Fade out with default duration
fadeOut("#myElement");

// Fade out with custom duration
fadeOut(".notification", 150);

// Fade out using WinnetouJS constructo ID
const card = new $myCard().create("#app");
fadeOut(card.ids.myCard, 250);
```

**Behavior:**

- Applies CSS transition for opacity
- Animates opacity to 0
- Sets `display: none` after animation completes
- Cleans up transition property

### slideUp(selector, duration?)

Slides an element up by animating its height from current value to 0, then hides it.

**Parameters:**

- `selector` (Selector): The element(s) to animate
- `duration` (number, optional): Animation duration in milliseconds (default: 300)

**Example:**

```javascript
import { slideUp } from "winnetoujs/modules/select";

// Slide up a dropdown menu
slideUp("#dropdown-menu");

// Slide up notification with custom timing
slideUp(".alert", 200);

// Slide up multiple panels
slideUp(".collapsible-panel", 400);
```

**Behavior:**

- Captures current height
- Sets initial height explicitly
- Sets `overflow: hidden`
- Animates height to 0
- Sets `display: none` after completion
- Restores original styles

### slideDown(selector, duration?)

Slides an element down by animating its height from 0 to its natural height.

**Parameters:**

- `selector` (Selector): The element(s) to animate
- `duration` (number, optional): Animation duration in milliseconds (default: 300)

**Example:**

```javascript
import { slideDown } from "winnetoujs/modules/select";

// Slide down a hidden menu
slideDown("#navigation-menu");

// Slide down content with custom duration
slideDown(".expandable-content", 600);

// Slide down multiple accordions
slideDown(".accordion-content", 350);
```

**Behavior:**

- Ensures element is visible
- Captures natural height
- Sets height to 0 initially
- Sets `overflow: hidden`
- Animates to natural height
- Restores original styles after completion

## Usage with WinnetouJS Constructos

The animations work seamlessly with WinnetouJS constructos:

```javascript
import { $myCard, $notification } from "./components.wcto";
import { fadeIn, slideDown, fadeOut } from "winnetoujs/modules/select";

// Create a constructo and animate it
const card = new $myCard({
  title: "Welcome",
  content: "Hello World!",
}).create("#app");

// Animate the constructo
fadeIn(card.ids.myCard, 400);

// Animate on user interaction
new $notification({
  message: "Success!",
  onclick: W.fx(self => {
    fadeOut(self, 200);
  }, "this"),
}).create("#notifications");
```

## Chaining Animations

You can chain animations using promises or timeouts:

```javascript
import { fadeOut, slideDown } from "winnetoujs/modules/select";

// Sequential animations
fadeOut("#oldContent", 200);
setTimeout(() => {
  slideDown("#newContent", 300);
}, 200);

// Using async/await pattern
async function replaceContent() {
  return new Promise(resolve => {
    fadeOut("#content", 200);
    setTimeout(() => {
      // Update content here
      slideDown("#content", 300);
      setTimeout(resolve, 300);
    }, 200);
  });
}
```

## Performance Notes

- All animations use CSS transitions for optimal performance
- Animations are hardware-accelerated when possible
- The module properly handles layout reflows
- Cleanup is performed after each animation to prevent memory leaks
- Multiple elements can be animated simultaneously

## Browser Compatibility

The animations module works in all modern browsers that support:

- CSS transitions
- `Element.offsetHeight`
- `setTimeout`

## Common Use Cases

### Modal Animations

```javascript
// Show modal
fadeIn("#modal-overlay", 200);
slideDown("#modal-content", 300);

// Hide modal
slideUp("#modal-content", 250);
fadeOut("#modal-overlay", 200);
```

### Notification System

```javascript
// Show notification
slideDown(".notification", 200);

// Auto-hide after delay
setTimeout(() => {
  fadeOut(".notification", 300);
}, 3000);
```

### Accordion Menus

```javascript
// Toggle accordion section
if (isOpen) {
  slideUp(".accordion-content", 250);
} else {
  slideDown(".accordion-content", 250);
}
```

### Loading States

```javascript
// Show loading spinner
fadeIn("#loading-spinner", 150);

// Hide when content loads
fadeOut("#loading-spinner", 150);
fadeIn("#main-content", 400);
```
