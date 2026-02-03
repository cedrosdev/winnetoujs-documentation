# Color Themes: Dynamic Theme Switching in WinnetouJs

## Overview

The Color Themes module in WinnetouJs provides a powerful system for implementing dynamic theme switching in your applications. It allows users to toggle between different color schemes (like light/dark mode) seamlessly, with automatic persistence in local storage.

**Key Features:**

- 🎨 Dynamic theme switching without page reload
- 💾 Automatic theme persistence in localStorage
- 🔄 CSS custom properties (variables) based
- ⚡ Instant theme application
- 🎯 Simple API with minimal setup
- 🌓 Perfect for light/dark mode implementations
- 📦 Part of WinnetouJs modules (tree-shakable)

## How It Works

The Color Themes module works by manipulating CSS custom properties (CSS variables) defined in your stylesheets. When you switch themes, it updates these variables dynamically, causing your entire application to re-style instantly.

## Installation

The Color Themes module is included with WinnetouJs. Simply import it:

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";
```

## Setup

### 1. Define CSS Custom Properties

First, define your color palette using CSS custom properties in the `:root` selector:

**`styles/main.css`**

```css
:root {
  /* Colors */
  --primary: #3498db;
  --secondary: #2ecc71;
  --background: #ffffff;
  --surface: #f5f5f5;
  --text: #333333;
  --text-secondary: #666666;
  --border: #dddddd;
  --shadow: rgba(0, 0, 0, 0.1);

  /* Component colors */
  --button-bg: var(--primary);
  --button-text: #ffffff;
  --card-bg: #ffffff;
  --header-bg: var(--surface);
}

/* Use variables in your styles */
body {
  background: var(--background);
  color: var(--text);
  font-family:
    -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
}

.card {
  background: var(--card-bg);
  border: 1px solid var(--border);
  box-shadow: 0 2px 4px var(--shadow);
}

button {
  background: var(--button-bg);
  color: var(--button-text);
}
```

**Or with Sass:**

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

### 2. Apply Saved Theme at Startup

Call `applySavedTheme()` when your application starts to restore the user's previously selected theme.

**`app.ts`**

```javascript
import { W } from "winnetoujs";
import { ColorThemes } from "winnetoujs/modules/colorThemes";

// Apply saved theme at startup
ColorThemes.applySavedTheme();

// Then start your app
async function startApp() {
  // Your app initialization code
  console.log("App started with saved theme");
}

startApp();
```

## Creating Themes

### Basic Theme Creation

Use the `newTheme()` method to create and apply a new theme:

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";

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

// Apply dark theme
applyDarkTheme();
```

**Important:** The `newTheme()` method:

- Immediately applies the theme to the page
- Saves the theme to localStorage automatically
- Overrides all specified CSS custom properties

## Practical Examples

### Example 1: Light/Dark Mode Toggle

**`themes.ts`**

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
      "--card-bg": "#ffffff",
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
      "--card-bg": "#2c2c2c",
    });
  }

  static toggleTheme() {
    const currentBg = getComputedStyle(document.documentElement)
      .getPropertyValue("--background")
      .trim();

    if (currentBg === "#ffffff") {
      this.darkTheme();
    } else {
      this.lightTheme();
    }
  }
}
```

**`themeToggle.wcto.html`**

```html
<winnetou description="Theme toggle button">
  <button id="[[themeToggle]]" class="theme-toggle" onclick="{{onclick}}">
    <span>{{icon}}</span>
    <span>{{label}}</span>
  </button>
</winnetou>
```

**`header.ts`**

```javascript
import { W } from "winnetoujs";
import { createElement, Sun, Moon } from "lucide";
import { $themeToggle } from "./themeToggle.wcto";
import { ThemeManager } from "./themes";

class Header {
  constructor() {
    this.renderThemeToggle();
  }

  renderThemeToggle() {
    new $themeToggle({
      icon: createElement(Moon, { size: 20 }).outerHTML,
      label: "Toggle Theme",
      onclick: W.fx(() => {
        ThemeManager.toggleTheme();
        this.updateToggleIcon();
      }),
    }).create("#header");
  }

