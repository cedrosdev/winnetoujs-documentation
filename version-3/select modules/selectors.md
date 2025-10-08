# WinnetouJS Selectors Module

The selectors module provides the core element selection and querying functionality for the WinnetouJS select system. It offers flexible and powerful ways to target DOM elements using various selector patterns.

## Import

```javascript
import { getElements, exists, findElements } from "winnetoujs/modules/select";
```

## Core Functions

### getElements(selector)

The foundational function that converts various selector types into arrays of DOM elements.

**Parameters:**

- `selector` (string | Element): The selector to convert

**Returns:**

- `Element[]`: Array of matching DOM elements

**Selector Types Supported:**

#### CSS Selectors with Commas

```javascript
import { getElements } from "winnetoujs/modules/select";

// Multiple selectors
const elements = getElements("#header, .sidebar, footer");
// Returns all elements matching any of the selectors
```

#### Complex CSS Selectors

```javascript
// Descendant selectors
const elements = getElements("#container .card");
// Returns all elements with class 'card' inside element with id 'container'

// Attribute selectors
const elements = getElements("[data-active='true']");

// Pseudo-selectors
const elements = getElements(".item:first-child");
```

#### ID Selectors

```javascript
// Standard ID selector
const elements = getElements("#myElement");
// Returns array with the element (or empty array if not found)

// ID without hash symbol
const elements = getElements("myElement");
// Automatically detects and handles as ID selector
```

#### Class Selectors

```javascript
// Standard class selector
const elements = getElements(".myClass");

// Class without dot
const elements = getElements("myClass");
// Automatically detects and handles as class selector
```

#### Tag Selectors

```javascript
// HTML tag selectors
const elements = getElements("div");
const elements = getElements("input");
const elements = getElements("button");
```

#### WinnetouJS Constructo IDs

```javascript
// Constructo-generated IDs (containing "-win-")
const elements = getElements("myElement-win-123");
// Automatically handles WinnetouJS generated element IDs
```

#### Direct Element Objects

```javascript
// Pass through existing elements
const element = document.getElementById("myElement");
const elements = getElements(element);
// Returns [element]
```

**Examples:**

```javascript
import { getElements } from "winnetoujs/modules/select";

// Basic usage
const buttons = getElements("button");
const header = getElements("#header");
const cards = getElements(".product-card");

// Complex selectors
const activeCards = getElements(".card.active");
const formInputs = getElements("#contactForm input, #contactForm select");

// Dynamic selectors
const selectorString = user.isAdmin ? ".admin-panel" : ".user-panel";
const panels = getElements(selectorString);

// With WinnetouJS constructos
const modal = new $modal().create("#app");
const modalElements = getElements(modal.ids.modal);
```

**Selection Logic:**

1. **Comma detection**: Multiple selectors separated by commas
2. **Space detection**: Descendant/child selectors
3. **Hash detection**: ID selectors (`#id`)
4. **Dot detection**: Class selectors (`.class`)
5. **WinnetouJS ID detection**: IDs containing `-win-`
6. **Tag fallback**: HTML tag names
7. **ID fallback**: Try as ID if tag search fails

### exists(selector)

Checks if elements matching the selector exist in the DOM.

**Parameters:**

- `selector` (string | Element | Element[]): The selector to check

**Returns:**

- `boolean`: True if elements exist, false otherwise

**Example:**

```javascript
import { exists } from "winnetoujs/modules/select";

// Check if elements exist before manipulating
if (exists("#sidebar")) {
  addClass("#sidebar", "visible");
}

// Conditional logic
if (!exists(".error-message")) {
  appendHtml("#form", "<div class='error-message'>Please fix errors</div>");
}

// Check constructo existence
const modal = new $modal().create("#app");
if (exists(modal.ids.modal)) {
  showElements(modal.ids.modal);
}

// Form validation
const validateRequiredFields = () => {
  const missingFields = [];

  if (!exists("#name") || !getValue("#name")) {
    missingFields.push("Name");
  }

  if (!exists("#email") || !getValue("#email")) {
    missingFields.push("Email");
  }

  return missingFields;
};

// Feature detection
if (exists(".mobile-menu")) {
  // Mobile-specific functionality
  setupMobileMenu();
}
```

**Use Cases:**

- Preventing errors when elements might not exist
- Conditional feature initialization
- Form validation
- Dynamic content checking
- Component existence verification

### findElements(parentSelector, childSelector)

Finds child elements within a parent element.

**Parameters:**

- `parentSelector` (string | Element | Element[]): The parent element(s) to search within
- `childSelector` (string): The child selector to find

**Returns:**

- `Element[]`: Array of child elements found

**Example:**

```javascript
import { findElements } from "winnetoujs/modules/select";

// Find children within specific parent
const buttons = findElements("#toolbar", "button");
const inputs = findElements("#form", "input[required]");

// Scoped searching
const activeCards = findElements(".container", ".card.active");

// With constructos
const form = new $userForm().create("#app");
const formFields = findElements(form.ids.userForm, ".form-field");

// Complex child searches
const errorFields = findElements("#registrationForm", ".field.error input");

// Multiple parent search
const allMenuItems = findElements(".navigation", "a");

// Nested component searching
const findComponentButtons = componentSelector => {
  return findElements(componentSelector, "[data-action]");
};
```

**Advanced Examples:**

