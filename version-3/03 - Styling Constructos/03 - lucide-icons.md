# Lucide Icons: Beautiful Icons for WinnetouJs

## Overview

Lucide is a community-maintained fork of Feather Icons, providing a comprehensive set of beautiful, consistent, and customizable SVG icons. WinnetouJs recommends using Lucide icons as the preferred icon solution for web applications, as it integrates seamlessly and keeps your bundle size optimized.

**Key Features:**

- 🎨 Over 1,000 beautifully crafted SVG icons
- 📦 Tree-shakable - only import what you need
- ⚡ Lightweight and performant
- 🎯 Easy integration with constructos
- 🔧 Fully customizable (size, color, stroke)
- 📱 Perfect for responsive designs

**Why Lucide?**

In WinnetouJs v3, icon digestion was removed to keep the framework lightweight. Icon libraries like Lucide have their own optimized integration methods, which is faster and makes the final bundle lighter.

## Installation

Install Lucide as a dependency in your project:

```bash
npm install lucide
```

For server-side rendering, use the static version:

```bash
npm install lucide-static
```

## Basic Usage

### Importing Icons

Import the `createElement` function and the specific icons you need from Lucide:

```javascript
import { createElement, Menu, Home, Settings, User } from "lucide";
```

### Creating Icon Elements

Use `createElement()` to convert Lucide icons to HTML:

```javascript
import { createElement, Menu } from "lucide";

const menuIcon = createElement(Menu);
const menuIconHTML = menuIcon.outerHTML;

console.log(menuIconHTML);
// Output: <svg>...</svg>
```

### Using Icons in Constructos

Pass icon HTML directly to constructo props:

**`menu.wcto.html`**

```html
<winnetou description="Menu item with icon">
  <div id="[[menuItem]]" class="menu-item" onclick="{{onclick}}">
    <span class="menu-icon">{{icon}}</span>
    <span class="menu-text">{{text}}</span>
  </div>
</winnetou>
```

**`menu.ts`**

```javascript
import { W } from "winnetoujs";
import { createElement, Home, Settings, User } from "lucide";
import { $menuItem } from "./menu.wcto";

// Create icons
const homeIcon = createElement(Home).outerHTML;
const settingsIcon = createElement(Settings).outerHTML;
const userIcon = createElement(User).outerHTML;

// Use in constructos
new $menuItem({
  icon: homeIcon,
  text: "Home",
  onclick: W.fx(() => console.log("Home clicked")),
}).create("#sidebar");

new $menuItem({
  icon: settingsIcon,
  text: "Settings",
  onclick: W.fx(() => console.log("Settings clicked")),
}).create("#sidebar");

new $menuItem({
  icon: userIcon,
  text: "Profile",
  onclick: W.fx(() => console.log("Profile clicked")),
}).create("#sidebar");
```

## Icon Customization

### Size Customization

Set icon size using the `size` option:

```javascript
import { createElement, Heart } from "lucide";

// Small icon (16x16)
const smallHeart = createElement(Heart, { size: 16 }).outerHTML;

// Medium icon (24x24) - default
const mediumHeart = createElement(Heart, { size: 24 }).outerHTML;

// Large icon (48x48)
const largeHeart = createElement(Heart, { size: 48 }).outerHTML;
```

### Color Customization

Icons inherit the current text color by default. You can customize this with CSS:

**Using CSS:**

```css
.icon-primary {
  color: #3498db;
}

.icon-danger {
  color: #e74c3c;
}

.icon-success {
  color: #2ecc71;
}
```

**In constructo:**

```html
<winnetou description="Icon with custom color">
  <button id="[[iconButton]]" onclick="{{onclick}}">
    <span class="{{iconClass}}">{{icon}}</span>
    {{text}}
  </button>
</winnetou>
```

```javascript
import { createElement, Trash2 } from "lucide";

new $iconButton({
  icon: createElement(Trash2, { size: 20 }).outerHTML,
  text: "Delete",
  iconClass: "icon-danger",
  onclick: W.fx(() => handleDelete()),
}).create("#app");
```

### Stroke Width Customization

Adjust the stroke width for thinner or bolder icons:

