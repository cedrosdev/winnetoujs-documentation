# WinnetouJS Select Module

The WinnetouJS Select Module is a comprehensive DOM manipulation library that provides a clean, consistent API for working with DOM elements in WinnetouJS applications. It serves as a powerful wrapper around native JavaScript DOM APIs, offering improved readability, performance, and ease of use.

## Overview

The select module is designed as a replacement for heavy DOM libraries, providing essential functionality with a lightweight footprint. It integrates seamlessly with WinnetouJS constructos, mutables, and the reactive system.

## Installation and Import

```javascript
// Import all functions
import {
  // Selectors
  getElements,
  exists,
  findElements,

  // DOM Manipulation
  removeElements,
  setHtml,
  getHtml,
  getText,
  setText,
  appendHtml,
  prependHtml,
  setCss,
  toggleClass,
  addClass,
  removeClass,
  hasClass,
  cloneElements,
  insertBefore,
  insertAfter,
  replaceElements,

  // Attributes
  getValue,
  setValue,
  setAttr,
  getAttr,
  isChecked,
  getFile,
  getFiles,
  disableElement,
  enableElement,
  setData,
  getData,
  removeData,
  clearForm,
  serializeForm,
  resetForm,
  insertTextAtCursor,

  // Events
  onClick,
  onSubmit,
  onChange,
  onInput,
  onFocus,
  onBlur,
  onKeyPress,
  onKeyDown,
  onKeyUp,
  onMouseOver,
  onMouseOut,
  onMouseEnter,
  onMouseLeave,
  focusElement,
  blurElement,
  selectText,

  // Positioning
  hideElements,
  showElements,
  getWidth,
  getHeight,
  getLeft,
  getTop,
  getGlobalPosition,
  getScrollTop,
  isVisible,
  isHidden,
  contains,
  scrollToElement,
  setScrollTop,
  setScrollLeft,
  getScrollHeight,
  getScrollWidth,

  // Navigation
  getParent,
  getChildren,
  getSiblings,
  getNext,
  getPrevious,

  // Animations
  fadeIn,
  fadeOut,
  slideUp,
  slideDown,
} from "winnetoujs/modules/select";
```

## Module Organization

The select module is organized into six main categories:

### 1. **[Selectors](./selectors.md)** - Element Selection and Querying

- `getElements()` - Core element selection
- `exists()` - Element existence checking
- `findElements()` - Scoped element searching

### 2. **[DOM Manipulation](./dom-manipulation.md)** - Content and Structure Management

- HTML content: `setHtml()`, `getHtml()`, `getText()`, `setText()`, `appendHtml()`, `prependHtml()`
- CSS styling: `setCss()`, `addClass()`, `removeClass()`, `toggleClass()`, `hasClass()`
- Element operations: `removeElements()`, `cloneElements()`, `insertBefore()`, `insertAfter()`, `replaceElements()`

### 3. **[Attributes](./attributes.md)** - Attributes and Form Data

- Values: `getValue()`, `setValue()`, `isChecked()`
- Attributes: `setAttr()`, `getAttr()`, `setData()`, `getData()`, `removeData()`
- Form operations: `clearForm()`, `serializeForm()`, `resetForm()`
- Element states: `disableElement()`, `enableElement()`
- File handling: `getFile()`, `getFiles()`

### 4. **[Events](./events.md)** - Event Handling and User Interactions

- Mouse events: `onClick()`, `onMouseOver()`, `onMouseEnter()`, etc.
- Form events: `onSubmit()`, `onChange()`, `onInput()`, `onFocus()`, `onBlur()`
- Keyboard events: `onKeyDown()`, `onKeyUp()`, `onKeyPress()`
- Focus management: `focusElement()`, `blurElement()`, `selectText()`

### 5. **[Positioning](./positioning.md)** - Layout and Viewport Management

- Visibility: `hideElements()`, `showElements()`, `isVisible()`, `isHidden()`
- Dimensions: `getWidth()`, `getHeight()`, `getGlobalPosition()`
- Positioning: `getLeft()`, `getTop()`, `contains()`
- Scrolling: `scrollToElement()`, `getScrollTop()`, `setScrollTop()`, `getScrollHeight()`

