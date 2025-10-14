# WStyle: Inline Styling with JavaScript Objects

## Overview

WStyle is a JavaScript-based styling methodology for WinnetouJs applications that allows you to define CSS styles as JavaScript objects. It provides a way to create reusable, type-safe style definitions that can be applied directly to constructos without writing separate CSS files.

**Key Features:**

- 🎨 Define styles as JavaScript objects
- 🔄 Reusable style definitions
- 📦 Export and import styles across files
- 🎯 Direct integration with constructos
- ⚡ No separate CSS compilation needed
- 🔍 Full IDE support with autocomplete

**Important Consideration:**

> ⚠️ **Bundle Size Warning:** WStyle can increase your final bundle size since styles are included in the JavaScript bundle. Use it wisely and only when appropriate. For large-scale styling, prefer Sass/CSS files.

## When to Use WStyle

WStyle is ideal for:

- ✅ Dynamic styles that change based on application state
- ✅ Small, component-specific styles
- ✅ Prototyping and rapid development
- ✅ Styles that need to be shared across multiple constructos
- ✅ Theme variations that are conditionally applied

**Avoid WStyle for:**

- ❌ Large-scale application styling
- ❌ Complex responsive layouts (use Sass with media queries instead)
- ❌ Animations and keyframes (use CSS/Sass)
- ❌ Global styles and resets (use CSS/Sass)

## Basic Usage

### Importing WStyle

First, import the `wstyle` function from the WinnetouJs style module:

```javascript
import { wstyle } from "winnetoujs/modules/style";
```

### Creating a Style Object

Define your styles using the `wstyle()` function, which accepts a CSS-like object:

```javascript
import { wstyle } from "winnetoujs/modules/style";

export const buttonStyle = wstyle({
  padding: "10px 20px",
  "background-color": "#3498db",
  color: "white",
  border: "none",
  "border-radius": "4px",
  cursor: "pointer",
  "font-size": "16px",
});
```

**Note:** CSS properties with hyphens must be quoted (e.g., `"background-color"`, `"border-radius"`).

### Applying Styles to Constructos

Use the `style` attribute in your constructo to apply the WStyle object:

```html
<!-- buttons.wcto.html -->
<winnetou description="A styled button">
  <button id="[[styledButton]]" style="{{style}}" onclick="{{onclick}}">
    {{text}}
  </button>
</winnetou>
```

```javascript
import { W } from "winnetoujs";
import { $styledButton } from "./buttons.wcto";
import { buttonStyle } from "./styles";

new $styledButton({
  text: "Click Me",
  style: buttonStyle,
  onclick: W.fx(() => {
    console.log("Button clicked!");
  }),
}).create("#app");
```

## Organizing WStyle Definitions

### Single Style File

For better organization, create a dedicated file for your style definitions:

**`src/styles/buttonStyles.js`**

```javascript
import { wstyle } from "winnetoujs/modules/style";

export const primaryButton = wstyle({
  padding: "12px 24px",
  "background-color": "#3498db",
  color: "white",
  border: "none",
  "border-radius": "6px",
  cursor: "pointer",
  "font-size": "16px",
  "font-weight": "500",
  transition: "all 0.3s ease",
});

export const secondaryButton = wstyle({
  padding: "12px 24px",
  "background-color": "#95a5a6",
  color: "white",
  border: "none",
  "border-radius": "6px",
  cursor: "pointer",
  "font-size": "16px",
  "font-weight": "500",
  transition: "all 0.3s ease",
});

export const dangerButton = wstyle({
  padding: "12px 24px",
  "background-color": "#e74c3c",
  color: "white",
  border: "none",
  "border-radius": "6px",
  cursor: "pointer",
  "font-size": "16px",
  "font-weight": "500",
  transition: "all 0.3s ease",
});
```

**Usage:**