```javascript
// Interactive element discovery
const setupInteractiveElements = container => {
  const clickables = findElements(container, "button, a, [data-clickable]");
  clickables.forEach(element => {
    addClass(element, "interactive");
  });
};

// Form field processing
const processFormFields = formSelector => {
  const textFields = findElements(formSelector, "input[type='text'], textarea");
  const selectFields = findElements(formSelector, "select");
  const checkboxes = findElements(formSelector, "input[type='checkbox']");

  return {
    text: textFields,
    select: selectFields,
    checkbox: checkboxes,
  };
};

// Component scanning
const scanForComponents = container => {
  const components = {
    modals: findElements(container, "[data-component='modal']"),
    tooltips: findElements(container, "[data-component='tooltip']"),
    tabs: findElements(container, "[data-component='tabs']"),
  };

  return components;
};
```

## Selector Pattern Examples

### Working with WinnetouJS Constructos

```javascript
import { $productCard, $modal } from "./components.wcto";
import { getElements, exists, findElements } from "winnetoujs/modules/select";

// Create and select constructo elements
const card = new $productCard({
  title: "Product Name",
  price: "$99.99",
}).create("#products");

// Select the constructo element
const cardElement = getElements(card.ids.productCard)[0];

// Find elements within constructo
const cardButtons = findElements(card.ids.productCard, "button");
const cardImage = findElements(card.ids.productCard, ".product-image")[0];

// Check constructo element existence
if (exists(card.ids.productCard)) {
  addClass(card.ids.productCard, "loaded");
}
```

### Dynamic Selector Building

```javascript
// Build selectors programmatically
const buildSelector = (baseClass, modifiers = []) => {
  let selector = `.${baseClass}`;
  modifiers.forEach(modifier => {
    selector += `.${modifier}`;
  });
  return selector;
};

// Usage
const activeCards = getElements(buildSelector("card", ["active", "featured"]));
// Equivalent to: getElements(".card.active.featured")

// Conditional selector building
const getUserPanelSelector = user => {
  const selectors = [".user-panel"];

  if (user.isAdmin) selectors.push(".admin-panel");
  if (user.isPremium) selectors.push(".premium-panel");

  return selectors.join(", ");
};

const panels = getElements(getUserPanelSelector(currentUser));
```

### Advanced Selection Patterns

```javascript
// Multi-level element searching
const findNestedElements = rootSelector => {
  const root = getElements(rootSelector)[0];
  if (!root) return [];

  // Level 1: Direct children
  const level1 = findElements(root, "> .level-1");

  // Level 2: Grandchildren
  const level2 = level1.flatMap(parent => findElements(parent, "> .level-2"));

  return { level1, level2 };
};

// Filtered selections
const getVisibleElements = selector => {
  return getElements(selector).filter(element => isVisible(element));
};

const getEnabledButtons = containerSelector => {
  const allButtons = findElements(containerSelector, "button");
  return allButtons.filter(button => !button.disabled);
};

// Selection with validation
const safeGetElements = (selector, fallback = []) => {
  try {
    const elements = getElements(selector);
    return elements.length > 0 ? elements : fallback;
  } catch (error) {
    console.warn(`Invalid selector: ${selector}`, error);
    return fallback;
  }
};
```

## Integration with Other Modules

### With DOM Manipulation

```javascript
import { getElements, findElements } from "winnetoujs/modules/select";
import { setText, addClass } from "winnetoujs/modules/select";

// Bulk operations
const elements = getElements(".status-indicator");
elements.forEach((element, index) => {
  setText(element, `Status ${index + 1}`);
  addClass(element, `status-${index}`);
});
```

### With Events

```javascript
import { getElements, findElements } from "winnetoujs/modules/select";
import { onClick, onChange } from "winnetoujs/modules/select";

// Event delegation with scoped selection
const form = getElements("#userForm")[0];
const submitButtons = findElements(form, "[type='submit']");

submitButtons.forEach(button => {
  onClick(button, handleFormSubmit);
});
```

### With Animations

```javascript
import { getElements } from "winnetoujs/modules/select";
import { fadeIn, slideDown } from "winnetoujs/modules/select";

// Animate multiple elements
const cards = getElements(".product-card");
cards.forEach((card, index) => {
  setTimeout(() => {
    fadeIn(card, 300);
  }, index * 100); // Staggered animation
});
```

## Performance Tips

1. **Specific Selectors**: Use specific selectors to reduce search scope
2. **Cache Results**: Store frequently accessed element arrays
3. **Batch Operations**: Group multiple selections together
4. **Scope Searching**: Use `findElements` to limit search scope

```javascript
// Good: Specific and cached
const navButtons = getElements("#navigation button");
navButtons.forEach(button => {
  // Process buttons
});

// Better: Scoped search
const buttons = findElements("#navigation", "button");

// Best: Combined with caching
const navigationCache = {
  buttons: findElements("#navigation", "button"),
  links: findElements("#navigation", "a"),
  dropdown: findElements("#navigation", ".dropdown"),
};
```

## Error Handling

```javascript
// Safe element selection
const safeSelect = selector => {
  try {
    const elements = getElements(selector);
    return elements.length > 0 ? elements : null;
  } catch (error) {
    console.error(`Selection failed for: ${selector}`, error);
    return null;
  }
};

// Existence checking before operations
const safeOperation = (selector, operation) => {
  if (exists(selector)) {
    operation(getElements(selector));
  } else {
    console.warn(`Elements not found: ${selector}`);
  }
};
```

## Browser Compatibility

The selectors module works in all modern browsers supporting:

- ES6+ features
- querySelector/querySelectorAll API
- getElementById/getElementsByClassName
- getElementsByTagName
- Standard DOM traversal methods
