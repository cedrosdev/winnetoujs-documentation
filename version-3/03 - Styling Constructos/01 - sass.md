# Using Sass with WinnetouJs

## Overview

Sass (Syntactically Awesome Style Sheets) is a powerful CSS preprocessor that extends CSS with features like variables, nested rules, mixins, and functions. WinnetouJs seamlessly integrates with Sass, allowing you to write modular, maintainable styles for your constructos.

This guide will show you how to set up and use Sass effectively in your WinnetouJs applications.

## Installation

Before using Sass with WinnetouJs, you need to install the Sass compiler:

```bash
npm install -D sass
```

This installs Sass as a development dependency in your project.

## Configuration

### Package.json Scripts

Add the following scripts to your `package.json` to compile Sass files during development and production:

```json
{
  "scripts": {
    "sass:dev": "sass --embed-sources --watch --style expanded sass/main.scss:dist/css/main.css --load-path='./src'",
    "sass:prod": "sass sass/main.scss:dist/css/main.min.css --style compressed --no-source-map --quiet --load-path='./src'"
  }
}
```

**Script Breakdown:**

- **`sass:dev`**: Development mode

  - `--embed-sources`: Embeds source content in source maps for debugging
  - `--watch`: Watches for file changes and recompiles automatically
  - `--style expanded`: Outputs readable, unminified CSS
  - `--load-path='./src'`: Allows importing Sass files from the `src` directory
  - Output: `dist/css/main.css`

- **`sass:prod`**: Production mode
  - `--style compressed`: Outputs minified CSS
  - `--no-source-map`: Skips source map generation for smaller file size
  - `--quiet`: Suppresses compilation messages
  - `--load-path='./src'`: Allows importing Sass files from the `src` directory
  - Output: `dist/css/main.min.css`

### Running Sass Compilation

**Development mode (with watch):**

```bash
npm run sass:dev
```

**Production build:**

```bash
npm run sass:prod
```

## Project Structure

Organize your Sass files following the WinnetouJs recommended structure:

```
application/
├── src/
│   ├── app.ts
│   ├── common-constructos/
│   │   ├── common.wcto.html
│   │   ├── _common.scss        # Partial Sass file for common constructos
│   │   └── common.ts
│   ├── cards/
│   │   ├── cards.wcto.html
│   │   ├── _cards.scss          # Partial Sass file for cards
│   │   └── cards.ts
├── sass/
│   ├── main.scss                # Main Sass entry point
│   ├── _variables.scss          # Global variables
│   ├── _mixins.scss             # Reusable mixins
│   └── _base.scss               # Base styles
├── dist/
│   └── css/
│       ├── main.css             # Compiled CSS (dev)
│       └── main.min.css         # Minified CSS (prod)
└── package.json
```

## Organizing Styles with Constructos

### Co-locating Styles with Constructos

WinnetouJs encourages organizing Sass files alongside their corresponding constructos. Use partial Sass files (prefixed with `_`) for each constructo group:

**Example: `src/cards/_cards.scss`**

```scss
// Cards constructo styles
.price-card {
  border: 1px solid var(--border-color);
  border-radius: 8px;
  padding: 1.5rem;
  background: var(--card-bg);

  .price {
    font-size: 2rem;
    font-weight: bold;
    color: var(--primary-color);
  }

  .product-name {
    font-size: 1.25rem;
    margin-bottom: 0.5rem;
  }
}

.feature-card {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  padding: 2rem;

  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  }
}
```

### Main Sass Entry Point

Import all partial Sass files in your `sass/main.scss`:

```scss
// Global configuration
@import "variables";
@import "mixins";
@import "base";

// Constructo styles (using load-path='./src')
@import "common-constructos/common";
@import "cards/cards";
@import "navigation/nav";
@import "forms/forms";
```

**Note:** The `--load-path='./src'` flag allows you to import files from the `src` directory without specifying the full path.

## Using Sass Features

### Variables

Define global variables in `sass/_variables.scss`:

```scss
// Colors
$primary-color: #3498db;
$secondary-color: #2ecc71;
$danger-color: #e74c3c;
$text-color: #333;
$bg-color: #f5f5f5;

// Spacing
$spacing-xs: 0.25rem;
$spacing-sm: 0.5rem;
$spacing-md: 1rem;
$spacing-lg: 1.5rem;
$spacing-xl: 2rem;

// Breakpoints
$breakpoint-sm: 576px;
$breakpoint-md: 768px;
$breakpoint-lg: 992px;
$breakpoint-xl: 1200px;
```

Use variables in your partial files:

```scss
@import "variables";

.button {
  background-color: $primary-color;
  padding: $spacing-md $spacing-lg;
  color: white;

  &:hover {
    background-color: darken($primary-color, 10%);
  }
}
```

### Nesting

Sass allows you to nest selectors, making your styles more readable:

```scss
.card-container {
  padding: $spacing-lg;

  .card {
    background: white;
    border-radius: 8px;

    .card-header {
      border-bottom: 1px solid #eee;
      padding: $spacing-md;

      h3 {
        margin: 0;
        font-size: 1.5rem;
      }
    }

    .card-body {
      padding: $spacing-lg;
    }
  }
}
```

### Mixins

Create reusable style patterns in `sass/_mixins.scss`:

```scss
// Flexbox centering
@mixin flex-center {
  display: flex;
  align-items: center;
  justify-content: center;
}

// Responsive breakpoint
@mixin respond-to($breakpoint) {
  @media (min-width: $breakpoint) {
    @content;
  }
}

// Card shadow
@mixin card-shadow($level: 1) {
  @if $level == 1 {
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  } @else if $level == 2 {
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
  } @else {
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
  }
}
```

Use mixins in your styles:

```scss
@import "mixins";

.modal {
  @include flex-center;
  @include card-shadow(2);

  .modal-content {
    @include respond-to($breakpoint-md) {
      width: 600px;
    }
  }
}
```

### Functions

Create custom functions for calculations:

```scss
@function rem($pixels, $base: 16) {
  @return ($pixels / $base) * 1rem;
}

// Usage
.heading {
  font-size: rem(24); // 1.5rem
  margin-bottom: rem(16); // 1rem
}
```

## Practical Examples

### Example 1: Styled Button Constructo

**`src/buttons/buttons.wcto.html`**

```html
<winnetou description="A customizable button component">
  <button
    id="[[styledButton]]"
    class="btn btn--{{variant:'primary'|'secondary'|'danger'}}"
    onclick="{{onclick}}">
    {{text:string}}
  </button>
</winnetou>
```

**`src/buttons/_buttons.scss`**

```scss
@import "variables";
@import "mixins";

.btn {
  padding: $spacing-md $spacing-lg;
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;

  &:hover {
    transform: translateY(-1px);
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
  }

  &:active {
    transform: translateY(0);
  }

  &--primary {
    background-color: $primary-color;
    color: white;

    &:hover {
      background-color: darken($primary-color, 8%);
    }
  }

  &--secondary {
    background-color: $secondary-color;
    color: white;

    &:hover {
      background-color: darken($secondary-color, 8%);
    }
  }

  &--danger {
    background-color: $danger-color;
    color: white;

    &:hover {
      background-color: darken($danger-color, 8%);
    }
  }
}
```

### Example 2: Card Grid Layout

**`src/cards/cards.wcto.html`**

```html
<winnetou description="A responsive card grid container">
  <div id="[[cardGrid]]" class="card-grid">{{content}}</div>
</winnetou>

<winnetou description="Individual card component">
  <div id="[[card]]" class="card">
    <div class="card__image">
      <img src="{{imageUrl}}" alt="{{imageAlt}}" />
    </div>
    <div class="card__content">
      <h3 class="card__title">{{title}}</h3>
      <p class="card__description">{{description}}</p>
    </div>
  </div>
</winnetou>
```

**`src/cards/_cards.scss`**

```scss
@import "variables";
@import "mixins";

.card-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: $spacing-lg;
  padding: $spacing-lg;

  @include respond-to($breakpoint-sm) {
    grid-template-columns: repeat(2, 1fr);
  }

  @include respond-to($breakpoint-lg) {
    grid-template-columns: repeat(3, 1fr);
  }
}

.card {
  @include card-shadow(1);
  border-radius: 8px;
  overflow: hidden;
  background: white;
  transition: all 0.3s ease;

  &:hover {
    @include card-shadow(2);
    transform: translateY(-4px);
  }

  &__image {
    width: 100%;
    height: 200px;
    overflow: hidden;

    img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
  }

  &__content {
    padding: $spacing-lg;
  }

  &__title {
    font-size: 1.25rem;
    margin: 0 0 $spacing-sm 0;
    color: $text-color;
  }

  &__description {
    color: lighten($text-color, 20%);
    line-height: 1.6;
  }
}
```

### Example 3: Form Styles with BEM Methodology

**`src/forms/_forms.scss`**

```scss
@import "variables";

.form {
  max-width: 500px;
  margin: 0 auto;

  &__group {
    margin-bottom: $spacing-lg;
  }

  &__label {
    display: block;
    margin-bottom: $spacing-sm;
    font-weight: 500;
    color: $text-color;
  }

  &__input {
    width: 100%;
    padding: $spacing-md;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 1rem;
    transition: border-color 0.3s ease;

    &:focus {
      outline: none;
      border-color: $primary-color;
      box-shadow: 0 0 0 3px rgba($primary-color, 0.1);
    }

    &--error {
      border-color: $danger-color;
    }
  }

  &__error-message {
    display: block;
    margin-top: $spacing-xs;
    color: $danger-color;
    font-size: 0.875rem;
  }
}
```