  updateToggleIcon() {
    const currentBg = getComputedStyle(document.documentElement)
      .getPropertyValue("--background")
      .trim();

    const isDark = currentBg === "#1a1a1a";
    const icon = isDark
      ? createElement(Sun, { size: 20 }).outerHTML
      : createElement(Moon, { size: 20 }).outerHTML;

    // Re-render toggle button with new icon
    this.renderThemeToggle();
  }
}

export default Header;
```

### Example 2: Multiple Theme Options

**`themes.ts`**

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";

export const themes = {
  light: {
    name: "Light",
    colors: {
      "--primary": "#3498db",
      "--secondary": "#2ecc71",
      "--background": "#ffffff",
      "--surface": "#f5f5f5",
      "--text": "#333333",
      "--border": "#dddddd",
    },
  },

  dark: {
    name: "Dark",
    colors: {
      "--primary": "#2980b9",
      "--secondary": "#27ae60",
      "--background": "#1a1a1a",
      "--surface": "#2c2c2c",
      "--text": "#ffffff",
      "--border": "#444444",
    },
  },

  ocean: {
    name: "Ocean",
    colors: {
      "--primary": "#006994",
      "--secondary": "#4ecdc4",
      "--background": "#e8f4f8",
      "--surface": "#d4edf4",
      "--text": "#003d5c",
      "--border": "#b8dfe8",
    },
  },

  sunset: {
    name: "Sunset",
    colors: {
      "--primary": "#ff6b6b",
      "--secondary": "#f9ca24",
      "--background": "#fff5e6",
      "--surface": "#ffe8cc",
      "--text": "#4a4a4a",
      "--border": "#ffd9a8",
    },
  },

  forest: {
    name: "Forest",
    colors: {
      "--primary": "#27ae60",
      "--secondary": "#f39c12",
      "--background": "#f0f7f0",
      "--surface": "#e1f0e1",
      "--text": "#1e5128",
      "--border": "#c8e6c9",
    },
  },
};

export function applyTheme(themeName) {
  const theme = themes[themeName];
  if (theme) {
    ColorThemes.newTheme(theme.colors);
    return true;
  }
  return false;
}

export function getCurrentTheme() {
  const currentPrimary = getComputedStyle(document.documentElement)
    .getPropertyValue("--primary")
    .trim();

  for (const [key, theme] of Object.entries(themes)) {
    if (theme.colors["--primary"] === currentPrimary) {
      return key;
    }
  }
  return "light"; // default
}
```

**`themeSelector.wcto.html`**

```html
<winnetou description="Theme selector dropdown">
  <div id="[[themeSelector]]" class="theme-selector">
    <label for="theme-dropdown">Choose Theme:</label>
    <select id="theme-dropdown" onchange="{{onchange}}">
      {{options}}
    </select>
  </div>
</winnetou>

<winnetou description="Theme option">
  <option id="[[themeOption]]" value="{{value}}" selected="{{selected}}">
    {{label}}
  </option>
</winnetou>
```

**`settings.ts`**

```javascript
import { W } from "winnetoujs";
import { $themeSelector, $themeOption } from "./themeSelector.wcto";
import { themes, applyTheme, getCurrentTheme } from "./themes";

class Settings {
  constructor() {
    this.renderThemeSelector();
  }

  renderThemeSelector() {
    const currentTheme = getCurrentTheme();

    // Create options HTML
    const optionsHTML = Object.keys(themes)
      .map((key) => {
        return new $themeOption({
          value: key,
          label: themes[key].name,
          selected: key === currentTheme ? "selected" : "",
        }).constructoString();
      })
      .join("");

    new $themeSelector({
      options: optionsHTML,
      onchange: W.fx((self) => {
        applyTheme(self.value);
      }, "this"),
    }).create("#settings");
  }
}

export default Settings;
```

### Example 3: Theme with System Preference Detection

**`themes.ts`**

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";

export class ThemeManager {
  static lightTheme() {
    ColorThemes.newTheme({
      "--primary": "#3498db",
      "--background": "#ffffff",
      "--text": "#333333",
      // ... other light theme colors
    });
  }

