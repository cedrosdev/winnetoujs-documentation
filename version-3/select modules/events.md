# WinnetouJS Events Module

The events module provides comprehensive event handling capabilities for WinnetouJS applications. It offers both low-level event management and convenient high-level event handlers for common user interactions.

## Import

```javascript
import {
  __addEventListener,
  __removeEventListener,
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
} from "winnetoujs/modules/select";
```

## Selector Types

All functions accept the following selector types:

- **String**: CSS selector (`"#myId"`, `".myClass"`, `"div"`, `"#id1, #id2"`)
- **Element**: Single DOM element
- **Element[]**: Array of DOM elements

## Core Event Management

### \_\_addEventListener(selector, event, handler, options?)

Low-level function to add event listeners to elements.

**Parameters:**

- `selector` (Selector): The element(s) to add event listener to
- `event` (string): The event type (e.g., 'click', 'change')
- `handler` (EventHandler): The event handler function
- `options` (AddEventListenerOptions, optional): Event listener options

**Example:**

```javascript
import { __addEventListener } from "winnetoujs/modules/select";

// Basic event listener
__addEventListener("#button", "click", event => {
  console.log("Button clicked!", event);
});

// With options
__addEventListener("#form", "submit", handleSubmit, {
  once: true,
  passive: false,
});

// Custom events
__addEventListener("#widget", "customEvent", event => {
  console.log("Custom event triggered:", event.detail);
});
```

### \_\_removeEventListener(selector, event, handler)

Removes event listeners from elements.

**Parameters:**

- `selector` (Selector): The element(s) to remove event listener from
- `event` (string): The event type
- `handler` (EventHandler): The exact handler function to remove

**Example:**

```javascript
import { __removeEventListener } from "winnetoujs/modules/select";

// Remove specific handler
const handleClick = event => {
  /* handler logic */
};
__addEventListener("#button", "click", handleClick);
// Later...
__removeEventListener("#button", "click", handleClick);

// Cleanup on component destroy
const cleanup = () => {
  __removeEventListener("#element", "resize", handleResize);
  __removeEventListener("#element", "scroll", handleScroll);
};
```

## Mouse Events

### onClick(selector, handler)

Adds click event listeners to elements.

**Parameters:**

- `selector` (Selector): The element(s) to add click handler to
- `handler` (EventHandler): The click handler function

**Example:**

```javascript
import { onClick } from "winnetoujs/modules/select";

// Simple click handler
onClick("#submitButton", event => {
  event.preventDefault();
  submitForm();
});

// Multiple elements
onClick(".tab-button", event => {
  const tabId = event.target.dataset.tabId;
  showTab(tabId);
});

// With WinnetouJS constructos
const menu = new $navigationMenu().create("#app");
onClick(menu.ids.menuToggle, event => {
  toggleClass(menu.ids.menuItems, "visible");
});
```

### onMouseOver(selector, handler) / onMouseOut(selector, handler)

Handles mouse over and out events.

**Example:**

```javascript
import { onMouseOver, onMouseOut } from "winnetoujs/modules/select";

// Hover effects
onMouseOver(".card", event => {
  addClass(event.target, "hover");
});

onMouseOut(".card", event => {
  removeClass(event.target, "hover");
});

// Tooltip functionality
onMouseOver("[data-tooltip]", event => {
  showTooltip(event.target.dataset.tooltip, event);
});

onMouseOut("[data-tooltip]", () => {
  hideTooltip();
});
```

### onMouseEnter(selector, handler) / onMouseLeave(selector, handler)

Handles mouse enter and leave events (no bubbling).

**Example:**

```javascript
import { onMouseEnter, onMouseLeave } from "winnetoujs/modules/select";

// Dropdown menus
onMouseEnter(".dropdown", event => {
  addClass(event.target.querySelector(".dropdown-menu"), "visible");
});

onMouseLeave(".dropdown", event => {
  removeClass(event.target.querySelector(".dropdown-menu"), "visible");
});
```

## Form Events

### onSubmit(selector, handler)

Handles form submission events.

**Parameters:**

- `selector` (Selector): The form element(s)
- `handler` (EventHandler): The submit handler function

**Example:**

```javascript
import { onSubmit, serializeForm } from "winnetoujs/modules/select";

// Form validation and submission
onSubmit("#contactForm", async event => {
  event.preventDefault();

  const formData = serializeForm("#contactForm");

  // Validate
  if (!formData.email || !formData.message) {
    showError("Please fill in all required fields");
    return;
  }

  // Submit
  try {
    await submitContactForm(formData);
    showSuccess("Message sent successfully!");
    resetForm("#contactForm");
  } catch (error) {
    showError("Failed to send message");
  }
});
```

### onChange(selector, handler)

Handles change events on form elements.

**Example:**

```javascript
import { onChange, getValue } from "winnetoujs/modules/select";

// Update UI based on selection
onChange("#countrySelect", event => {
  const country = getValue("#countrySelect");
  updateStateOptions(country);
});

// Auto-save functionality
onChange(".auto-save", event => {
  const fieldName = event.target.name;
  const value = getValue(event.target);
  autoSaveField(fieldName, value);
});
```

### onInput(selector, handler)

Handles input events (real-time text input).

**Example:**

