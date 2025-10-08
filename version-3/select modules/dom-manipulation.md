# WinnetouJS DOM Manipulation Module

The DOM manipulation module provides powerful functions for modifying DOM elements, managing content, styling, and element operations in WinnetouJS applications. All functions support flexible selector targeting.

## Import

```javascript
import {
  removeElements,
  setHtml,
  getHtml,
  getText,
  appendHtml,
  prependHtml,
  setCss,
  toggleClass,
  addClass,
  removeClass,
  hasClass,
  setText,
  cloneElements,
  insertBefore,
  insertAfter,
  replaceElements,
} from "winnetoujs/modules/select";
```

## Selector Types

All functions accept the following selector types:

- **String**: CSS selector (`"#myId"`, `".myClass"`, `"div"`, `"#id1, #id2"`)
- **Element**: Single DOM element
- **Element[]**: Array of DOM elements

## Element Removal

### removeElements(selector)

Removes elements from the DOM.

**Parameters:**

- `selector` (Selector): The element(s) to remove

**Example:**

```javascript
import { removeElements } from "winnetoujs/modules/select";

// Remove specific element
removeElements("#temporaryDialog");

// Remove multiple elements
removeElements(".notification");

// Remove using constructo reference
const modal = new $modal().create("#app");
// Later...
removeElements(modal.ids.modal);
```

**Use Cases:**

- Closing modals and dialogs
- Removing temporary notifications
- Cleaning up dynamic content
- Implementing delete functionality

## HTML Content Management

### setHtml(selector, htmlContentString)

Sets the inner HTML content of elements.

**Parameters:**

- `selector` (Selector): The element(s) to set content for
- `htmlContentString` (string): The HTML content to set

**Example:**

```javascript
import { setHtml } from "winnetoujs/modules/select";

// Set content with HTML
setHtml("#contentArea", "<h2>Welcome</h2><p>Hello world!</p>");

// Set dynamic content
const userCard = `
  <div class="user-info">
    <h3>${user.name}</h3>
    <p>${user.email}</p>
  </div>
`;
setHtml("#userContainer", userCard);

// Clear content
setHtml("#results", "");
```

### getHtml(selector)

Gets the inner HTML content of an element.

**Parameters:**

- `selector` (Selector): The element to get content from

**Returns:**

- `string`: The inner HTML content

**Example:**

```javascript
import { getHtml } from "winnetoujs/modules/select";

// Get current content
const currentContent = getHtml("#editor");

// Save content before modifications
const backup = getHtml("#importantContent");
// Make changes...
// Restore if needed
setHtml("#importantContent", backup);
```

### getText(selector)

Gets the text content of an element (without HTML tags).

**Parameters:**

- `selector` (Selector): The element to get text from

**Returns:**

- `string | null`: The text content

**Example:**

```javascript
import { getText } from "winnetoujs/modules/select";

// Get plain text
const plainText = getText("#article");
const characterCount = getText("#content")?.length || 0;

// Extract text for processing
const searchText = getText("#searchResults")?.toLowerCase();
```

### setText(selector, text)

Sets the text content of elements (safely escapes HTML).

**Parameters:**

- `selector` (Selector): The element(s) to set text for
- `text` (string): The text content to set

**Example:**

```javascript
import { setText } from "winnetoujs/modules/select";

// Set safe text content
setText("#userName", user.name); // Automatically escapes HTML
setText("#message", userInput); // Safe from XSS

// Update counters
setText("#itemCount", `${items.length} items`);
```

### appendHtml(selector, htmlContentString)

Appends HTML content to the end of elements.

**Parameters:**

- `selector` (Selector): The element(s) to append content to
- `htmlContentString` (string): The HTML content to append

**Example:**

```javascript
import { appendHtml } from "winnetoujs/modules/select";

// Add new list item
appendHtml("#todoList", `<li data-id="${todo.id}">${todo.text}</li>`);

// Add notification to container
appendHtml(
  "#notifications",
  `
  <div class="alert alert-success">
    Operation completed successfully!
  </div>
`
);

// Build content incrementally
items.forEach(item => {
  appendHtml("#itemContainer", renderItem(item));
});
```