```javascript
import { createElement, Star } from "lucide";

// Thin stroke
const thinStar = createElement(Star, { strokeWidth: 1 }).outerHTML;

// Default stroke
const normalStar = createElement(Star, { strokeWidth: 2 }).outerHTML;

// Bold stroke
const boldStar = createElement(Star, { strokeWidth: 3 }).outerHTML;
```

### Additional Attributes

Pass any SVG attributes to customize icons further:

```javascript
import { createElement, AlertCircle } from "lucide";

const customIcon = createElement(AlertCircle, {
  size: 24,
  strokeWidth: 2,
  color: "#e74c3c",
  fill: "none",
  class: "alert-icon",
}).outerHTML;
```

## Practical Examples

### Example 1: Navigation Menu

**`navigation.wcto.html`**

```html
<winnetou description="Navigation bar">
  <nav id="[[navbar]]" class="navbar">{{content}}</nav>
</winnetou>

<winnetou description="Navigation item">
  <a id="[[navItem]]" href="{{href}}" class="nav-item" onclick="{{onclick}}">
    <span class="nav-icon">{{icon}}</span>
    <span class="nav-label">{{label}}</span>
  </a>
</winnetou>
```

**`navigation.ts`**

```javascript
import { W } from "winnetoujs";
import { createElement, Home, ShoppingCart, User, Settings } from "lucide";
import { $navbar, $navItem } from "./navigation.wcto";

class Navigation {
  constructor() {
    this.navbarId = "";
    this.render();
  }

  render() {
    // Create navbar container
    this.navbarId = new $navbar({
      content: "",
    }).create("#app").ids.navbar;

    // Define menu items
    const menuItems = [
      { icon: Home, label: "Home", href: "/home" },
      { icon: ShoppingCart, label: "Shop", href: "/shop" },
      { icon: User, label: "Profile", href: "/profile" },
      { icon: Settings, label: "Settings", href: "/settings" },
    ];

    // Create nav items
    menuItems.forEach(item => {
      new $navItem({
        icon: createElement(item.icon, { size: 20 }).outerHTML,
        label: item.label,
        href: item.href,
        onclick: W.fx(e => {
          e.preventDefault();
          this.navigate(item.href);
        }, "this"),
      }).create(`#${this.navbarId}`);
    });
  }

  navigate(route) {
    console.log("Navigating to:", route);
    // Router logic here
  }
}

export default Navigation;
```

### Example 2: Action Buttons

**`buttons.wcto.html`**

```html
<winnetou description="Icon button">
  <button
    id="[[iconButton]]"
    class="btn btn--{{variant:'primary'|'secondary'|'danger'}}"
    onclick="{{onclick}}">
    <span class="btn-icon">{{icon}}</span>
    <span class="btn-text">{{text}}</span>
  </button>
</winnetou>
```

**`actions.ts`**

```javascript
import { W } from "winnetoujs";
import { createElement, Save, Trash2, Download, Upload } from "lucide";
import { $iconButton } from "./buttons.wcto";

// Save button
new $iconButton({
  icon: createElement(Save, { size: 18 }).outerHTML,
  text: "Save",
  variant: "primary",
  onclick: W.fx(() => handleSave()),
}).create("#actions");

// Delete button
new $iconButton({
  icon: createElement(Trash2, { size: 18 }).outerHTML,
  text: "Delete",
  variant: "danger",
  onclick: W.fx(() => handleDelete()),
}).create("#actions");

// Download button
new $iconButton({
  icon: createElement(Download, { size: 18 }).outerHTML,
  text: "Download",
  variant: "secondary",
  onclick: W.fx(() => handleDownload()),
}).create("#actions");
```

### Example 3: Status Indicators

**`status.wcto.html`**

```html
<winnetou description="Status message">
  <div
    id="[[statusMessage]]"
    class="status status--{{type:'info'|'success'|'warning'|'error'}}">
    <span class="status-icon">{{icon}}</span>
    <span class="status-text">{{message}}</span>
  </div>
</winnetou>
```

**`notifications.ts`**

```javascript
import { W } from "winnetoujs";
import {
  createElement,
  Info,
  CheckCircle,
  AlertTriangle,
  XCircle,
} from "lucide";
import { $statusMessage } from "./status.wcto";

class Notifications {
  static info(message) {
    new $statusMessage({
      icon: createElement(Info, { size: 20 }).outerHTML,
      message: message,
      type: "info",
    }).create("#notifications");
  }