```javascript
import { $styledButton } from "./buttons.wcto";
import { primaryButton, secondaryButton, dangerButton } from "./styles/buttonStyles";

// Primary button
new $styledButton({
  text: "Save",
  style: primaryButton,
  onclick: W.fx(() => handleSave()),
}).create("#app");

// Secondary button
new $styledButton({
  text: "Cancel",
  style: secondaryButton,
  onclick: W.fx(() => handleCancel()),
}).create("#app");

// Danger button
new $styledButton({
  text: "Delete",
  style: dangerButton,
  onclick: W.fx(() => handleDelete()),
}).create("#app");
```

### Co-locating Styles with Constructos

You can also keep style definitions close to their constructos:

```
src/
└── components/
    └── menu/
        ├── menu.wcto.html
        ├── menu.ts
        └── menuStyles.js
```

**`src/components/menu/menuStyles.js`**

```javascript
import { wstyle } from "winnetoujs/modules/style";

export const leftMenu = wstyle({
  width: "100%",
  height: "50px",
  color: "white",
  "background-color": "#2c3e50",
  "border-radius": "5px",
  display: "flex",
  "align-items": "center",
  "justify-content": "center",
  cursor: "pointer",
  "font-size": "16px",
  "text-transform": "uppercase",
  transition: "background-color 0.3s ease",
});

export const menuItem = wstyle({
  padding: "12px 20px",
  display: "flex",
  "align-items": "center",
  gap: "10px",
  cursor: "pointer",
  transition: "background-color 0.2s ease",
});
```

**`src/components/menu/menu.ts`**

```javascript
import { W } from "winnetoujs";
import { $leftMenu } from "./menu.wcto";
import { leftMenu } from "./menuStyles";

export class Menu {
  constructor(output) {
    this.output = output;
    this.render();
  }

  render() {
    new $leftMenu({
      style: leftMenu,
      text: "Dashboard",
      onclick: W.fx(() => this.navigate("/dashboard")),
    }).create(this.output);
  }

  navigate(route) {
    console.log("Navigating to:", route);
  }
}
```

## Common Style Patterns

### Layout Styles

```javascript
import { wstyle } from "winnetoujs/modules/style";

export const flexCenter = wstyle({
  display: "flex",
  "align-items": "center",
  "justify-content": "center",
});

export const flexColumn = wstyle({
  display: "flex",
  "flex-direction": "column",
});

export const flexBetween = wstyle({
  display: "flex",
  "align-items": "center",
  "justify-content": "space-between",
});

export const gridContainer = wstyle({
  display: "grid",
  "grid-template-columns": "repeat(auto-fit, minmax(250px, 1fr))",
  gap: "20px",
});
```

### Card Styles

```javascript
import { wstyle } from "winnetoujs/modules/style";

export const card = wstyle({
  "background-color": "white",
  "border-radius": "8px",
  padding: "20px",
  "box-shadow": "0 2px 4px rgba(0, 0, 0, 0.1)",
  transition: "transform 0.2s ease, box-shadow 0.2s ease",
});

export const cardHeader = wstyle({
  "font-size": "20px",
  "font-weight": "bold",
  "margin-bottom": "12px",
  "border-bottom": "1px solid #e0e0e0",
  "padding-bottom": "10px",
});

export const cardBody = wstyle({
  "line-height": "1.6",
  color: "#555",
});
```

### Form Styles

```javascript
import { wstyle } from "winnetoujs/modules/style";

export const inputField = wstyle({
  width: "100%",
  padding: "12px",
  border: "1px solid #ddd",
  "border-radius": "4px",
  "font-size": "16px",
  transition: "border-color 0.3s ease",
});

export const label = wstyle({
  display: "block",
  "margin-bottom": "8px",
  "font-weight": "500",
  color: "#333",
});

export const formGroup = wstyle({
  "margin-bottom": "20px",
});
```

## Dynamic Styles with Functions

Create dynamic styles by wrapping `wstyle()` in a function:

```javascript
import { wstyle } from "winnetoujs/modules/style";

export function createButtonStyle(backgroundColor, textColor) {
  return wstyle({
    padding: "12px 24px",
    "background-color": backgroundColor,
    color: textColor,
    border: "none",
    "border-radius": "6px",
    cursor: "pointer",
    "font-size": "16px",
    transition: "opacity 0.3s ease",
  });
}

export function createCardStyle(bgColor, borderColor) {
  return wstyle({
    "background-color": bgColor,
    border: `2px solid ${borderColor}`,
    "border-radius": "8px",
    padding: "20px",
    margin: "10px",
  });
}
```

**Usage:**

```javascript
import { $styledButton } from "./buttons.wcto";
import { createButtonStyle } from "./styles";

// Create custom button styles
const successButton = createButtonStyle("#27ae60", "white");
const warningButton = createButtonStyle("#f39c12", "white");

new $styledButton({
  text: "Success",
  style: successButton,
  onclick: W.fx(() => console.log("Success")),
}).create("#app");

new $styledButton({
  text: "Warning",
  style: warningButton,
  onclick: W.fx(() => console.log("Warning")),
}).create("#app");
```

## Combining WStyle with CSS Variables

For maximum flexibility, combine WStyle with CSS custom properties (variables):

**`styles/main.css`**

```css
:root {
  --primary-color: #3498db;
  --secondary-color: #2ecc71;
  --danger-color: #e74c3c;
  --text-color: #333;
  --border-radius: 6px;
  --spacing-md: 12px;
  --spacing-lg: 20px;
}
```

**`styles/buttonStyles.js`**

```javascript
import { wstyle } from "winnetoujs/modules/style";

export const themedButton = wstyle({
  padding: "var(--spacing-md) var(--spacing-lg)",
  "background-color": "var(--primary-color)",
  color: "white",
  border: "none",
  "border-radius": "var(--border-radius)",
  cursor: "pointer",
  "font-size": "16px",
});
```

This approach allows you to:
- Change theme colors dynamically using the ColorThemes module
- Maintain consistent spacing and sizing
- Reduce duplication
- Enable runtime theme switching

## WStyle with State Management

Combine WStyle with WinnetouJs mutables for reactive styling:

```javascript
import { W } from "winnetoujs";
import { wstyle } from "winnetoujs/modules/style";
import { $styledDiv } from "./components.wcto";

// Create different style states
const normalState = wstyle({
  "background-color": "#ecf0f1",
  color: "#2c3e50",
  padding: "20px",
  "border-radius": "8px",
});

const activeState = wstyle({
  "background-color": "#3498db",
  color: "white",
  padding: "20px",
  "border-radius": "8px",
  transform: "scale(1.05)",
  "box-shadow": "0 4px 12px rgba(52, 152, 219, 0.3)",
});

// Initialize state
const isActive = W.initMutable(false);

// Create constructo with reactive style
function createComponent() {
  const currentStyle = W.getMutable(isActive) ? activeState : normalState;
  
  new $styledDiv({
    style: currentStyle,
    text: "Click to toggle",
    onclick: W.fx(() => {
      W.setMutableNotPersistent(isActive, !W.getMutable(isActive));
      createComponent(); // Re-render with new style
    }),
  }).create("#app", { clear: true });
}

createComponent();
```

## Advanced Patterns

### Style Composition

Combine multiple style objects (note: this requires manual merging):

```javascript
import { wstyle } from "winnetoujs/modules/style";

const baseButton = {
  padding: "12px 24px",
  border: "none",
  "border-radius": "6px",
  cursor: "pointer",
  "font-size": "16px",
  transition: "all 0.3s ease",
};

export const primaryButton = wstyle({
  ...baseButton,
  "background-color": "#3498db",
  color: "white",
});

export const outlineButton = wstyle({
  ...baseButton,
  "background-color": "transparent",
  border: "2px solid #3498db",
  color: "#3498db",
});

export const largeButton = wstyle({
  ...baseButton,
  padding: "16px 32px",
  "font-size": "18px",
  "background-color": "#2ecc71",
  color: "white",
});
```

### Conditional Styles

Apply styles based on conditions:

```javascript
import { wstyle } from "winnetoujs/modules/style";

function getButtonStyle(variant, size) {
  const baseStyle = {
    border: "none",
    "border-radius": "6px",
    cursor: "pointer",
    transition: "all 0.3s ease",
  };

  const variants = {
    primary: { "background-color": "#3498db", color: "white" },
    secondary: { "background-color": "#95a5a6", color: "white" },
    danger: { "background-color": "#e74c3c", color: "white" },
  };

  const sizes = {
    small: { padding: "8px 16px", "font-size": "14px" },
    medium: { padding: "12px 24px", "font-size": "16px" },
    large: { padding: "16px 32px", "font-size": "18px" },
  };

  return wstyle({
    ...baseStyle,
    ...variants[variant],
    ...sizes[size],
  });
}

// Usage
const primaryLarge = getButtonStyle("primary", "large");
const dangerSmall = getButtonStyle("danger", "small");
```

### Responsive Styles

While WStyle doesn't support media queries directly, you can create responsive behaviors with JavaScript:

```javascript
import { wstyle } from "winnetoujs/modules/style";

function getResponsiveCardStyle() {
  const isMobile = window.innerWidth < 768;
  
  return wstyle({
    padding: isMobile ? "12px" : "20px",
    "font-size": isMobile ? "14px" : "16px",
    width: isMobile ? "100%" : "auto",
    margin: isMobile ? "10px 0" : "10px",
  });
}

// Update on resize
window.addEventListener("resize", () => {
  const newStyle = getResponsiveCardStyle();
  // Re-render components with new style
});
```

**Note:** For complex responsive designs, use Sass/CSS with media queries instead.

## Best Practices

### 1. Keep Style Objects Focused

Create small, single-purpose style objects rather than large, monolithic ones:

✅ **Good:**
```javascript
export const flexCenter = wstyle({
  display: "flex",
  "align-items": "center",
  "justify-content": "center",
});

export const cardPadding = wstyle({
  padding: "20px",
});

export const roundedCorners = wstyle({
  "border-radius": "8px",
});
```

❌ **Avoid:**
```javascript
export const everythingStyle = wstyle({
  display: "flex",
  "align-items": "center",
  "justify-content": "center",
  padding: "20px",
  margin: "10px",
  "border-radius": "8px",
  color: "blue",
  "font-size": "16px",
  // ... too many unrelated properties
});
```

### 2. Use Descriptive Names

Name your styles based on their purpose, not their appearance:

✅ **Good:**
```javascript
export const primaryActionButton = wstyle({ /* ... */ });
export const navigationMenuItem = wstyle({ /* ... */ });
export const errorMessageText = wstyle({ /* ... */ });
```

❌ **Avoid:**
```javascript
export const blueButton = wstyle({ /* ... */ });
export const smallText = wstyle({ /* ... */ });
export const redDiv = wstyle({ /* ... */ });
```

### 3. Prefer CSS/Sass for Static Styles

Use WStyle only for dynamic or component-specific styles. For global styles, use CSS/Sass:

**Use CSS/Sass for:**
- Global resets and base styles
- Typography scales
- Color palettes
- Grid systems
- Complex animations

**Use WStyle for:**
- Component-specific styles
- Dynamic style variations
- Conditional styling
- Programmatically generated styles

### 4. Document Complex Style Objects

Add comments to explain complex or non-obvious style decisions:

```javascript
export const complexCard = wstyle({
  // Positioning
  position: "relative",
  "z-index": "10", // Above background elements
  
  // Layout
  display: "flex",
  "flex-direction": "column",
  gap: "12px",
  
  // Appearance
  "background-color": "white",
  "border-radius": "8px",
  "box-shadow": "0 2px 8px rgba(0, 0, 0, 0.1)",
  
  // Interaction
  cursor: "pointer",
  transition: "transform 0.2s ease, box-shadow 0.2s ease",
});
```

### 5. Avoid Deep Nesting in Style Objects