### 6. **[Navigation](./navigation.md)** - DOM Tree Traversal

- Hierarchy: `getParent()`, `getChildren()`
- Siblings: `getSiblings()`, `getNext()`, `getPrevious()`

### 7. **[Animations](./animations.md)** - CSS-Based Animations

- Fade effects: `fadeIn()`, `fadeOut()`
- Slide effects: `slideUp()`, `slideDown()`

## Key Features

### Flexible Selector System

All functions accept multiple selector types:

```javascript
// String selectors
onClick("#button", handler); // ID
onClick(".buttons", handler); // Class
onClick("button", handler); // Tag
onClick("#id1, #id2", handler); // Multiple
onClick("#container .button", handler); // Descendant

// Element objects
const element = document.getElementById("myButton");
onClick(element, handler);

// Element arrays
const buttons = document.querySelectorAll("button");
onClick(buttons, handler);

// WinnetouJS constructo IDs
const modal = new $modal().create("#app");
onClick(modal.ids.closeButton, handler);
```

### Chainable Operations

Functions are designed to work together seamlessly:

```javascript
// Find elements, modify them, then animate
const cards = getElements(".product-card");
addClass(cards, "highlighted");
setText(cards, "Updated!");
fadeIn(cards, 300);
```

### Integration with WinnetouJS

Perfect integration with constructos, mutables, and W.fx():

```javascript
import { $userCard } from "./components.wcto";
import { setValue, onClick, fadeIn } from "winnetoujs/modules/select";
import { W } from "winnetoujs";

// Create constructo with select module integration
const card = new $userCard({
  name: "John Doe",
  onClick: W.fx(self => {
    addClass(self, "active");
    const siblings = getSiblings(self);
    removeClass(siblings, "active");
  }, "this"),
}).create("#app");

// Use select module with constructo
setValue(card.ids.nameInput, "Jane Doe");
fadeIn(card.ids.userCard, 400);
```

## Common Patterns

### Form Handling

```javascript
import {
  serializeForm,
  clearForm,
  setValue,
  getValue,
  onSubmit,
  onChange,
  focusElement,
} from "winnetoujs/modules/select";

// Complete form management
onSubmit("#userForm", async event => {
  event.preventDefault();

  // Get form data
  const formData = serializeForm("#userForm");

  // Validate
  if (!formData.email || !formData.password) {
    focusElement("#email");
    return;
  }

  // Submit
  try {
    await submitUser(formData);
    clearForm("#userForm");
    showSuccess("User created successfully!");
  } catch (error) {
    showError("Failed to create user");
  }
});

// Real-time validation
onChange("#email", event => {
  const email = getValue("#email");
  if (isValidEmail(email)) {
    removeClass("#email", "error");
    addClass("#email", "valid");
  }
});
```

### Modal Management

```javascript
import {
  showElements,
  hideElements,
  fadeIn,
  fadeOut,
  onClick,
  onKeyDown,
  focusElement,
} from "winnetoujs/modules/select";

// Complete modal system
const openModal = modalSelector => {
  showElements(modalSelector);
  fadeIn(modalSelector, 200);
  focusElement(findElements(modalSelector, "[data-focus-first]")[0]);
};

const closeModal = modalSelector => {
  fadeOut(modalSelector, 200);
  setTimeout(() => hideElements(modalSelector), 200);
};

// Event handlers
onClick("[data-modal-open]", event => {
  const modalId = event.target.dataset.modalTarget;
  openModal(modalId);
});

onClick("[data-modal-close]", event => {
  const modal = getParent(event.target);
  closeModal(modal);
});

// Escape key handling
onKeyDown(document, event => {
  if (event.key === "Escape") {
    const visibleModal = getElements(".modal").find(isVisible);
    if (visibleModal) closeModal(visibleModal);
  }
});
```

### Dynamic Content Loading

