# Color Themes API

The Color Themes module enables dynamic theme switching using CSS custom properties.

## Import

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";
```

---

## Methods

### ColorThemes.applySavedTheme()

Applies the previously saved theme from localStorage at application startup.

**Signature:**

```typescript
ColorThemes.applySavedTheme(): Promise<void>
```

**Parameters:** None

**Returns:** `Promise<void>` - Resolves when theme is applied

**Usage:**

**With await:**

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";

// Apply saved theme before starting app
await ColorThemes.applySavedTheme();

function startApp() {
  console.log("App started with saved theme");
  // Your app initialization
}

startApp();
```

**With .then():**

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";

ColorThemes.applySavedTheme().then(() => {
  startApp();
});

function startApp() {
  console.log("App started with saved theme");
  // Your app initialization
}
```

**Important:** Always use `await` or `.then()` to ensure the theme is applied before rendering the app, preventing theme flashing.

---

### ColorThemes.newTheme()

Creates and applies a new theme by updating CSS custom properties.

**Signature:**

```typescript
ColorThemes.newTheme(theme: Record<string, string>): void
```

**Parameters:**

- `theme` - Object mapping CSS custom property names to values

**Returns:** `void`

**Usage:**

**Basic theme:**

```javascript
ColorThemes.newTheme({
  "--primary": "#3498db",
  "--secondary": "#2ecc71",
  "--background": "#ffffff",
  "--text": "#333333",
});
```

**Complete dark theme:**

```javascript
function applyDarkTheme() {
  ColorThemes.newTheme({
    "--primary": "#2980b9",
    "--secondary": "#27ae60",
    "--background": "#1a1a1a",
    "--surface": "#2c2c2c",
    "--text": "#ffffff",
    "--text-secondary": "#cccccc",
    "--border": "#444444",
    "--shadow": "rgba(0, 0, 0, 0.3)",
    "--button-bg": "#2980b9",
    "--button-text": "#ffffff",
    "--card-bg": "#2c2c2c",
    "--header-bg": "#222222",
  });
}

applyDarkTheme();
```

**Features:**

- Immediately applies theme to the page
- Automatically saves theme to localStorage
- Updates all elements using the CSS custom properties

---

## Setup Requirements

### CSS Custom Properties

Define CSS custom properties in `:root` selector:

**CSS:**

```css
:root {
  /* Colors */
  --primary: #3498db;
  --secondary: #2ecc71;
  --background: #ffffff;
  --text: #333333;
  --border: #dddddd;
}

/* Use variables in styles */
body {
  background: var(--background);
  color: var(--text);
}

button {
  background: var(--primary);
  border: 1px solid var(--border);
}
```

**SCSS:**

```scss
:root {
  --primary: #3498db;
  --secondary: #2ecc71;
  --background: #ffffff;
  --text: #333333;
}

body {
  background: var(--background);
  color: var(--text);
}
```

---

## Best Practices

### Theme Organization

**Create a theme manager class:**

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";

export class ThemeManager {
  static lightTheme() {
    ColorThemes.newTheme({
      "--primary": "#3498db",
      "--secondary": "#2ecc71",
      "--background": "#ffffff",
      "--surface": "#f5f5f5",
      "--text": "#333333",
      "--text-secondary": "#666666",
      "--border": "#dddddd",
      "--shadow": "rgba(0, 0, 0, 0.1)",
    });
  }

  static darkTheme() {
    ColorThemes.newTheme({
      "--primary": "#2980b9",
      "--secondary": "#27ae60",
      "--background": "#1a1a1a",
      "--surface": "#2c2c2c",
      "--text": "#ffffff",
      "--text-secondary": "#cccccc",
      "--border": "#444444",
      "--shadow": "rgba(0, 0, 0, 0.3)",
    });
  }

  static blueTheme() {
    ColorThemes.newTheme({
      "--primary": "#1e3a8a",
      "--secondary": "#3b82f6",
      "--background": "#eff6ff",
      "--surface": "#dbeafe",
      "--text": "#1e293b",
      "--text-secondary": "#64748b",
      "--border": "#bfdbfe",
      "--shadow": "rgba(30, 58, 138, 0.1)",
    });
  }

  static getCurrentTheme() {
    return getComputedStyle(document.documentElement)
      .getPropertyValue("--background")
      .trim();
  }

  static toggleTheme() {
    const currentBg = this.getCurrentTheme();
    if (currentBg === "#ffffff") {
      this.darkTheme();
    } else {
      this.lightTheme();
    }
  }
}
```

### Consistent Variable Names

**Use a consistent naming convention:**

```css
:root {
  /* Base colors */
  --primary: #3498db;
  --secondary: #2ecc71;
  --accent: #e74c3c;

  /* Surfaces */
  --background: #ffffff;
  --surface: #f5f5f5;
  --overlay: rgba(0, 0, 0, 0.5);

  /* Text */
  --text-primary: #333333;
  --text-secondary: #666666;
  --text-disabled: #999999;

  /* Borders & dividers */
  --border: #dddddd;
  --divider: #eeeeee;

  /* Shadows */
  --shadow-sm: rgba(0, 0, 0, 0.05);
  --shadow-md: rgba(0, 0, 0, 0.1);
  --shadow-lg: rgba(0, 0, 0, 0.2);

  /* Status colors */
  --success: #2ecc71;
  --warning: #f39c12;
  --error: #e74c3c;
  --info: #3498db;
}
```

### Theme Detection

**Detect and apply system theme:**