### prependHtml(selector, htmlContentString)

Prepends HTML content to the beginning of elements.

**Parameters:**

- `selector` (Selector): The element(s) to prepend content to
- `htmlContentString` (string): The HTML content to prepend

**Example:**

```javascript
import { prependHtml } from "winnetoujs/modules/select";

// Add latest message to top
prependHtml(
  "#chatMessages",
  `
  <div class="message latest">
    <strong>${user.name}:</strong> ${message}
  </div>
`
);

// Add urgent notification at top
prependHtml(
  "#alerts",
  `
  <div class="alert alert-danger">
    ${urgentMessage}
  </div>
`
);
```

## CSS Styling

### setCss(selector, property, value)

Sets CSS properties on elements.

**Parameters:**

- `selector` (Selector): The element(s) to style
- `property` (string): The CSS property name
- `value` (string | number): The CSS property value

**Example:**

```javascript
import { setCss } from "winnetoujs/modules/select";

// Set style properties
setCss("#myElement", "color", "red");
setCss("#container", "width", "100%");
setCss("#box", "height", 200); // Automatically adds 'px' for numbers

// Dynamic styling
setCss("#progressBar", "width", `${progress}%`);
setCss("#sidebar", "transform", `translateX(${isOpen ? 0 : -250}px)`);

// Responsive adjustments
if (window.innerWidth < 768) {
  setCss(".mobile-hidden", "display", "none");
}
```

**Behavior:**

- Automatically adds 'px' unit for numeric values
- Direct style property manipulation for immediate effect
- Works with all CSS properties

## CSS Classes

### addClass(selector, className)

Adds CSS classes to elements.

**Parameters:**

- `selector` (Selector): The element(s) to add classes to
- `className` (string): The class name to add

**Example:**

```javascript
import { addClass } from "winnetoujs/modules/select";

// Add state classes
addClass("#button", "active");
addClass(".form-field", "error");

// Add multiple classes to different elements
addClass("#sidebar", "open");
addClass("#overlay", "visible");
```

### removeClass(selector, className)

Removes CSS classes from elements.

**Parameters:**

- `selector` (Selector): The element(s) to remove classes from
- `className` (string): The class name to remove

**Example:**

```javascript
import { removeClass } from "winnetoujs/modules/select";

// Remove state classes
removeClass("#button", "loading");
removeClass(".form-field", "error");

// Clean up temporary classes
removeClass(".highlighted", "highlight");
```

### toggleClass(selector, className)

Toggles CSS classes on elements.

**Parameters:**

- `selector` (Selector): The element(s) to toggle classes on
- `className` (string): The class name to toggle

**Example:**

```javascript
import { toggleClass } from "winnetoujs/modules/select";

// Toggle visibility
toggleClass("#menu", "hidden");

// Toggle theme
toggleClass("body", "dark-theme");

// Interactive elements
onClick("#themeToggle", () => {
  toggleClass("html", "dark-mode");
});
```

### hasClass(selector, className)

Checks if an element has a specific CSS class.

**Parameters:**

- `selector` (Selector): The element to check
- `className` (string): The class name to check for

**Returns:**

- `boolean`: True if element has the class, false otherwise

**Example:**

```javascript
import { hasClass } from "winnetoujs/modules/select";

// Conditional logic based on classes
if (hasClass("#sidebar", "open")) {
  removeClass("#sidebar", "open");
  addClass("#sidebar", "closing");
}

// State checking
const isActive = hasClass("#tab1", "active");
const isDarkMode = hasClass("body", "dark-theme");
```

## Element Operations

### cloneElements(selector, deep?)

Creates copies of elements.

**Parameters:**

- `selector` (Selector): The element(s) to clone
- `deep` (boolean, optional): Whether to deep clone (default: true)

**Returns:**

- `Element[]`: Array of cloned elements

**Example:**

```javascript
import { cloneElements } from "winnetoujs/modules/select";

// Clone template
const template = cloneElements("#itemTemplate")[0];
template.id = `item-${newId}`;
setText(template, newItemText);

// Clone with modifications
const clonedCards = cloneElements(".product-card");
clonedCards.forEach((card, index) => {
  setAttribute(card, "data-clone-id", index);
});
```

