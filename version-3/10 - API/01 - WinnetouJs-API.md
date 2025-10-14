# WinnetouJs API

WinnetouJs provides a comprehensive set of tools and functionalities for building modern web applications. This documentation covers all available APIs, organized by scope and functionality.

## Overview

The WinnetouJs API is designed to be simple, intuitive, and modular. Each module can be imported independently, keeping your bundle size optimized through tree-shaking.

**Core Philosophy:**

- **Simple & Direct** - Clear method signatures with minimal complexity
- **Type-Safe** - Full TypeScript support with detailed type definitions
- **Modular** - Import only what you need
- **Performant** - Optimized for speed and efficiency

## API Documentation Index

### [Core API](./02%20-%20core-api.md)

The foundation of WinnetouJs, providing state management and event handling.

**Import:**

```javascript
import { W } from "winnetoujs";
```

**Key Methods:**

- `W.setMutable()` - Create persistent mutables in localStorage
- `W.getMutable()` - Retrieve mutable values
- `W.initMutable()` - Create non-persistent mutables
- `W.setMutableNotPersistent()` - Update non-persistent mutables
- `W.fx()` - Create event handlers for constructos
- `W.strings` - Translation strings object

---

### [Constructos API](./03%20-%20constructos-api.md)

Methods for working with constructos (compiled HTML components).

**Import:**

```javascript
import { $constructoName } from "./path/to/file.wcto";
```

**Key Methods:**

- `.create()` - Attach constructo to DOM
- `.constructoString()` - Get HTML string without DOM attachment

**Features:**

- Props system with types and descriptions
- ID management and generation
- Options (clear, reverse, identifier)
- Mutable connections

---

### [Router API](./04%20-%20router-api.md)

Single-Page Application routing system.

**Import:**

```javascript
import { Router } from "winnetoujs/modules/router";
```

**Key Methods:**

- `Router.navigate()` - Navigate to a route
- `Router.createRoutes()` - Register routes and lifecycle hooks

**Features:**

- Browser history support
- Lifecycle hooks (onGo, onBack)
- Type-safe route structure
- Clean organization pattern

---

### [Color Themes API](./05%20-%20color-themes-api.md)

Dynamic theme switching using CSS custom properties.

**Import:**

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";
```

**Key Methods:**

- `ColorThemes.applySavedTheme()` - Apply saved theme at startup (async)
- `ColorThemes.newTheme()` - Create and apply new theme

**Features:**

- CSS custom properties based
- Automatic persistence
- Instant theme application
- Light/dark mode support

---

### [Translations API](./06%20-%20translations-api.md)

Built-in internationalization (i18n) system.

**Import:**

```javascript
import {
  updateTranslations,
  changeLang,
} from "winnetoujs/modules/translations";
```

**Key Functions:**

- `updateTranslations()` - Initialize translation system (async)
- `changeLang()` - Change application language

**Features:**

- JavaScript/TypeScript base language (F12 support)
- JSON translation files
- Automatic persistence
- Dynamic language switching

---

### [Style API](./07%20-%20style-api.md)

JavaScript-based inline styling system.

**Import:**

```javascript
import { wstyle } from "winnetoujs/modules/style";
```

**Key Functions:**

- `wstyle()` - Create CSS style object

**Features:**

- Define styles as JavaScript objects
- Reusable style definitions
- Export/import across files
- Full IDE support

**⚠️ Note:** Increases bundle size - use sparingly for dynamic styles only.

---

### [SSR API](./08%20-%20ssr-api.md)

Server-Side Rendering utilities.

**Import:**

```javascript
import { ssr, escapeHTML, loadPartial } from "winnetoujs/modules/ssr";
```

**Key Functions:**

- `ssr()` - Concatenate constructo strings
- `escapeHTML()` - Escape HTML for security
- `loadPartial()` - Load partial files with caching

**Features:**

- Node.js + Express support
- Automatic array flattening
- Partial caching
- XSS protection

---

## Quick Reference

### State Management

```javascript
import { W } from "winnetoujs";

// Persistent mutables
W.setMutable("user", "john");
const user = W.getMutable("user");

// Non-persistent mutables
const status = W.initMutable("idle");
W.setMutableNotPersistent(status, "loading");
```

### Constructos

```javascript
import { $card } from "./components.wcto";

// Create in DOM
const result = new $card({ title: "Title" }).create("#app");

// Get HTML string
const html = new $card({ title: "Title" }).constructoString();

// With options
new $card({ title: "Title" }).create("#app", {
  clear: true,
  reverse: true,
});