## Integration with Color Themes

When using WinnetouJs Color Themes module, combine CSS custom properties with Sass:

**`sass/_variables.scss`**

```scss
:root {
  // Define CSS custom properties
  --primary-color: #3498db;
  --secondary-color: #2ecc71;
  --background-color: #ffffff;
  --text-color: #333333;
  --border-color: #dddddd;
}

// Sass variables can reference CSS custom properties
$primary: var(--primary-color);
$secondary: var(--secondary-color);
```

**Usage in components:**

```scss
.button {
  background-color: var(--primary-color);
  color: var(--text-color);

  &:hover {
    // Use Sass for calculations, but reference CSS vars
    filter: brightness(1.1);
  }
}
```

This approach allows the WinnetouJs Color Themes module to dynamically change theme colors while maintaining Sass's powerful features.

## Best Practices

### 1. Use Partial Files

Prefix Sass files with an underscore (`_`) to indicate they are partials and shouldn't be compiled directly:

```
sass/
├── _variables.scss
├── _mixins.scss
└── main.scss
```

### 2. Follow BEM Naming Convention

Use Block Element Modifier (BEM) methodology for clear, maintainable CSS:

```scss
.card {
  // Block
  &__header {
    // Element
    &--highlighted {
      // Modifier
      background-color: $primary-color;
    }
  }
}
```

### 3. Keep Specificity Low

Avoid deep nesting (max 3-4 levels) to prevent specificity issues:

❌ **Avoid:**

```scss
.container {
  .sidebar {
    .menu {
      .item {
        .link {
          color: blue; // Too deep!
        }
      }
    }
  }
}
```

✅ **Prefer:**

```scss
.menu-link {
  color: blue;
}
```

### 4. Co-locate Styles with Constructos

Keep Sass files close to their corresponding constructos for better maintainability:

```
src/
└── cards/
    ├── cards.wcto.html
    ├── _cards.scss
    └── cards.ts
```

### 5. Use CSS Custom Properties for Dynamic Values

Combine Sass with CSS custom properties for values that need to change dynamically:

```scss
.theme-element {
  // Static Sass variable
  padding: $spacing-md;

  // Dynamic CSS custom property
  color: var(--theme-text-color);
}
```

### 6. Leverage Sass Functions for Calculations

Use Sass functions for responsive sizing and spacing:

```scss
@function fluid-size($min, $max, $min-vw: 320, $max-vw: 1200) {
  $slope: ($max - $min) / ($max-vw - $min-vw);
  $intercept: $min - $slope * $min-vw;
  @return clamp(#{$min}px, #{$intercept}px + #{$slope * 100}vw, #{$max}px);
}

.heading {
  font-size: fluid-size(18, 32); // Scales between 18px and 32px
}
```

## Troubleshooting

### Common Issues

**1. Compilation Errors**

If Sass fails to compile, check:

- Syntax errors in `.scss` files
- Missing semicolons or braces
- Incorrect import paths

**2. Styles Not Applying**

Ensure:

- The compiled CSS file is linked in your HTML
- The Sass compiler is running (`npm run sass:dev`)
- Class names match between constructos and Sass files

**3. Import Path Issues**

If imports fail:

- Verify the `--load-path='./src'` flag is set in scripts
- Check that partial files are prefixed with `_`
- Use correct relative paths in imports

### Debugging Tips

**Enable verbose output:**

```bash
sass --watch --verbose sass/main.scss:dist/css/main.css
```

**Check source maps in DevTools:**
Source maps (generated in dev mode) allow you to see original Sass files in browser DevTools, making debugging easier.

## Performance Optimization

### Production Builds

Always use compressed output for production:

```json
{
  "scripts": {
    "sass:prod": "sass sass/main.scss:dist/css/main.min.css --style compressed --no-source-map"
  }
}
```

### Minimize File Size

- Remove unused styles
- Avoid duplicate rules
- Use shorthand properties
- Leverage CSS minification

### Optimize Load Times

1. **Split critical CSS**: Extract above-the-fold styles
2. **Async load non-critical CSS**: Use `media="print"` trick
3. **Use CSS containment**: Apply `contain` property where appropriate

## Conclusion

Sass integration with WinnetouJs provides a powerful styling solution for building modern web applications. By following the recommended structure and best practices outlined in this guide, you can create maintainable, scalable, and performant stylesheets for your WinnetouJs constructos.

**Key Takeaways:**

- Use `sass:dev` for development with watch mode
- Use `sass:prod` for optimized production builds
- Co-locate Sass files with constructos for better organization
- Leverage Sass features like variables, nesting, and mixins
- Combine with CSS custom properties for dynamic theming
- Follow BEM naming conventions for clarity
- Keep specificity low and nesting shallow
