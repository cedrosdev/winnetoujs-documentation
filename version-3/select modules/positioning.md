# WinnetouJS Positioning Module

The positioning module provides comprehensive functions for managing element visibility, dimensions, positioning, and scrolling behavior in WinnetouJS applications. It includes utilities for layout management and viewport interactions.

## Import

```javascript
import {
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
} from "winnetoujs/modules/select";
```

## Selector Types

All functions accept the following selector types:

- **String**: CSS selector (`"#myId"`, `".myClass"`, `"div"`, `"#id1, #id2"`)
- **Element**: Single DOM element
- **Element[]**: Array of DOM elements

## Visibility Management

### hideElements(selector)

Hides elements by adding the `winnetou_display_none` CSS class.

**Parameters:**

- `selector` (Selector): The element(s) to hide

**Example:**

```javascript
import { hideElements } from "winnetoujs/modules/select";

// Hide modal
hideElements("#modal");

// Hide multiple elements
hideElements(".temporary-notice");

// Hide elements conditionally
if (!user.isAuthenticated) {
  hideElements(".authenticated-only");
}

// Use with constructos
const tooltip = new $tooltip().create("#app");
hideElements(tooltip.ids.tooltip); // Initially hidden
```

### showElements(selector)

Shows elements by removing the `winnetou_display_none` class and ensuring visibility.

**Parameters:**

- `selector` (Selector): The element(s) to show

**Example:**

```javascript
import { showElements } from "winnetoujs/modules/select";

// Show modal
showElements("#modal");

// Show elements based on conditions
if (user.hasPermission) {
  showElements(".admin-controls");
}

// Show/hide toggle
const toggleVisibility = selector => {
  if (isVisible(selector)) {
    hideElements(selector);
  } else {
    showElements(selector);
  }
};
```

**Behavior:**

- Removes `winnetou_display_none` class
- Sets `display: initial` if element is still hidden
- Preserves other display properties

### isVisible(selector) / isHidden(selector)

Checks element visibility state.

**Parameters:**

- `selector` (Selector): The element to check

**Returns:**

- `boolean`: True if visible/hidden, false otherwise

**Example:**

```javascript
import { isVisible, isHidden } from "winnetoujs/modules/select";

// Conditional logic based on visibility
if (isVisible("#sidebar")) {
  addClass("#content", "sidebar-open");
} else {
  removeClass("#content", "sidebar-open");
}

// Toggle states
onClick("#toggleButton", () => {
  if (isHidden("#menu")) {
    showElements("#menu");
    setText("#toggleButton", "Hide Menu");
  } else {
    hideElements("#menu");
    setText("#toggleButton", "Show Menu");
  }
});

// Visibility-based animations
if (isVisible("#element")) {
  fadeOut("#element", 300);
} else {
  fadeIn("#element", 300);
}
```

**Visibility Detection:**

- Checks `display: none`
- Checks `visibility: hidden`
- Checks `opacity: 0`

## Dimensions

### getWidth(selector) / getHeight(selector)

Gets the rendered dimensions of elements.

**Parameters:**

- `selector` (Selector): The element to measure

**Returns:**

- `number`: The width/height in pixels

**Example:**

```javascript
import { getWidth, getHeight } from "winnetoujs/modules/select";

// Get element dimensions
const width = getWidth("#container");
const height = getHeight("#container");
console.log(`Container size: ${width}x${height}`);

// Responsive layout adjustments
const cardWidth = getWidth(".product-card");
if (cardWidth < 200) {
  addClass(".product-card", "compact-layout");
}

// Dynamic sizing
const availableHeight = getHeight("#viewport");
setCss("#content", "max-height", availableHeight - 100);

// Measure before positioning
const tooltipWidth = getWidth("#tooltip");
const triggerLeft = getLeft("#trigger");
const idealLeft = triggerLeft - tooltipWidth / 2;
setCss("#tooltip", "left", idealLeft);
```

## Positioning

### getLeft(selector) / getTop(selector)

Gets the offset position of elements relative to their offset parent.