  static darkTheme() {
    ColorThemes.newTheme({
      "--primary": "#2980b9",
      "--background": "#1a1a1a",
      "--text": "#ffffff",
      // ... other dark theme colors
    });
  }

  static applySystemTheme() {
    const prefersDark = window.matchMedia(
      "(prefers-color-scheme: dark)",
    ).matches;

    if (prefersDark) {
      this.darkTheme();
    } else {
      this.lightTheme();
    }
  }

  static watchSystemTheme() {
    const mediaQuery = window.matchMedia("(prefers-color-scheme: dark)");

    mediaQuery.addEventListener("change", (e) => {
      if (e.matches) {
        this.darkTheme();
      } else {
        this.lightTheme();
      }
    });
  }
}

// Apply system theme on startup
ThemeManager.applySystemTheme();

// Watch for system theme changes
ThemeManager.watchSystemTheme();
```

### Example 4: Smooth Theme Transitions

Add CSS transitions for smooth color changes:

**`styles/main.css`**

```css
:root {
  --primary: #3498db;
  --background: #ffffff;
  --text: #333333;
  /* ... other variables ... */
}

/* Add transitions to elements */
* {
  transition:
    background-color 0.3s ease,
    color 0.3s ease,
    border-color 0.3s ease,
    box-shadow 0.3s ease;
}

/* Disable transitions on theme switch for instant change (optional) */
body.theme-switching * {
  transition: none !important;
}
```

**`themes.ts`**

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";

export function applyThemeWithTransition(themeColors) {
  // Add class to body
  document.body.classList.add("theme-switching");

  // Apply theme
  ColorThemes.newTheme(themeColors);

  // Remove class after a short delay
  setTimeout(() => {
    document.body.classList.remove("theme-switching");
  }, 50);
}
```

## Integration with WStyle

Combine Color Themes with WStyle for maximum flexibility:

**`styles/theme.css`**

```css
:root {
  --button-bg: #3498db;
  --button-hover-bg: #2980b9;
  --button-text: #ffffff;
}
```

**`styles/buttonStyles.js`**

```javascript
import { wstyle } from "winnetoujs/modules/style";

export const themedButton = wstyle({
  padding: "12px 24px",
  "background-color": "var(--button-bg)",
  color: "var(--button-text)",
  border: "none",
  "border-radius": "6px",
  cursor: "pointer",
  transition: "background-color 0.3s ease",
});
```

Now when themes change, WStyle buttons automatically update!

## Advanced Patterns

### Theme Presets with Inheritance

Create theme presets that inherit from a base theme:

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";

const baseTheme = {
  "--font-family":
    "-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif",
  "--border-radius": "8px",
  "--spacing": "16px",
  "--transition": "0.3s ease",
};

const lightColors = {
  "--primary": "#3498db",
  "--background": "#ffffff",
  "--text": "#333333",
};

const darkColors = {
  "--primary": "#2980b9",
  "--background": "#1a1a1a",
  "--text": "#ffffff",
};

export function applyLightTheme() {
  ColorThemes.newTheme({
    ...baseTheme,
    ...lightColors,
  });
}

export function applyDarkTheme() {
  ColorThemes.newTheme({
    ...baseTheme,
    ...darkColors,
  });
}
```

### Per-Component Theme Overrides

Create component-specific theme variations:

**`themes/componentThemes.ts`**

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";

export function applyThemeWithAccent(baseTheme, accentColor) {
  ColorThemes.newTheme({
    ...baseTheme,
    "--accent": accentColor,
    "--button-bg": accentColor,
    "--link-color": accentColor,
  });
}

// Usage
const darkThemeBlueAccent = {
  "--background": "#1a1a1a",
  "--text": "#ffffff",
};

applyThemeWithAccent(darkThemeBlueAccent, "#3498db");
```

### Dynamic Theme Generation

Generate themes programmatically:

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";

function hexToRgb(hex) {
  const result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
  return result
    ? {
        r: parseInt(result[1], 16),
        g: parseInt(result[2], 16),
        b: parseInt(result[3], 16),
      }
    : null;
}

function lighten(color, percent) {
  const rgb = hexToRgb(color);
  const amt = Math.round(2.55 * percent);
  return `#${[rgb.r, rgb.g, rgb.b]
    .map((c) =>
      Math.min(255, c + amt)
        .toString(16)
        .padStart(2, "0"),
    )
    .join("")}`;
}