```javascript
export class ThemeManager {
  static applySystemTheme() {
    const isDark = window.matchMedia("(prefers-color-scheme: dark)").matches;

    if (isDark) {
      this.darkTheme();
    } else {
      this.lightTheme();
    }
  }

  static watchSystemTheme() {
    window
      .matchMedia("(prefers-color-scheme: dark)")
      .addEventListener("change", e => {
        if (e.matches) {
          this.darkTheme();
        } else {
          this.lightTheme();
        }
      });
  }
}

// Auto-apply system theme
ThemeManager.applySystemTheme();
ThemeManager.watchSystemTheme();
```

---

## Common Patterns

### Theme Toggle Button

```javascript
import { W } from "winnetoujs";
import { createElement, Sun, Moon } from "lucide";
import { $themeToggle } from "./components.wcto";
import { ThemeManager } from "./themes";

class Header {
  constructor() {
    this.renderThemeToggle();
  }

  renderThemeToggle() {
    const isDark = ThemeManager.getCurrentTheme() === "#1a1a1a";

    new $themeToggle({
      icon: createElement(isDark ? Sun : Moon, { size: 20 }).outerHTML,
      onclick: W.fx(() => {
        ThemeManager.toggleTheme();
        this.updateToggleIcon();
      }),
    }).create("#header");
  }

  updateToggleIcon() {
    // Re-render toggle with new icon
    const toggle = document.querySelector("#header .theme-toggle");
    toggle.remove();
    this.renderThemeToggle();
  }
}
```

### Theme Selector Dropdown

```javascript
import { W } from "winnetoujs";
import { $themeSelector } from "./components.wcto";
import { ThemeManager } from "./themes";

new $themeSelector({
  options: [
    { value: "light", label: "Light" },
    { value: "dark", label: "Dark" },
    { value: "blue", label: "Blue" },
    { value: "system", label: "System" },
  ],
  onchange: W.fx(select => {
    const theme = select.value;

    switch (theme) {
      case "light":
        ThemeManager.lightTheme();
        break;
      case "dark":
        ThemeManager.darkTheme();
        break;
      case "blue":
        ThemeManager.blueTheme();
        break;
      case "system":
        ThemeManager.applySystemTheme();
        break;
    }
  }, "this"),
}).create("#settings");
```

### Persistent Theme with User Preference

```javascript
import { W } from "winnetoujs";
import { ColorThemes } from "winnetoujs/modules/colorThemes";

export class ThemeManager {
  static init() {
    // Apply saved theme on startup
    ColorThemes.applySavedTheme().then(() => {
      // Check user preference
      const userPreference = W.getMutable("themePreference");

      if (userPreference === "system") {
        this.applySystemTheme();
        this.watchSystemTheme();
      }
    });
  }

  static setUserPreference(preference) {
    W.setMutable("themePreference", preference);

    if (preference === "system") {
      this.applySystemTheme();
    }
  }

  static lightTheme() {
    W.setMutable("themePreference", "light");
    ColorThemes.newTheme({
      "--primary": "#3498db",
      "--background": "#ffffff",
      "--text": "#333333",
    });
  }

  static darkTheme() {
    W.setMutable("themePreference", "dark");
    ColorThemes.newTheme({
      "--primary": "#2980b9",
      "--background": "#1a1a1a",
      "--text": "#ffffff",
    });
  }
}

// Initialize on app start
ThemeManager.init();
```

### Theme Transition Animation

```css
/* Add transition for smooth theme changes */
:root {
  --primary: #3498db;
  --background: #ffffff;
  --text: #333333;
}

* {
  transition: background-color 0.3s ease, color 0.3s ease,
    border-color 0.3s ease;
}

/* Disable transitions on theme change for instant update */
.theme-changing * {
  transition: none !important;
}
```

```javascript
export class ThemeManager {
  static applyTheme(themeFunction) {
    // Add class to disable transitions
    document.body.classList.add("theme-changing");

    // Apply theme
    themeFunction();

    // Remove class after brief delay
    setTimeout(() => {
      document.body.classList.remove("theme-changing");
    }, 50);
  }

  static lightTheme() {
    this.applyTheme(() => {
      ColorThemes.newTheme({
        "--primary": "#3498db",
        "--background": "#ffffff",
        "--text": "#333333",
      });
    });
  }
}
```

### Multi-Theme Support

```javascript
export class ThemeManager {
  static themes = {
    light: {
      "--primary": "#3498db",
      "--background": "#ffffff",
      "--text": "#333333",
    },
    dark: {
      "--primary": "#2980b9",
      "--background": "#1a1a1a",
      "--text": "#ffffff",
    },
    ocean: {
      "--primary": "#0077be",
      "--background": "#e0f2f7",
      "--text": "#01579b",
    },
    sunset: {
      "--primary": "#ff6b6b",
      "--background": "#fff5f5",
      "--text": "#c92a2a",
    },
  };

  static applyTheme(themeName) {
    const theme = this.themes[themeName];
    if (theme) {
      ColorThemes.newTheme(theme);
      W.setMutable("currentTheme", themeName);
    }
  }

  static getCurrentTheme() {
    return W.getMutable("currentTheme") || "light";
  }

  static listThemes() {
    return Object.keys(this.themes);
  }
}

// Usage
ThemeManager.applyTheme("ocean");
console.log(ThemeManager.getCurrentTheme()); // "ocean"
console.log(ThemeManager.listThemes()); // ["light", "dark", "ocean", "sunset"]
```