  static success(message) {
    new $statusMessage({
      icon: createElement(CheckCircle, { size: 20 }).outerHTML,
      message: message,
      type: "success",
    }).create("#notifications");
  }

  static warning(message) {
    new $statusMessage({
      icon: createElement(AlertTriangle, { size: 20 }).outerHTML,
      message: message,
      type: "warning",
    }).create("#notifications");
  }

  static error(message) {
    new $statusMessage({
      icon: createElement(XCircle, { size: 20 }).outerHTML,
      message: message,
      type: "error",
    }).create("#notifications");
  }
}

// Usage
Notifications.success("File saved successfully!");
Notifications.error("Failed to load data.");
```

### Example 4: Icon-only Buttons

**`iconButtons.wcto.html`**

```html
<winnetou description="Icon-only button">
  <button
    id="[[iconOnlyButton]]"
    class="icon-btn"
    onclick="{{onclick}}"
    title="{{tooltip}}">
    {{icon}}
  </button>
</winnetou>
```

**`toolbar.ts`**

```javascript
import { W } from "winnetoujs";
import { createElement, Edit, Copy, Share2, MoreVertical } from "lucide";
import { $iconOnlyButton } from "./iconButtons.wcto";

// Edit button
new $iconOnlyButton({
  icon: createElement(Edit, { size: 18 }).outerHTML,
  tooltip: "Edit",
  onclick: W.fx(() => handleEdit()),
}).create("#toolbar");

// Copy button
new $iconOnlyButton({
  icon: createElement(Copy, { size: 18 }).outerHTML,
  tooltip: "Copy",
  onclick: W.fx(() => handleCopy()),
}).create("#toolbar");

// Share button
new $iconOnlyButton({
  icon: createElement(Share2, { size: 18 }).outerHTML,
  tooltip: "Share",
  onclick: W.fx(() => handleShare()),
}).create("#toolbar");

// More options
new $iconOnlyButton({
  icon: createElement(MoreVertical, { size: 18 }).outerHTML,
  tooltip: "More options",
  onclick: W.fx(() => showMoreOptions()),
}).create("#toolbar");
```

## Creating an Icon Library

For better organization, create a centralized icon library:

**`lib/icons.js`**

```javascript
import { createElement } from "lucide";
import {
  Home,
  Settings,
  User,
  ShoppingCart,
  Heart,
  Star,
  Search,
  Menu,
  X,
  ChevronRight,
  ChevronLeft,
  Plus,
  Minus,
  Edit,
  Trash2,
  Save,
  Download,
  Upload,
  AlertCircle,
  CheckCircle,
  Info,
  XCircle,
} from "lucide";

// Icon configuration
const iconConfig = {
  size: 24,
  strokeWidth: 2,
};

// Export icon factory
export const icons = {
  // Navigation
  home: (size = iconConfig.size) => createElement(Home, { size }).outerHTML,
  settings: (size = iconConfig.size) =>
    createElement(Settings, { size }).outerHTML,
  user: (size = iconConfig.size) => createElement(User, { size }).outerHTML,
  menu: (size = iconConfig.size) => createElement(Menu, { size }).outerHTML,
  close: (size = iconConfig.size) => createElement(X, { size }).outerHTML,

  // E-commerce
  cart: (size = iconConfig.size) =>
    createElement(ShoppingCart, { size }).outerHTML,
  heart: (size = iconConfig.size) => createElement(Heart, { size }).outerHTML,
  star: (size = iconConfig.size) => createElement(Star, { size }).outerHTML,

  // Actions
  search: (size = iconConfig.size) => createElement(Search, { size }).outerHTML,
  edit: (size = iconConfig.size) => createElement(Edit, { size }).outerHTML,
  delete: (size = iconConfig.size) => createElement(Trash2, { size }).outerHTML,
  save: (size = iconConfig.size) => createElement(Save, { size }).outerHTML,
  download: (size = iconConfig.size) =>
    createElement(Download, { size }).outerHTML,
  upload: (size = iconConfig.size) => createElement(Upload, { size }).outerHTML,

  // Chevrons
  chevronRight: (size = iconConfig.size) =>
    createElement(ChevronRight, { size }).outerHTML,
  chevronLeft: (size = iconConfig.size) =>
    createElement(ChevronLeft, { size }).outerHTML,

  // Math
  plus: (size = iconConfig.size) => createElement(Plus, { size }).outerHTML,
  minus: (size = iconConfig.size) => createElement(Minus, { size }).outerHTML,

  // Status
  info: (size = iconConfig.size) => createElement(Info, { size }).outerHTML,
  success: (size = iconConfig.size) =>
    createElement(CheckCircle, { size }).outerHTML,
  warning: (size = iconConfig.size) =>
    createElement(AlertCircle, { size }).outerHTML,
  error: (size = iconConfig.size) => createElement(XCircle, { size }).outerHTML,
};
```

**Usage:**

```javascript
import { icons } from "@libs/icons";
import { $menuItem } from "./menu.wcto";