**Parameters:**

- `selector` (Selector): The element to get position of

**Returns:**

- `number`: The left/top offset in pixels

**Example:**

```javascript
import { getLeft, getTop } from "winnetoujs/modules/select";

// Get element position
const left = getLeft("#draggableElement");
const top = getTop("#draggableElement");
console.log(`Position: ${left}, ${top}`);

// Relative positioning
const buttonLeft = getLeft("#triggerButton");
const buttonTop = getTop("#triggerButton");
setCss("#dropdown", "left", buttonLeft);
setCss("#dropdown", "top", buttonTop + 40); // Below button

// Collision detection
const elementLeft = getLeft("#movingElement");
const containerWidth = getWidth("#container");
if (elementLeft + getWidth("#movingElement") > containerWidth) {
  // Element exceeds container
  addClass("#movingElement", "overflowing");
}
```

### getGlobalPosition(selector)

Gets the element's position relative to the viewport.

**Parameters:**

- `selector` (Selector): The element to get global position of

**Returns:**

- `DOMRect`: Object with x, y, width, height, top, right, bottom, left properties

**Example:**

```javascript
import { getGlobalPosition } from "winnetoujs/modules/select";

// Get viewport position
const rect = getGlobalPosition("#element");
console.log(`Global position: ${rect.x}, ${rect.y}`);
console.log(`Size: ${rect.width}x${rect.height}`);

// Viewport collision detection
const checkViewportCollision = selector => {
  const rect = getGlobalPosition(selector);
  const viewport = {
    width: window.innerWidth,
    height: window.innerHeight,
  };

  return {
    left: rect.left < 0,
    right: rect.right > viewport.width,
    top: rect.top < 0,
    bottom: rect.bottom > viewport.height,
  };
};

// Position tooltips in viewport
const positionTooltip = (trigger, tooltip) => {
  const triggerRect = getGlobalPosition(trigger);
  const tooltipWidth = getWidth(tooltip);
  const tooltipHeight = getHeight(tooltip);

  let left = triggerRect.left + triggerRect.width / 2 - tooltipWidth / 2;
  let top = triggerRect.top - tooltipHeight - 10;

  // Keep in viewport
  if (left < 0) left = 10;
  if (left + tooltipWidth > window.innerWidth) {
    left = window.innerWidth - tooltipWidth - 10;
  }
  if (top < 0) {
    top = triggerRect.bottom + 10;
  }

  setCss(tooltip, "left", left);
  setCss(tooltip, "top", top);
};
```

## Containment

### contains(parentSelector, childSelector)

Checks if one element contains another.

**Parameters:**

- `parentSelector` (Selector): The potential parent element
- `childSelector` (Selector): The potential child element

**Returns:**

- `boolean`: True if parent contains child, false otherwise

**Example:**

```javascript
import { contains } from "winnetoujs/modules/select";

// Check element containment
if (contains("#sidebar", "#activeMenu")) {
  addClass("#sidebar", "has-active-menu");
}

// Event delegation validation
onClick(document, event => {
  if (contains("#modal", event.target)) {
    // Click inside modal
    return;
  } else {
    // Click outside modal - close it
    hideElements("#modal");
  }
});

// Component boundary checking
const isWithinComponent = (element, componentSelector) => {
  return contains(componentSelector, element);
};
```

## Scrolling

### getScrollTop(selector)

Gets the scroll position of an element.

**Parameters:**

- `selector` (Selector): The scrollable element

**Returns:**

- `number`: The scroll position in pixels

**Example:**

```javascript
import { getScrollTop } from "winnetoujs/modules/select";

// Monitor scroll position
const scrollPosition = getScrollTop("#scrollableContainer");
console.log(`Scrolled: ${scrollPosition}px`);

// Scroll-based UI updates
onScroll("#container", () => {
  const scrollTop = getScrollTop("#container");
  const maxScroll = getScrollHeight("#container") - getHeight("#container");
  const scrollPercent = (scrollTop / maxScroll) * 100;

  setCss("#scrollProgress", "width", `${scrollPercent}%`);
});

// Infinite scroll detection
const checkInfiniteScroll = () => {
  const scrollTop = getScrollTop(window);
  const windowHeight = window.innerHeight;
  const documentHeight = document.documentElement.scrollHeight;

  if (scrollTop + windowHeight >= documentHeight - 100) {
    loadMoreContent();
  }
};
```