### insertBefore(selector, content)

Inserts content before elements.

**Parameters:**

- `selector` (Selector): The element(s) to insert content before
- `content` (string | Element): The content to insert

**Example:**

```javascript
import { insertBefore } from "winnetoujs/modules/select";

// Insert HTML before element
insertBefore("#existingElement", "<div class='new-section'>New content</div>");

// Insert element before another
const newElement = document.createElement("div");
insertBefore("#target", newElement);

// Add header before content
insertBefore(
  "#mainContent",
  `
  <header class="page-header">
    <h1>${pageTitle}</h1>
  </header>
`
);
```

### insertAfter(selector, content)

Inserts content after elements.

**Parameters:**

- `selector` (Selector): The element(s) to insert content after
- `content` (string | Element): The content to insert

**Example:**

```javascript
import { insertAfter } from "winnetoujs/modules/select";

// Add footer after content
insertAfter(
  "#mainContent",
  `
  <footer class="page-footer">
    <p>&copy; 2024 Company Name</p>
  </footer>
`
);

// Insert elements dynamically
insertAfter(".form-field", "<span class='help-text'>Required field</span>");
```

### replaceElements(selector, newContent)

Replaces elements with new content.

**Parameters:**

- `selector` (Selector): The element(s) to replace
- `newContent` (string | Element): The replacement content

**Example:**

```javascript
import { replaceElements } from "winnetoujs/modules/select";

// Replace with new HTML
replaceElements(
  "#oldWidget",
  `
  <div id="newWidget" class="improved-widget">
    <h3>Updated Widget</h3>
    <p>New functionality</p>
  </div>
`
);

// Replace with created element
const newElement = document.createElement("section");
replaceElements("#placeholder", newElement);
```

## Usage with WinnetouJS

Integration with WinnetouJS constructos and reactive patterns:

```javascript
import { $productCard, $notification } from "./components.wcto";
import {
  appendHtml,
  removeClass,
  addClass,
  setText,
  setHtml,
} from "winnetoujs/modules/select";
import { W } from "winnetoujs";

// Dynamic content updates
const loadProducts = async () => {
  addClass("#loading", "visible");

  try {
    const products = await fetchProducts();
    setHtml("#productContainer", ""); // Clear existing

    products.forEach(product => {
      const card = new $productCard({
        title: product.name,
        price: product.price,
        image: product.image,
        onAddToCart: W.fx(() => {
          addClass(card.ids.productCard, "added-to-cart");
          setText(card.ids.addButton, "Added!");

          setTimeout(() => {
            removeClass(card.ids.productCard, "added-to-cart");
            setText(card.ids.addButton, "Add to Cart");
          }, 2000);
        }),
      }).create("#productContainer");
    });
  } finally {
    removeClass("#loading", "visible");
  }
};

// Real-time updates with mutables
const messageCount = W.initMutable(0);
W.setMutableNotPersistent(messageCount, newCount => {
  setText("#messageCounter", newCount);
  if (newCount > 0) {
    addClass("#messageIndicator", "has-messages");
  } else {
    removeClass("#messageIndicator", "has-messages");
  }
});
```

## Performance Considerations

- **Batch Operations**: Group multiple DOM operations together
- **Content vs Text**: Use `setText()` for user content to prevent XSS
- **Class Management**: Prefer CSS classes over inline styles for better performance
- **Element References**: Store element references when performing multiple operations

## Best Practices

1. **XSS Prevention**: Use `setText()` for user-generated content
2. **Semantic HTML**: Use `insertBefore`/`insertAfter` for proper document structure
3. **CSS Classes**: Prefer class manipulation over inline styles
4. **Memory Management**: Remove event listeners before removing elements
5. **Accessibility**: Maintain proper ARIA attributes when modifying DOM

## Browser Compatibility

All functions work in modern browsers supporting:

- ES6+ features
- Standard DOM manipulation APIs
- ClassList API
- Modern event handling