new $menuItem({
  icon: icons.home(20),
  text: "Home",
  onclick: W.fx(() => navigate("/home")),
}).create("#menu");

new $menuItem({
  icon: icons.settings(20),
  text: "Settings",
  onclick: W.fx(() => navigate("/settings")),
}).create("#menu");
```

## Server-Side Rendering

For server-side rendering with WinnetouJs, use the `lucide-static` package:

**Installation:**

```bash
npm install lucide-static
```

**Usage in Node.js:**

```javascript
const { ExternalLink, ArrowRight, Home, Settings } = require("lucide-static");
const { $footer, $menuItem } = require("./constructos.wcto");

// In your SSR function
function renderPage() {
  const footer = new $footer({
    currentYear: new Date().getFullYear(),
    iconArrowRight: ArrowRight,
    iconExternalLink: ExternalLink,
  }).constructoString();

  const menu = new $menuItem({
    icon: Home,
    text: "Home",
    onclick: "",
  }).constructoString();

  return `
    <!DOCTYPE html>
    <html>
      <body>
        ${menu}
        ${footer}
      </body>
    </html>
  `;
}
```

**Note:** `lucide-static` returns raw SVG strings, so you don't need to use `createElement()` or `.outerHTML` on the server side.

## Styling Icons with CSS

### Basic Icon Styles

```css
/* Icon container */
.icon {
  display: inline-flex;
  align-items: center;
  justify-content: center;
}

/* Icon sizing */
.icon svg {
  width: 1em;
  height: 1em;
}

/* Icon colors */
.icon-primary svg {
  color: #3498db;
}

.icon-success svg {
  color: #2ecc71;
}

.icon-danger svg {
  color: #e74c3c;
}

.icon-muted svg {
  color: #95a5a6;
}
```

### Hover Effects

```css
.icon-button {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  border: none;
  background: transparent;
  cursor: pointer;
  transition: all 0.3s ease;
}

.icon-button svg {
  transition: transform 0.3s ease;
}

.icon-button:hover svg {
  transform: scale(1.1);
}

.icon-button:active svg {
  transform: scale(0.95);
}
```

### Animated Icons

```css
/* Spinning loader */
@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.icon-spin svg {
  animation: spin 1s linear infinite;
}

/* Pulse effect */
@keyframes pulse {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0.5;
  }
}