function darken(color, percent) {
  const rgb = hexToRgb(color);
  const amt = Math.round(2.55 * percent);
  return `#${[rgb.r, rgb.g, rgb.b]
    .map((c) =>
      Math.max(0, c - amt)
        .toString(16)
        .padStart(2, "0"),
    )
    .join("")}`;
}

export function generateTheme(primaryColor, isDark = false) {
  const bgColor = isDark ? "#1a1a1a" : "#ffffff";
  const textColor = isDark ? "#ffffff" : "#333333";

  ColorThemes.newTheme({
    "--primary": primaryColor,
    "--primary-light": lighten(primaryColor, 20),
    "--primary-dark": darken(primaryColor, 20),
    "--background": bgColor,
    "--text": textColor,
    "--surface": isDark ? lighten(bgColor, 10) : darken(bgColor, 5),
  });
}

// Generate custom theme
generateTheme("#e74c3c", false); // Light theme with red primary
generateTheme("#9b59b6", true); // Dark theme with purple primary
```

## Best Practices

### 1. Define All Variables Upfront

Create a comprehensive set of CSS variables for consistency:

```css
:root {
  /* Base colors */
  --primary: #3498db;
  --secondary: #2ecc71;
  --success: #27ae60;
  --warning: #f39c12;
  --danger: #e74c3c;
  --info: #3498db;

  /* Neutral colors */
  --background: #ffffff;
  --surface: #f5f5f5;
  --text: #333333;
  --text-secondary: #666666;
  --text-muted: #999999;
  --border: #dddddd;
  --shadow: rgba(0, 0, 0, 0.1);

  /* Component-specific */
  --header-bg: var(--surface);
  --card-bg: #ffffff;
  --button-bg: var(--primary);
  --input-bg: #ffffff;

  /* Spacing */
  --spacing-xs: 4px;
  --spacing-sm: 8px;
  --spacing-md: 16px;
  --spacing-lg: 24px;
  --spacing-xl: 32px;
}
```

### 2. Use Semantic Variable Names

Name variables based on purpose, not appearance:

✅ **Good:**

```css
:root {
  --background-primary: #ffffff;
  --text-primary: #333333;
  --button-primary-bg: #3498db;
}
```

❌ **Avoid:**

```css
:root {
  --white: #ffffff;
  --dark-gray: #333333;
  --blue: #3498db;
}
```

### 3. Maintain Consistent Contrast

Ensure text remains readable in all themes:

```javascript
// Always test contrast ratios
const lightTheme = {
  "--background": "#ffffff",
  "--text": "#333333", // Good contrast: 12.63:1
};

