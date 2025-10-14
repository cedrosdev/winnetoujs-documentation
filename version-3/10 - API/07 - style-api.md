# Style API

The Style module provides JavaScript-based styling functionality through the `wstyle()` function.

## Import

```javascript
import { wstyle } from "winnetoujs/modules/style";
```

---

## Functions

### wstyle()

Creates a CSS style object that can be applied to constructos.

**Signature:**

```typescript
wstyle(styles: Record<string, string>): string
```

**Parameters:**

- `styles` - Object with CSS property-value pairs
  - Properties with hyphens must be quoted (e.g., `"background-color"`)
  - Camel case properties are not supported
  - Values are strings

**Returns:** `string` - Encoded style string for constructo

**Usage:**

**Basic styles:**

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

**Applying to constructo:**

```javascript
import { $styledButton } from "./buttons.wcto";
import { buttonStyle } from "./styles";

new $styledButton({
  text: "Click Me",
  style: buttonStyle,
}).create("#app");
```

**Features:**

- Define styles as JavaScript objects
- Reusable style definitions
- Export and import across files
- Full IDE support with autocomplete
- No separate CSS compilation needed

**Important:** WStyle increases bundle size since styles are included in JavaScript. Use wisely.

---

## Best Practices

### When to Use WStyle

**✅ Use WStyle for:**

- Dynamic styles that change based on state
- Small, component-specific styles
- Prototyping and rapid development
- Styles shared across multiple constructos
- Theme variations conditionally applied

**❌ Avoid WStyle for:**

- Large-scale application styling
- Complex responsive layouts (use Sass with media queries)
- Animations and keyframes (use CSS/Sass)
- Global styles and resets (use CSS/Sass)

### Style Organization

**Create dedicated style files:**

```javascript
// styles/buttonStyles.js
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

**Co-locate styles with constructos:**

```
src/
└── components/
    └── menu/
        ├── menu.wcto.html
        ├── menu.ts
        └── menuStyles.js
```

### Property Naming

**Use quoted property names for hyphens:**

```javascript
// ✅ Correct
const style = wstyle({
  "background-color": "#fff",
  "border-radius": "4px",
  "font-size": "16px",
});

// ❌ Wrong - will not work
const style = wstyle({
  backgroundColor: "#fff", // Camel case not supported
  borderRadius: "4px",
  fontSize: "16px",
});
```

---

## Common Patterns

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
  "justify-content": "space-between",
  "align-items": "center",
});

export const gridContainer = wstyle({
  display: "grid",
  "grid-template-columns": "repeat(auto-fit, minmax(250px, 1fr))",
  gap: "20px",
});
```

**Usage:**

```javascript
new $container({
  style: flexCenter,
  content: "Centered content",
}).create("#app");
```

### Card Styles

```javascript
export const card = wstyle({
  "background-color": "#ffffff",
  border: "1px solid #e0e0e0",
  "border-radius": "8px",
  padding: "20px",
  "box-shadow": "0 2px 4px rgba(0, 0, 0, 0.1)",
  transition: "box-shadow 0.3s ease",
});

export const cardHeader = wstyle({
  "font-size": "20px",
  "font-weight": "600",
  "margin-bottom": "12px",
  color: "#333333",
});

export const cardBody = wstyle({
  "font-size": "14px",
  color: "#666666",
  "line-height": "1.6",
});
```

### Form Styles

```javascript
export const inputField = wstyle({
  width: "100%",
  padding: "10px 12px",
  border: "1px solid #ddd",
  "border-radius": "4px",
  "font-size": "14px",
  outline: "none",
  transition: "border-color 0.3s ease",
});

export const label = wstyle({
  display: "block",
  "margin-bottom": "6px",
  "font-size": "14px",
  "font-weight": "500",
  color: "#333",
});

export const errorMessage = wstyle({
  color: "#e74c3c",
  "font-size": "12px",
  "margin-top": "4px",
});
```

### Dynamic Styles Based on State

```javascript
import { W } from "winnetoujs";
import { wstyle } from "winnetoujs/modules/style";

const isActive = W.getMutable("buttonActive") === "true";

const dynamicButtonStyle = wstyle({
  padding: "10px 20px",
  "background-color": isActive ? "#2ecc71" : "#95a5a6",
  color: "white",
  border: "none",
  "border-radius": "4px",
  cursor: "pointer",
});

new $button({
  style: dynamicButtonStyle,
  text: isActive ? "Active" : "Inactive",
}).create("#app");
```

### Style Composition

```javascript
// Base styles
const baseButton = {
  padding: "10px 20px",
  border: "none",
  "border-radius": "4px",
  cursor: "pointer",
  "font-size": "16px",
  transition: "all 0.3s ease",
};

// Variant styles
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

export const ghostButton = wstyle({
  ...baseButton,
  "background-color": "transparent",
  border: "none",
  color: "#3498db",
});
```

### Responsive Styles with CSS Variables

```javascript
// Combine WStyle with CSS variables for responsiveness
export const responsiveCard = wstyle({
  padding: "var(--card-padding, 20px)",
  "border-radius": "var(--card-radius, 8px)",
  "background-color": "var(--card-bg, #ffffff)",
  width: "var(--card-width, 100%)",
});
```

