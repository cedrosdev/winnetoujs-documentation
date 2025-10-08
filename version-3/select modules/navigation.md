# WinnetouJS Navigation Module

The navigation module provides functions for traversing and exploring DOM element relationships in WinnetouJS applications. These functions help you navigate the DOM tree structure efficiently.

## Import

```javascript
import {
  getParent,
  getChildren,
  getSiblings,
  getNext,
  getPrevious,
} from "winnetoujs/modules/select";
```

## Selector Types

All functions accept the following selector types:

- **String**: CSS selector (`"#myId"`, `".myClass"`, `"div"`, `"#id1, #id2"`)
- **Element**: Single DOM element
- **Element[]**: Array of DOM elements

## Parent Navigation

### getParent(selector)

Gets the parent element of the first matched element.

**Parameters:**

- `selector` (Selector): The element to get the parent of

**Returns:**

- `Element | null`: The parent element, or null if not found

**Example:**

```javascript
import { getParent } from "winnetoujs/modules/select";

// Get parent element
const parentElement = getParent("#childElement");
if (parentElement) {
  addClass(parentElement, "has-active-child");
}

// Navigate up the DOM tree
const button = document.querySelector(".submit-btn");
const form = getParent(button); // Gets the parent form

// Use with constructos
const card = new $productCard().create("#app");
const container = getParent(card.ids.productCard);
```

**Use Cases:**

- Finding containing elements
- Accessing form elements from inputs
- Implementing event delegation
- Managing component hierarchies

## Children Navigation

### getChildren(selector)

Gets all direct children of the first matched element.

**Parameters:**

- `selector` (Selector): The element to get children of

**Returns:**

- `Element[]`: Array of child elements

**Example:**

```javascript
import { getChildren } from "winnetoujs/modules/select";

// Get all children
const children = getChildren("#container");
children.forEach((child, index) => {
  child.setAttribute("data-index", index);
});

// Process menu items
const menuItems = getChildren("#navigationMenu");
menuItems.forEach(item => {
  onClick(item, () => {
    removeClass(menuItems, "active");
    addClass(item, "active");
  });
});

// Dynamic content management
const tabs = getChildren("#tabContainer");
const firstTab = tabs[0];
if (firstTab) {
  addClass(firstTab, "active");
}
```

**Use Cases:**

- Iterating through child elements
- Managing tab systems
- Processing list items
- Implementing accordion controls

## Sibling Navigation

### getSiblings(selector)

Gets all sibling elements of the first matched element (excluding the element itself).

**Parameters:**

- `selector` (Selector): The element to get siblings of

**Returns:**

- `Element[]`: Array of sibling elements

**Example:**

```javascript
import { getSiblings } from "winnetoujs/modules/select";

// Manage sibling states
const activateTab = tabElement => {
  const siblings = getSiblings(tabElement);

  // Deactivate all siblings
  removeClass(siblings, "active");

  // Activate current tab
  addClass(tabElement, "active");
};

// Process related elements
const currentCard = document.getElementById("card-3");
const siblingCards = getSiblings(currentCard);
siblingCards.forEach(card => {
  addClass(card, "related");
});

// Navigation controls
onClick(".nav-item", event => {
  const siblings = getSiblings(event.target);
  removeClass(siblings, "selected");
  addClass(event.target, "selected");
});
```

**Use Cases:**

- Tab management
- Menu item selection
- Gallery navigation
- Form step indicators

## Sequential Navigation

### getNext(selector)

Gets the next sibling element.

**Parameters:**

- `selector` (Selector): The element to get the next sibling of

**Returns:**

- `Element | null`: The next sibling element, or null if not found

**Example:**

```javascript
import { getNext } from "winnetoujs/modules/select";

// Navigate to next item
const nextButton = currentElement => {
  const nextElement = getNext(currentElement);
  if (nextElement) {
    removeClass(currentElement, "current");
    addClass(nextElement, "current");
    scrollToElement(nextElement);
  }
};

// Form navigation
onClick("#nextStepBtn", () => {
  const currentStep = document.querySelector(".step.active");
  const nextStep = getNext(currentStep);

  if (nextStep) {
    removeClass(currentStep, "active");
    addClass(nextStep, "active");
  }
});

// Carousel functionality
const showNextSlide = () => {
  const currentSlide = document.querySelector(".slide.active");
  const nextSlide = getNext(currentSlide) || getChildren("#carousel")[0]; // Loop to first

  removeClass(currentSlide, "active");
  addClass(nextSlide, "active");
};
```

### getPrevious(selector)

Gets the previous sibling element.

**Parameters:**

- `selector` (Selector): The element to get the previous sibling of

**Returns:**

- `Element | null`: The previous sibling element, or null if not found

**Example:**

```javascript
import { getPrevious } from "winnetoujs/modules/select";

// Navigate to previous item
const previousButton = currentElement => {
  const prevElement = getPrevious(currentElement);
  if (prevElement) {
    removeClass(currentElement, "current");
    addClass(prevElement, "current");
    scrollToElement(prevElement);
  }
};

// Breadcrumb navigation
onClick(".breadcrumb-back", event => {
  const currentCrumb = event.target.closest(".breadcrumb-item");
  const previousCrumb = getPrevious(currentCrumb);

  if (previousCrumb) {
    navigateToSection(previousCrumb.dataset.section);
  }
});

// Image gallery navigation
const showPreviousImage = () => {
  const currentImage = document.querySelector(".gallery-image.active");
  const prevImage = getPrevious(currentImage);

  if (prevImage) {
    removeClass(currentImage, "active");
    addClass(prevImage, "active");
  }
};
```