.icon-pulse svg {
  animation: pulse 2s ease-in-out infinite;
}
```

## Popular Icon Categories

### Navigation Icons

- `Home` - Home page
- `Menu` - Hamburger menu
- `X` - Close/exit
- `ChevronRight` - Next/forward
- `ChevronLeft` - Previous/back
- `ArrowRight` - Direction indicator
- `ArrowLeft` - Direction indicator

### Action Icons

- `Edit` - Edit content
- `Trash2` - Delete
- `Save` - Save changes
- `Download` - Download file
- `Upload` - Upload file
- `Copy` - Copy to clipboard
- `Share2` - Share content
- `Plus` - Add/create new
- `Minus` - Remove/subtract

### Status Icons

- `CheckCircle` - Success
- `XCircle` - Error
- `AlertTriangle` - Warning
- `Info` - Information
- `AlertCircle` - Alert

### UI Elements

- `Search` - Search functionality
- `Filter` - Filter options
- `Settings` - Settings/preferences
- `MoreVertical` - More options (vertical)
- `MoreHorizontal` - More options (horizontal)

### User & Social

- `User` - User profile
- `Users` - Multiple users
- `Heart` - Favorite/like
- `Star` - Rating
- `ThumbsUp` - Like/approve
- `MessageCircle` - Comments/chat

### E-commerce

- `ShoppingCart` - Shopping cart
- `CreditCard` - Payment
- `Package` - Product/shipping
- `DollarSign` - Price/money

## Best Practices

### 1. Import Only What You Need

Tree-shake unused icons by importing only the ones you use:

✅ **Good:**

```javascript
import { createElement, Home, Settings, User } from "lucide";
```

❌ **Avoid:**

```javascript
import * as Icons from "lucide"; // Imports ALL icons
```

### 2. Centralize Icon Definitions

Create a centralized icon library to avoid repetition:

```javascript
// lib/icons.js
export const icons = {
  home: createElement(Home, { size: 20 }).outerHTML,
  settings: createElement(Settings, { size: 20 }).outerHTML,
  // ...
};
```

### 3. Use Consistent Sizing

Maintain consistent icon sizes throughout your app:

```javascript
const ICON_SIZES = {
  small: 16,
  medium: 20,
  large: 24,
  xlarge: 32,
};

const menuIcon = createElement(Menu, { size: ICON_SIZES.medium }).outerHTML;
```

### 4. Provide Accessible Labels

Always provide accessible labels for icon-only buttons:

```html
<button aria-label="Settings" title="Settings">{{settingsIcon}}</button>
```

### 5. Use Semantic Icons

Choose icons that clearly represent their function:

✅ **Clear:**

- Trash icon for delete
- Save icon for save
- Home icon for home page

❌ **Confusing:**

- Star icon for delete
- Heart icon for settings

### 6. Optimize for Performance

Cache icon HTML if used multiple times:

```javascript
// Cache icons
const cachedIcons = {
  home: createElement(Home, { size: 20 }).outerHTML,
  settings: createElement(Settings, { size: 20 }).outerHTML,
};

// Reuse cached icons
new $menuItem({ icon: cachedIcons.home }).create("#menu");
new $menuItem({ icon: cachedIcons.home }).create("#footer");
```

## Troubleshooting

### Icons Not Displaying

**Issue:** Icons don't appear in the UI.

**Solutions:**

1. Ensure you're using `.outerHTML` to convert the element to string:

   ```javascript
   const icon = createElement(Home).outerHTML; // ✅
   const icon = createElement(Home); // ❌
   ```

2. Check that the icon is imported correctly:

   ```javascript
   import { createElement, Home } from "lucide"; // ✅
   import { Home } from "lucide"; // ❌ Missing createElement
   ```

3. Verify the constructo prop receives the icon:
   ```html
   <span>{{icon}}</span>
   <!-- ✅ -->
   <span>{{icons}}</span>
   <!-- ❌ Wrong prop name -->
   ```

### Icons Too Large or Small

**Issue:** Icons don't match the expected size.

**Solutions:**

1. Set explicit size in `createElement()`:

   ```javascript
   createElement(Home, { size: 24 });
   ```

2. Control size with CSS:
   ```css
   .icon svg {
     width: 20px;
     height: 20px;
   }
   ```

### Icons Wrong Color

**Issue:** Icons have incorrect colors.

**Solutions:**

1. Icons inherit text color by default. Set color on parent:

   ```css
   .menu-item {
     color: #3498db;
   }
   ```

2. Target SVG directly:
   ```css
   .icon svg {
     color: #3498db;
   }
   ```

## Conclusion

Lucide icons provide a lightweight, flexible, and beautiful icon solution for WinnetouJs applications. By following the integration patterns and best practices outlined in this guide, you can create visually appealing, performant interfaces with minimal bundle overhead.

**Key Takeaways:**

- Use `createElement()` from Lucide to generate icon HTML
- Import only the icons you need for better tree-shaking
- Create a centralized icon library for consistency
- Customize icons with size, color, and stroke options
- Use `lucide-static` for server-side rendering
- Provide accessible labels for icon-only elements
- Cache frequently used icons for better performance
- Follow semantic icon selection principles

Lucide's extensive icon library and WinnetouJs's flexible constructo system make it easy to build professional, icon-rich user interfaces.