```javascript
import { onInput, getValue, setText } from "winnetoujs/modules/select";

// Real-time search
onInput("#searchInput", event => {
  const query = getValue("#searchInput");

  if (query.length > 2) {
    performSearch(query);
  } else {
    clearSearchResults();
  }
});

// Character counter
onInput("#messageText", event => {
  const text = getValue("#messageText");
  const remaining = 280 - text.length;
  setText("#charCounter", `${remaining} characters remaining`);

  if (remaining < 0) {
    addClass("#charCounter", "error");
  } else {
    removeClass("#charCounter", "error");
  }
});
```

## Focus Events

### onFocus(selector, handler) / onBlur(selector, handler)

Handles focus and blur events.

**Example:**

```javascript
import { onFocus, onBlur } from "winnetoujs/modules/select";

// Form field styling
onFocus(".form-input", event => {
  addClass(event.target.parentElement, "focused");
});

onBlur(".form-input", event => {
  removeClass(event.target.parentElement, "focused");

  // Validation on blur
  validateField(event.target);
});

// Auto-save on blur
onBlur(".auto-save-field", event => {
  const value = getValue(event.target);
  saveFieldValue(event.target.name, value);
});
```

### focusElement(selector) / blurElement(selector)

Programmatically focus or blur elements.

**Example:**

```javascript
import { focusElement, blurElement } from "winnetoujs/modules/select";

// Auto-focus first input
focusElement("#firstNameInput");

// Focus after modal opens
const openModal = () => {
  showModal("#loginModal");
  setTimeout(() => {
    focusElement("#usernameInput");
  }, 100);
};

// Remove focus for custom handling
blurElement("#customInput");
```

## Keyboard Events

### onKeyPress(selector, handler) / onKeyDown(selector, handler) / onKeyUp(selector, handler)

Handle keyboard events.

**Example:**

```javascript
import { onKeyDown, onKeyUp } from "winnetoujs/modules/select";

// Enter key submission
onKeyDown("#messageInput", event => {
  if (event.key === "Enter" && !event.shiftKey) {
    event.preventDefault();
    sendMessage();
  }
});

// Escape key handling
onKeyDown(document, event => {
  if (event.key === "Escape") {
    closeAllModals();
  }
});

// Keyboard shortcuts
onKeyDown(document, event => {
  if (event.ctrlKey && event.key === "s") {
    event.preventDefault();
    saveDocument();
  }
});

// Tab navigation
onKeyUp(".tab-container", event => {
  if (event.key === "ArrowLeft" || event.key === "ArrowRight") {
    handleTabNavigation(event);
  }
});
```

## Text Selection

### selectText(selector)

Programmatically selects text in input/textarea elements.

**Example:**

```javascript
import { selectText } from "winnetoujs/modules/select";

// Select all text on focus
onFocus("#urlInput", () => {
  selectText("#urlInput");
});

// Select text for copying
onClick("#selectAllBtn", () => {
  selectText("#codeOutput");
});
```

## Usage with WinnetouJS

Integration with WinnetouJS constructos and W.fx():

```javascript
import { $todoItem, $modal } from "./components.wcto";
import { onClick, onChange, onKeyDown } from "winnetoujs/modules/select";
import { W } from "winnetoujs";

// Using W.fx() for constructo events
const todoList = new $todoList({
  onAddTodo: W.fx(self => {
    const input = findElements(self, "input")[0];
    const todoText = getValue(input);

    if (todoText.trim()) {
      addTodoItem(todoText);
      setValue(input, "");
    }
  }, "this"),
}).create("#app");

// External event handling for constructos
const modal = new $modal({
  title: "Confirm Delete",
  content: "Are you sure you want to delete this item?",
}).create("#modals");

// Close modal on escape
onKeyDown(document, event => {
  if (event.key === "Escape" && isVisible(modal.ids.modal)) {
    fadeOut(modal.ids.modal, 200);
  }
});

// Enhanced form handling
const form = new $userForm().create("#app");

onChange(form.ids.emailInput, event => {
  const email = getValue(event.target);
  if (isValidEmail(email)) {
    removeClass(event.target, "error");
    addClass(event.target, "valid");
  } else {
    removeClass(event.target, "valid");
    addClass(event.target, "error");
  }
});
```

## Event Delegation

For dynamic content, use event delegation:

```javascript
import { onClick } from "winnetoujs/modules/select";

// Handle clicks on dynamically added elements
onClick(document, event => {
  // Delete buttons
  if (event.target.matches(".delete-btn")) {
    const itemId = event.target.dataset.itemId;
    deleteItem(itemId);
  }

  // Edit buttons
  if (event.target.matches(".edit-btn")) {
    const itemId = event.target.dataset.itemId;
    editItem(itemId);
  }
});
```

## Custom Events

Working with custom events:

```javascript
import { __addEventListener } from "winnetoujs/modules/select";

// Listen for custom events
__addEventListener("#widget", "dataUpdated", event => {
  console.log("Data updated:", event.detail);
  refreshWidget(event.detail);
});

// Dispatch custom events
const dispatchCustomEvent = (element, eventName, data) => {
  const event = new CustomEvent(eventName, { detail: data });
  element.dispatchEvent(event);
};
```

## Performance Considerations

- **Event Delegation**: Use for dynamic content
- **Cleanup**: Remove event listeners when elements are destroyed
- **Passive Events**: Use passive option for scroll/touch events
- **Debouncing**: Implement for high-frequency events like scroll/resize

## Browser Compatibility

All functions work in modern browsers supporting:

- ES6+ features
- Standard DOM events
- AddEventListener API
- CustomEvent API (for custom events)