```css
/* In your CSS file */
:root {
  --card-padding: 20px;
  --card-radius: 8px;
  --card-bg: #ffffff;
  --card-width: 100%;
}

@media (max-width: 768px) {
  :root {
    --card-padding: 12px;
    --card-radius: 4px;
    --card-width: 100%;
  }
}
```

### Theme-Aware Styles

```javascript
import { wstyle } from "winnetoujs/modules/style";

// Using CSS custom properties for theming
export const themedCard = wstyle({
  "background-color": "var(--surface)",
  color: "var(--text-primary)",
  border: "1px solid var(--border)",
  "border-radius": "8px",
  padding: "20px",
  "box-shadow": "0 2px 4px var(--shadow-sm)",
});

export const themedButton = wstyle({
  "background-color": "var(--primary)",
  color: "white",
  border: "none",
  "border-radius": "4px",
  padding: "10px 20px",
  cursor: "pointer",
});
```

### Conditional Styling

```javascript
import { W } from "winnetoujs";
import { wstyle } from "winnetoujs/modules/style";

function getStatusStyle(status) {
  const baseStyle = {
    padding: "6px 12px",
    "border-radius": "4px",
    "font-size": "14px",
    "font-weight": "500",
  };

  switch (status) {
    case "success":
      return wstyle({
        ...baseStyle,
        "background-color": "#d4edda",
        color: "#155724",
      });
    case "error":
      return wstyle({
        ...baseStyle,
        "background-color": "#f8d7da",
        color: "#721c24",
      });
    case "warning":
      return wstyle({
        ...baseStyle,
        "background-color": "#fff3cd",
        color: "#856404",
      });
    default:
      return wstyle({
        ...baseStyle,
        "background-color": "#d1ecf1",
        color: "#0c5460",
      });
  }
}

// Usage
new $statusBadge({
  text: "Success",
  style: getStatusStyle("success"),
}).create("#app");
```

### Animation Support

```javascript
// WStyle for basic properties, CSS for animations
export const animatedButton = wstyle({
  padding: "10px 20px",
  "background-color": "#3498db",
  color: "white",
  border: "none",
  "border-radius": "4px",
  cursor: "pointer",
  animation: "pulse 2s infinite", // Reference CSS animation
});
```

```css
/* Define animation in CSS */
@keyframes pulse {
  0%,
  100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.05);
  }
}
```

### Utility Styles

```javascript
export const textStyles = {
  heading1: wstyle({
    "font-size": "32px",
    "font-weight": "700",
    "line-height": "1.2",
    color: "#333",
  }),
  heading2: wstyle({
    "font-size": "24px",
    "font-weight": "600",
    "line-height": "1.3",
    color: "#333",
  }),
  body: wstyle({
    "font-size": "16px",
    "font-weight": "400",
    "line-height": "1.6",
    color: "#666",
  }),
  caption: wstyle({
    "font-size": "12px",
    "font-weight": "400",
    color: "#999",
  }),
};

export const spacingStyles = {
  marginSm: wstyle({ margin: "8px" }),
  marginMd: wstyle({ margin: "16px" }),
  marginLg: wstyle({ margin: "24px" }),
  paddingSm: wstyle({ padding: "8px" }),
  paddingMd: wstyle({ padding: "16px" }),
  paddingLg: wstyle({ padding: "24px" }),
};
```

---

## Performance Considerations

### Bundle Size Impact

**WStyle adds to bundle size:**

```javascript
// Each style definition is included in bundle
export const button1 = wstyle({
  /* 10 properties */
}); // ~200 bytes
export const button2 = wstyle({
  /* 10 properties */
}); // ~200 bytes
export const button3 = wstyle({
  /* 10 properties */
}); // ~200 bytes
// Total: ~600 bytes just for styles

// Compare to CSS (not in bundle)
// 600 bytes in separate CSS file = 0 bytes in JS bundle
```

**Best practice: Use WStyle sparingly**

```javascript
// ✅ Good - Few dynamic styles
const dynamicStyle = wstyle({
  "background-color": userColor,
  color: textColor,
});

// ❌ Avoid - Large static styles (use CSS instead)
const massiveStyle = wstyle({
  // 50 properties...
});
```

### When to Use CSS Instead

**Use CSS/Sass for:**

- Static styles that don't change
- Complex responsive layouts
- Global styling
- Animations and keyframes
- Large style sheets

**Use WStyle for:**

- Truly dynamic styles based on runtime data
- Small, component-specific variations
- Conditional styling
- Theme-specific overrides

---

## Migration from WStyle to CSS

**If your WStyle bundle gets too large:**

1. **Extract static styles to CSS:**

```javascript
// Before (in JS)
export const cardStyle = wstyle({
  padding: "20px",
  "border-radius": "8px",
  // ... 20 more properties
});

// After (in CSS)
.card {
  padding: 20px;
  border-radius: 8px;
  /* ... 20 more properties */
}
```

2. **Use class names in constructos:**

```html
<!-- Before -->
<div id="[[card]]" style="{{style}}">{{content}}</div>

<!-- After -->
<div id="[[card]]" class="card">{{content}}</div>
```

3. **Keep only dynamic styles in WStyle:**

```javascript
// Only styles that change at runtime
export const dynamicOverlay = wstyle({
  "background-color": userPreferenceColor,
  opacity: visibilityLevel,
});
```