WStyle objects don't support nesting like Sass. Keep styles flat:

❌ **This won't work:**
```javascript
const invalid = wstyle({
  ".card": {
    padding: "20px",
    "&:hover": { // Pseudo-classes not supported
      transform: "scale(1.05)",
    },
  },
});
```

✅ **Create separate styles instead:**
```javascript
const card = wstyle({
  padding: "20px",
  transition: "transform 0.2s ease",
});

// Handle hover with JavaScript event
onclick: W.fx((self) => {
  self.style.transform = "scale(1.05)";
}, "this")
```

### 6. Monitor Bundle Size

Regularly check your bundle size when using WStyle:

```bash
# After building
ls -lh dist/js/*.js
```

If bundle size becomes a concern:
- Migrate frequently-used styles to CSS/Sass
- Remove unused style definitions
- Consider code splitting for large style collections

## Troubleshooting

### Styles Not Applying

**Issue:** WStyle object not rendering correctly.

**Solutions:**
1. Ensure the style is passed correctly to the constructo prop:
   ```javascript
   new $component({
     style: myStyle, // Not "myStyle" as string
   }).create("#app");
   ```

2. Check for CSS property name errors (use quotes for hyphenated properties):
   ```javascript
   "background-color": "#fff", // ✅ Correct
   backgroundColor: "#fff",    // ❌ Won't work
   ```

3. Verify the constructo accepts a `style` prop:
   ```html
   <winnetou>
     <div id="[[myDiv]]" style="{{style}}">Content</div>
   </winnetou>
   ```

### Property Name Issues

**Issue:** CSS properties not being applied.

**Solution:** Always use kebab-case (hyphenated) property names in quotes:

```javascript
✅ "font-size": "16px"
✅ "background-color": "#fff"
✅ "border-radius": "4px"

❌ fontSize: "16px"
❌ backgroundColor: "#fff"
❌ borderRadius: "4px"
```

### Large Bundle Size

**Issue:** Application bundle is too large due to WStyle usage.

**Solutions:**
1. Move static styles to CSS/Sass files
2. Use CSS custom properties with WStyle for dynamic values
3. Implement code splitting for style modules
4. Remove duplicate style definitions
5. Consider using utility-first CSS frameworks for common patterns

## WStyle vs CSS/Sass

### When to Use WStyle

- ✅ Small, component-specific styles
- ✅ Dynamic styles based on app state
- ✅ Programmatically generated styles
- ✅ Rapid prototyping
- ✅ Sharing styles between JavaScript modules

### When to Use CSS/Sass

- ✅ Global application styles
- ✅ Complex responsive layouts
- ✅ Animations and keyframes
- ✅ Pseudo-classes and pseudo-elements (`:hover`, `::before`, etc.)
- ✅ Media queries
- ✅ Large-scale styling needs
- ✅ Better performance (smaller bundle size)

### Hybrid Approach (Recommended)

The best approach is often a combination:

**CSS/Sass for:**
- Base styles, resets, typography
- Layout grids and responsive breakpoints
- Common component styles
- Animations

**WStyle for:**
- Dynamic variations
- Conditional styling
- Component-specific overrides
- Programmatic style generation

## Conclusion

WStyle provides a flexible, JavaScript-native way to define and apply styles in WinnetouJs applications. While it's a powerful tool for dynamic and component-specific styling, it's important to use it judiciously to avoid increasing bundle size unnecessarily.

**Key Takeaways:**

- Use `wstyle()` from `winnetoujs/modules/style` to create style objects
- Apply styles using the `style` prop in constructos
- Organize styles in dedicated files for better maintainability
- Combine with CSS variables for dynamic theming
- Monitor bundle size and migrate to CSS/Sass when appropriate
- Use WStyle for dynamic styles, CSS/Sass for static styles
- Create reusable style patterns with functions
- Document complex style objects for clarity

By following these guidelines and best practices, you can effectively leverage WStyle to create dynamic, maintainable styling solutions in your WinnetouJs applications.