### setScrollTop(selector, position) / setScrollLeft(selector, position)

Sets the scroll position of elements.

**Parameters:**

- `selector` (Selector): The element(s) to scroll
- `position` (number): The scroll position in pixels

**Example:**

```javascript
import { setScrollTop, setScrollLeft } from "winnetoujs/modules/select";

// Scroll to top
setScrollTop("#container", 0);

// Scroll to specific position
setScrollTop("#chatContainer", getScrollHeight("#chatContainer"));

// Horizontal scrolling
setScrollLeft("#imageGallery", 300);

// Smooth scroll to position
const smoothScrollTo = (selector, targetPosition) => {
  const currentPosition = getScrollTop(selector);
  const distance = targetPosition - currentPosition;
  const duration = 500;
  const start = Date.now();

  const animateScroll = () => {
    const elapsed = Date.now() - start;
    const progress = Math.min(elapsed / duration, 1);
    const easeProgress = 0.5 - Math.cos(progress * Math.PI) / 2;

    setScrollTop(selector, currentPosition + distance * easeProgress);

    if (progress < 1) {
      requestAnimationFrame(animateScroll);
    }
  };

  animateScroll();
};
```

### scrollToElement(selector, options?)

Scrolls to make an element visible in the viewport.

**Parameters:**

- `selector` (Selector): The element to scroll to
- `options` (ScrollOptions, optional): Scroll behavior options

**Example:**

```javascript
import { scrollToElement } from "winnetoujs/modules/select";

// Smooth scroll to element
scrollToElement("#targetSection", {
  behavior: "smooth",
  block: "start",
  inline: "nearest",
});

// Jump to element immediately
scrollToElement("#errorField", {
  behavior: "auto",
  block: "center",
});

// Navigation with scroll
onClick(".nav-link", event => {
  const targetId = event.target.getAttribute("href");
  scrollToElement(targetId, {
    behavior: "smooth",
    block: "start",
  });
});

// Form validation with scroll
const validateAndScrollToError = () => {
  const firstError = document.querySelector(".form-field.error");
  if (firstError) {
    scrollToElement(firstError, {
      behavior: "smooth",
      block: "center",
    });
    focusElement(findElements(firstError, "input")[0]);
  }
};
```

### getScrollHeight(selector) / getScrollWidth(selector)

Gets the total scrollable dimensions of elements.

**Parameters:**

- `selector` (Selector): The element to measure

**Returns:**

- `number`: The total scroll dimension in pixels

**Example:**

```javascript
import { getScrollHeight, getScrollWidth } from "winnetoujs/modules/select";

// Calculate scroll progress
const calculateScrollProgress = container => {
  const scrollTop = getScrollTop(container);
  const scrollHeight = getScrollHeight(container);
  const clientHeight = getHeight(container);

  return (scrollTop / (scrollHeight - clientHeight)) * 100;
};

// Detect if content is scrollable
const isScrollable = selector => {
  const scrollHeight = getScrollHeight(selector);
  const clientHeight = getHeight(selector);
  return scrollHeight > clientHeight;
};

// Auto-resize based on content
const autoResize = selector => {
  const contentHeight = getScrollHeight(selector);
  const maxHeight = 400;

  if (contentHeight <= maxHeight) {
    setCss(selector, "height", contentHeight);
    setCss(selector, "overflow", "hidden");
  } else {
    setCss(selector, "height", maxHeight);
    setCss(selector, "overflow", "auto");
  }
};
```

## Usage with WinnetouJS

Integration with WinnetouJS constructos and layout management:

```javascript
import { $modal, $tooltip, $sidebar } from "./components.wcto";
import {
  hideElements,
  showElements,
  isVisible,
  getGlobalPosition,
  scrollToElement,
  getWidth,
  getHeight,
} from "winnetoujs/modules/select";
import { W } from "winnetoujs";

// Modal management
const modal = new $modal({
  title: "Confirm Action",
  onClose: W.fx(self => {
    fadeOut(self, 200);
    setTimeout(() => hideElements(self), 200);
  }, "this"),
}).create("#modals");

// Initially hidden
hideElements(modal.ids.modal);

// Show modal function
const showModal = () => {
  showElements(modal.ids.modal);
  fadeIn(modal.ids.modal, 200);
  focusElement(modal.ids.closeButton);
};

// Responsive sidebar
const sidebar = new $sidebar().create("#app");

const manageSidebar = () => {
  const screenWidth = window.innerWidth;

  if (screenWidth < 768) {
    // Mobile: overlay sidebar
    hideElements(sidebar.ids.sidebar);
    addClass(sidebar.ids.sidebar, "overlay");
  } else {
    // Desktop: persistent sidebar
    showElements(sidebar.ids.sidebar);
    removeClass(sidebar.ids.sidebar, "overlay");
  }
};

// Smart tooltip positioning
const createTooltip = (trigger, content) => {
  const tooltip = new $tooltip({
    content: content,
  }).create("body");

  // Position relative to trigger
  const triggerRect = getGlobalPosition(trigger);
  const tooltipWidth = getWidth(tooltip.ids.tooltip);
  const tooltipHeight = getHeight(tooltip.ids.tooltip);

  let left = triggerRect.left + triggerRect.width / 2 - tooltipWidth / 2;
  let top = triggerRect.top - tooltipHeight - 10;

  // Viewport collision detection
  if (left < 10) left = 10;
  if (left + tooltipWidth > window.innerWidth - 10) {
    left = window.innerWidth - tooltipWidth - 10;
  }
  if (top < 10) {
    top = triggerRect.bottom + 10;
    addClass(tooltip.ids.tooltip, "below");
  }

  setCss(tooltip.ids.tooltip, "left", left);
  setCss(tooltip.ids.tooltip, "top", top);

  return tooltip;
};

// Scroll-to-top functionality
const scrollToTop = () => {
  scrollToElement("body", {
    behavior: "smooth",
    block: "start",
  });
};

// Lazy loading with scroll detection
onScroll(window, () => {
  const elements = document.querySelectorAll("[data-lazy]:not(.loaded)");

  elements.forEach(element => {
    const rect = getGlobalPosition(element);
    if (rect.top < window.innerHeight + 100) {
      loadLazyElement(element);
      addClass(element, "loaded");
    }
  });
});
```

## Layout Utilities

```javascript
import { getWidth, getHeight, setCss } from "winnetoujs/modules/select";

// Responsive grid layout
const createResponsiveGrid = container => {
  const containerWidth = getWidth(container);
  const cardMinWidth = 250;
  const gap = 20;

  const columns = Math.floor((containerWidth + gap) / (cardMinWidth + gap));
  const cardWidth = (containerWidth - gap * (columns - 1)) / columns;

  const cards = getChildren(container);
  cards.forEach(card => {
    setCss(card, "width", cardWidth);
  });
};

// Equal height columns
const equalizeHeights = selector => {
  const elements = getElements(selector);
  const maxHeight = Math.max(...elements.map(el => getHeight(el)));

  elements.forEach(element => {
    setCss(element, "min-height", maxHeight);
  });
};
```

## Performance Considerations

- **Batch Measurements**: Perform all reads before writes to avoid layout thrashing
- **Debounce Scroll Events**: Use debouncing for scroll-based calculations
- **Cache Dimensions**: Store frequently accessed dimensions
- **Viewport Optimization**: Only process visible elements when possible

## Browser Compatibility

All functions work in modern browsers supporting:

- ES6+ features
- getBoundingClientRect API
- Modern CSS properties
- Scroll behavior options