const darkTheme = {
  "--background": "#1a1a1a",
  "--text": "#ffffff", // Good contrast: 15.84:1
};
```

### 4. Include Fallback Values

Provide fallback values for browsers without CSS variable support:

```css
.button {
  background-color: #3498db; /* Fallback */
  background-color: var(--primary, #3498db);
}
```

### 5. Document Your Themes

Create clear documentation for all available themes:

```javascript
/**
 * Available themes:
 * - light: Default light theme with blue accents
 * - dark: Dark theme with reduced blue saturation
 * - ocean: Light theme with blue/teal palette
 * - sunset: Warm theme with orange/yellow accents
 * - forest: Green theme for nature-focused apps
 */
export const themes = {
  // ...
};
```

### 6. Test Accessibility

Always test themes for accessibility compliance:

- Minimum contrast ratio: 4.5:1 for normal text
- Minimum contrast ratio: 3:1 for large text
- Test with screen readers
- Verify focus indicators are visible

## Troubleshooting

### Theme Not Persisting

**Issue:** Theme resets on page reload.

**Solution:** Ensure `applySavedTheme()` is called at app startup and properly awaited:

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";

// Call this BEFORE rendering components (using await)
await ColorThemes.applySavedTheme();

// Then start app
startApp();
```

**Or using .then():**

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";

// Call this BEFORE rendering components (using .then)
ColorThemes.applySavedTheme().then(() => {
  startApp();
});
```

### Theme Not Applying

**Issue:** Colors don't change when switching themes.

**Solutions:**

1. Verify CSS variables are defined in `:root`:

   ```css
   :root {
     --primary: #3498db; /* ✅ */
   }

   body {
     --primary: #3498db; /* ❌ Won't work with ColorThemes */
   }
   ```

2. Check variable names match exactly (including `--` prefix):

   ```javascript
   ColorThemes.newTheme({
     "--primary": "#000", // ✅ Correct
     primary: "#000", // ❌ Missing --
   });
   ```

3. Ensure variables are used in CSS:
   ```css
   .button {
     background: var(--primary); /* ✅ */
     background: #3498db; /* ❌ Won't change */
   }
   ```

### Partial Theme Application

**Issue:** Some elements don't update when theme changes.

**Solution:** Check if all relevant CSS properties use variables:

```css
/* ❌ Incomplete */
.card {
  background: var(--card-bg);
  color: #333; /* Hard-coded color won't change */
}

/* ✅ Complete */
.card {
  background: var(--card-bg);
  color: var(--text);
}
```

### Theme Flashing on Load

**Issue:** Wrong theme briefly shows before correct theme applies.

**Solution:** Apply theme as early as possible and await it before rendering:

```javascript
// At the very top of your app entry point
import { ColorThemes } from "winnetoujs/modules/colorThemes";

// Await theme application before starting app
await ColorThemes.applySavedTheme();

// Then import and start app
import { startApp } from "./app";
startApp();
```

**Or using .then():**

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";

ColorThemes.applySavedTheme().then(async () => {
  const { startApp } = await import("./app");
  startApp();
});
```

Or use inline script in HTML:

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      /* Prevent flash of unstyled content */
      body {
        opacity: 0;
        transition: opacity 0.3s;
      }
      body.loaded {
        opacity: 1;
      }
    </style>
  </head>
  <body>
    <div id="app"></div>
    <script type="module">
      import { ColorThemes } from "winnetoujs/modules/colorThemes";

      // Await theme before showing content
      await ColorThemes.applySavedTheme();
      document.body.classList.add("loaded");
    </script>
    <script type="module" src="/dist/app.js"></script>
  </body>
</html>
```

## API Reference

### `ColorThemes.applySavedTheme()`

Applies the previously saved theme from localStorage. **Returns a Promise** that resolves when the theme is applied.

**Usage:**

```javascript
// Using await
await ColorThemes.applySavedTheme();

// Or using .then()
ColorThemes.applySavedTheme().then(() => {
  console.log("Theme applied");
});
```

**Returns:** `Promise<void>`

**When to use:** At application startup, before rendering components. Always await or use `.then()` to ensure the theme is applied before your app renders.

### `ColorThemes.newTheme(themeObject)`

Creates and applies a new theme, saving it to localStorage.

**Parameters:**

- `themeObject` (Object): Key-value pairs of CSS variable names and values

**Usage:**

```javascript
ColorThemes.newTheme({
  "--primary": "#3498db",
  "--background": "#ffffff",
  "--text": "#333333",
});
```

**Returns:** void

**Side effects:**

- Updates CSS custom properties immediately
- Saves theme to localStorage
- Triggers re-render of themed elements

## Conclusion

The Color Themes module in WinnetouJs provides a simple yet powerful way to implement dynamic theming in your applications. By leveraging CSS custom properties and localStorage, you can create beautiful, accessible themes that persist across sessions with minimal code.

**Key Takeaways:**

- Use CSS custom properties (`:root` selector) for theme variables
- Call `applySavedTheme()` at app startup and **await** it before rendering components
- `applySavedTheme()` returns a Promise - use `await` or `.then()` to handle it properly
- Use `newTheme()` to create and apply new themes
- Combine with WStyle for dynamic, themeable components
- Follow accessibility guidelines for color contrast
- Use semantic variable names for maintainability
- Test themes across different devices and scenarios
- Provide smooth transitions for better UX
- Document available themes and their purposes

By following the patterns and best practices in this guide, you can create sophisticated, user-friendly theming systems that enhance the overall experience of your WinnetouJs applications.