## Advanced Navigation Patterns

### Tree Traversal

```javascript
import { getParent, getChildren } from "winnetoujs/modules/select";

// Find root element
const findRoot = element => {
  let current = element;
  let parent = getParent(current);

  while (parent && parent !== document.body) {
    current = parent;
    parent = getParent(current);
  }

  return current;
};

// Get all descendants
const getAllDescendants = element => {
  const descendants = [];
  const children = getChildren(element);

  children.forEach(child => {
    descendants.push(child);
    descendants.push(...getAllDescendants(child));
  });

  return descendants;
};
```

### Component Management

```javascript
import { getParent, getChildren, getSiblings } from "winnetoujs/modules/select";

// Find component container
const findComponentContainer = element => {
  let current = element;

  while (current) {
    if (hasClass(current, "component")) {
      return current;
    }
    current = getParent(current);
  }

  return null;
};

// Manage component siblings
const updateComponentStates = activeComponent => {
  const container = getParent(activeComponent);
  const allComponents = getChildren(container);

  allComponents.forEach(component => {
    if (component === activeComponent) {
      addClass(component, "active");
    } else {
      removeClass(component, "inactive");
    }
  });
};
```

## Usage with WinnetouJS

Integration with WinnetouJS constructos and navigation patterns:

```javascript
import { $tabPanel, $accordionItem } from "./components.wcto";
import {
  getChildren,
  getSiblings,
  getNext,
  getPrevious,
} from "winnetoujs/modules/select";
import { W } from "winnetoujs";

// Tab system with navigation
const tabContainer = new $tabContainer().create("#app");

// Setup tab navigation
const tabs = getChildren(tabContainer.ids.tabList);
tabs.forEach((tab, index) => {
  onClick(
    tab,
    W.fx(self => {
      // Deactivate siblings
      const siblings = getSiblings(self);
      removeClass(siblings, "active");
      removeClass(getChildren(tabContainer.ids.tabPanels), "active");

      // Activate current
      addClass(self, "active");
      addClass(getChildren(tabContainer.ids.tabPanels)[index], "active");
    }, "this")
  );
});

// Keyboard navigation
onKeyDown(tabContainer.ids.tabList, event => {
  const activeTab = document.querySelector(".tab.active");
  let targetTab = null;

  if (event.key === "ArrowRight") {
    targetTab = getNext(activeTab) || getChildren(tabContainer.ids.tabList)[0];
  } else if (event.key === "ArrowLeft") {
    const tabs = getChildren(tabContainer.ids.tabList);
    targetTab = getPrevious(activeTab) || tabs[tabs.length - 1];
  }

  if (targetTab) {
    targetTab.click();
    focusElement(targetTab);
  }
});

// Accordion with sibling management
const setupAccordion = () => {
  onClick(".accordion-header", event => {
    const header = event.target;
    const item = getParent(header);
    const siblings = getSiblings(item);

    // Close all siblings
    siblings.forEach(sibling => {
      removeClass(sibling, "open");
    });

    // Toggle current
    toggleClass(item, "open");
  });
};
```

## Form Navigation

```javascript
import { getNext, getPrevious, getParent } from "winnetoujs/modules/select";

// Smart form navigation
const setupFormNavigation = () => {
  // Next field on Enter
  onKeyDown(".form-field input", event => {
    if (event.key === "Enter") {
      event.preventDefault();
      const currentField = getParent(event.target);
      const nextField = getNext(currentField);

      if (nextField) {
        const nextInput = findElements(nextField, "input, select, textarea")[0];
        if (nextInput) {
          focusElement(nextInput);
        }
      }
    }
  });

  // Previous field on Shift+Tab
  onKeyDown(".form-field input", event => {
    if (event.key === "Tab" && event.shiftKey) {
      const currentField = getParent(event.target);
      const prevField = getPrevious(currentField);

      if (prevField) {
        const prevInput = findElements(prevField, "input, select, textarea")[0];
        if (prevInput) {
          event.preventDefault();
          focusElement(prevInput);
        }
      }
    }
  });
};
```

## Performance Considerations

- **Caching Results**: Store navigation results when doing multiple operations
- **Null Checks**: Always check for null returns before using elements
- **Batch Operations**: Group multiple navigation operations together
- **Selector Optimization**: Use direct element references when available

## Common Use Cases

1. **Tab Systems**: Managing active states across tabs
2. **Accordions**: Controlling expand/collapse states
3. **Carousels**: Navigation between slides
4. **Form Steps**: Moving between form sections
5. **Tree Views**: Expanding/collapsing tree nodes
6. **Menu Systems**: Managing menu item states

## Browser Compatibility

All functions work in modern browsers supporting:

- ES6+ features
- Standard DOM navigation properties
- Element relationship APIs
- Modern query selectors