// With custom identifier
new $card({ title: "Title" }, { identifier: "main" }).create("#app");
```

### Event Handling

```javascript
import { W } from "winnetoujs";

// Basic event
W.fx(() => console.log("clicked"));

// With element access
W.fx(self => {
  self.style.color = "red";
}, "this");

// With multiple arguments
W.fx(
  (self, id, msg) => {
    self.classList.add("active");
    console.log(id, msg);
  },
  "this",
  "myId",
  "Hello"
);
```

### Routing

```javascript
import { Router } from "winnetoujs/modules/router";

// Navigate
Router.navigate("/home");

// Create routes
Router.createRoutes(routes, {
  onGo(route) {
    console.log("Go:", route);
  },
  onBack(route) {
    console.log("Back:", route);
  },
});
```

### Theming

```javascript
import { ColorThemes } from "winnetoujs/modules/colorThemes";

// Apply saved theme (at startup)
await ColorThemes.applySavedTheme();

// Create new theme
ColorThemes.newTheme({
  "--primary": "#3498db",
  "--background": "#ffffff",
});
```

### Translations

```javascript
import {
  updateTranslations,
  changeLang,
} from "winnetoujs/modules/translations";
import strings from "./translations/en-us";

// Initialize
await updateTranslations({
  stringsClass: strings,
  translationsPublicPath: "/translations",
});

// Change language
changeLang("pt-br", true);

// Use strings
console.log(strings.appTitle);
```

### Styling

```javascript
import { wstyle } from "winnetoujs/modules/style";

const buttonStyle = wstyle({
  padding: "10px 20px",
  "background-color": "#3498db",
  color: "white",
});

new $button({
  style: buttonStyle,
  text: "Click",
}).create("#app");
```

### SSR

```javascript
import { ssr, escapeHTML, loadPartial } from "winnetoujs/modules/ssr";

const page = ssr(
  new $header({ title: "SSR" }).constructoString(),
  loadPartial("./content/main.html"),
  new $footer().constructoString()
);

res.send(page);
```

---

## Type Definitions

### Core Types

```typescript
// Mutable
type MutableKey = string;
type MutableValue = string;

// Event Handler
type EventHandler = (...args: any[]) => void;

// Constructo
interface ConstructoOptions {
  identifier?: string;
}

interface CreateOptions {
  clear?: boolean;
  reverse?: boolean;
}

interface ConstructoResult {
  ids: Record<string, string>;
}
```

### Module Types

```typescript
// Router
type Route = string;
type RouteHandler = () => void;

interface RouterHooks {
  onGo?: (route: string) => void;
  onBack?: (route: string) => void;
}

// Translations
interface TranslationConfig {
  stringsClass: Record<string, string>;
  translationsPublicPath: string;
}

// Themes
type CSSProperties = Record<string, string>;

// SSR
interface LoadPartialOptions {
  verbose?: boolean;
}
```

---

## Best Practices

### Import Strategy

**Import only what you need:**

```javascript
// ✅ Good - Tree-shakable
import { W } from "winnetoujs";
import { Router } from "winnetoujs/modules/router";
import { ColorThemes } from "winnetoujs/modules/colorThemes";

// ❌ Avoid - Imports everything
import * as Winnetou from "winnetoujs";
```

### Async Methods

**Always handle promises:**

```javascript
// ✅ Good
await ColorThemes.applySavedTheme();
await updateTranslations({ ... });

// or
ColorThemes.applySavedTheme().then(() => startApp());
```

### Type Safety

**Use TypeScript for better DX:**

```typescript
// Define prop types in constructos
<winnetou>
  <div id="[[card]]">
    <h2>{{ title: string }}</h2>
    <p>{{ content: string }}</p>
    <span>{{ count: number }}</span>
  </div>
</winnetou>
```

### Performance

**Optimize bundle size:**

```javascript
// ✅ Use CSS for static styles
// ✅ Use WStyle only for dynamic styles
// ✅ Import modules selectively
// ✅ Use mutables efficiently
```

---

## Additional Resources

- [Getting Started Guide](../01%20-%20Getting%20Started/)
- [Routing System](../02%20-%20Routing%20System/)
- [Styling Constructos](../03%20-%20Styling%20Constructos/)
- [Native Translations](../04%20-%20Native%20Translations/)
- [Bundle WinnetouJs App](../05%20-%20Bundle%20WinnetouJs%20App/)

---

## Support

For issues, questions, or contributions, please visit the [WinnetouJs GitHub repository](https://github.com/cedrosdev/winnetoujs).