```javascript
import {
  setHtml,
  appendHtml,
  addClass,
  removeClass,
  scrollToElement,
  fadeIn,
} from "winnetoujs/modules/select";

// Content loading with loading states
const loadContent = async (containerSelector, url) => {
  // Show loading state
  addClass(containerSelector, "loading");
  setHtml(containerSelector, "<div class='loader'>Loading...</div>");

  try {
    const content = await fetchContent(url);

    // Update content
    setHtml(containerSelector, content);
    removeClass(containerSelector, "loading");

    // Animate in
    fadeIn(findElements(containerSelector, ".content-item"), 300);

    // Scroll to new content
    scrollToElement(containerSelector, { behavior: "smooth" });
  } catch (error) {
    setHtml(
      containerSelector,
      "<div class='error'>Failed to load content</div>"
    );
    removeClass(containerSelector, "loading");
  }
};
```

### Component State Management

```javascript
import {
  addClass,
  removeClass,
  hasClass,
  toggleClass,
  getChildren,
  getSiblings,
} from "winnetoujs/modules/select";

// Tab system
const activateTab = tabSelector => {
  const tab = getElements(tabSelector)[0];
  const tabContainer = getParent(tab);
  const allTabs = getChildren(tabContainer);
  const tabIndex = allTabs.indexOf(tab);

  // Update tab states
  removeClass(allTabs, "active");
  addClass(tab, "active");

  // Update panel states
  const panels = getElements("[data-tab-panel]");
  removeClass(panels, "active");
  addClass(panels[tabIndex], "active");
};

// Accordion system
const toggleAccordion = triggerSelector => {
  const trigger = getElements(triggerSelector)[0];
  const item = getParent(trigger);
  const content = findElements(item, ".accordion-content")[0];

  if (hasClass(item, "open")) {
    removeClass(item, "open");
    slideUp(content, 300);
  } else {
    addClass(item, "open");
    slideDown(content, 300);
  }
};
```

## Performance Best Practices

1. **Cache Element References**: Store frequently accessed elements

```javascript
// Good: Cache elements
const navbar = getElements("#navbar")[0];
const buttons = findElements(navbar, "button");

// Better than: Repeated selections
// onClick("#navbar button", handler); // Selects every time
```

2. **Batch DOM Operations**: Group reads and writes

```javascript
// Good: Batch operations
const elements = getElements(".card");
const heights = elements.map(getHeight); // All reads first
elements.forEach((el, i) => setCss(el, "height", heights[i])); // Then writes
```

3. **Use Scoped Searches**: Limit search scope with findElements

```javascript
// Good: Scoped search
const formButtons = findElements("#userForm", "button");

// Less efficient: Global search
const allButtons = getElements("#userForm button");
```

4. **Event Delegation**: Use for dynamic content

```javascript
// Good: Event delegation
onClick(document, event => {
  if (event.target.matches(".dynamic-button")) {
    handleDynamicClick(event);
  }
});
```

## Browser Compatibility

The select module works in all modern browsers supporting:

- ES6+ features (2015+)
- Standard DOM APIs
- CSS3 transitions (for animations)
- Modern event handling

## Migration from jQuery

The select module provides similar functionality to jQuery with performance benefits:

| jQuery                        | WinnetouJS Select             |
| ----------------------------- | ----------------------------- |
| `$(selector)`                 | `getElements(selector)`       |
| `$(selector).html(content)`   | `setHtml(selector, content)`  |
| `$(selector).text(content)`   | `setText(selector, content)`  |
| `$(selector).css(prop, val)`  | `setCss(selector, prop, val)` |
| `$(selector).addClass(class)` | `addClass(selector, class)`   |
| `$(selector).on('click', fn)` | `onClick(selector, fn)`       |
| `$(selector).fadeIn()`        | `fadeIn(selector)`            |
| `$(selector).val()`           | `getValue(selector)`          |

## Getting Started

1. **Start Simple**: Begin with basic DOM manipulation
2. **Learn Selectors**: Master the flexible selector system
3. **Integrate with WinnetouJS**: Use with constructos and W.fx()
4. **Explore Animations**: Add smooth transitions
5. **Build Components**: Create reusable interactive patterns

For detailed documentation on each module, see the individual module documentation files linked above.
