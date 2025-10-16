# SSR API

The Server Side Rendering (SSR) module provides utilities for rendering WinnetouJs constructos on the server and working with server-side content.

## Import

```javascript
// CommonJS
const {
  loadPartial,
  escapeHTML,
  joinConstructos,
} = require("winnetoujs/modules/ssr");

// ES Modules
import {
  loadPartial,
  escapeHTML,
  joinConstructos,
} from "winnetoujs/modules/ssr";
```

---

## loadPartial()

Loads and returns the content of a file from the server's file system. This function is essential for loading HTML templates, partials, or any text-based content for server-side rendering.

### Syntax

```typescript
loadPartial(filePath: string): string
```

### Parameters

| Parameter  | Type     | Required | Description                                       |
| ---------- | -------- | -------- | ------------------------------------------------- |
| `filePath` | `string` | Yes      | The absolute or relative path to the file to load |

### Return Value

- **Type:** `string`
- **Description:** The content of the file as a string. Returns an empty string if the file doesn't exist or cannot be read.

### Examples

#### Basic Usage

```javascript
const content = loadPartial("./views/home.html");
console.log(content); // HTML content as string
```

#### Loading Different File Types

```javascript
// Load HTML template
const template = loadPartial("./views/template.html");

// Load EJS file
const ejsContent = loadPartial("./views/page.ejs");

// Load Handlebars template
const hbsContent = loadPartial("./views/component.hbs");

// Load plain text
const textContent = loadPartial("./content/article.txt");

// Load Markdown
const mdContent = loadPartial("./docs/readme.md");
```

#### Using with Path Module

```javascript
const path = require("path");

const content = loadPartial(
  path.join(__dirname, "./views/docs/getting-started.html")
);
```

#### Loading Partials into Constructos

```javascript
const header = loadPartial("./views/partials/header.html");
const footer = loadPartial("./views/partials/footer.html");
const content = loadPartial("./views/content.html");

const html = new $html({
  header,
  content,
  footer,
}).constructoString();
```

#### Dynamic File Loading

```javascript
app.get("/docs/:page", (req, res) => {
  const pageName = req.params.page;
  const content = loadPartial(`./views/docs/${pageName}.html`);

  if (!content) {
    return res.status(404).send("Page not found");
  }

  res.send(content);
});
```

#### Loading Multiple Partials

```javascript
const partials = {
  navigation: loadPartial("./views/partials/nav.html"),
  sidebar: loadPartial("./views/partials/sidebar.html"),
  main: loadPartial("./views/partials/main.html"),
  footer: loadPartial("./views/partials/footer.html"),
};

const page = new $pageLayout({
  nav: partials.navigation,
  sidebar: partials.sidebar,
  content: partials.main,
  footer: partials.footer,
}).constructoString();
```

### Use Cases

- Loading HTML template files for rendering
- Reading EJS, Handlebars, or other template engine files
- Loading static content for pages
- Including reusable partial views
- Reading configuration or data files
- Loading email templates
- Reading documentation or article content

### Error Handling

The function returns an empty string if the file cannot be read. Always check the result:

```javascript
const content = loadPartial("./views/page.html");

if (!content) {
  console.error("Failed to load template");
  // Handle error appropriately
}
```

### Best Practices

1. **Use Absolute Paths**: Avoid path resolution issues by using `path.join(__dirname, ...)`

   ```javascript
   const content = loadPartial(path.join(__dirname, "./views/template.html"));
   ```

2. **Cache Frequently Used Files**: Cache templates that are loaded multiple times

   ```javascript
   const templateCache = new Map();

   function getCachedPartial(filePath) {
     if (!templateCache.has(filePath)) {
       templateCache.set(filePath, loadPartial(filePath));
     }
     return templateCache.get(filePath);
   }
   ```

3. **Validate File Existence**: Check if content was loaded successfully

   ```javascript
   const content = loadPartial(filePath);
   if (!content) {
     throw new Error(`Template not found: ${filePath}`);
   }
   ```

4. **Organize Templates**: Keep templates in a dedicated directory structure
   ```
   views/
   ├── layouts/
   ├── partials/
   ├── pages/
   └── components/
   ```

---

## escapeHTML()

Escapes HTML special characters in a string to prevent HTML injection and to display HTML markup as plain text in the browser. This is crucial for security and for displaying code examples.

### Syntax

```typescript
escapeHTML(html: string): string
```

### Parameters

| Parameter | Type     | Required | Description                          |
| --------- | -------- | -------- | ------------------------------------ |
| `html`    | `string` | Yes      | The string containing HTML to escape |

### Return Value

- **Type:** `string`
- **Description:** The input string with HTML special characters converted to their HTML entity equivalents.

### Character Conversions

| Character | HTML Entity | Description               |
| --------- | ----------- | ------------------------- |
| `<`       | `&lt;`      | Less than                 |
| `>`       | `&gt;`      | Greater than              |
| `&`       | `&amp;`     | Ampersand                 |
| `"`       | `&quot;`    | Double quote              |
| `'`       | `&#39;`     | Single quote (apostrophe) |

### Examples

#### Basic Usage

```javascript
const escaped = escapeHTML("<div>Hello World</div>");
console.log(escaped);
// Output: "&lt;div&gt;Hello World&lt;/div&gt;"
```

#### Displaying Code Examples

```javascript
const codeSnippet = `
<button onclick="handleClick()">
  Click me
</button>
`;

const escaped = escapeHTML(codeSnippet);

const html = new $codeBlock({
  code: escaped,
}).constructoString();
```

#### Preventing XSS Attacks

```javascript
app.post("/comment", (req, res) => {
  const userComment = req.body.comment;

  // Escape user input to prevent XSS
  const safeComment = escapeHTML(userComment);

  const commentHTML = new $comment({
    text: safeComment,
    author: escapeHTML(req.body.author),
  }).constructoString();

  res.send(commentHTML);
});
```

#### Escaping Multiple Strings

```javascript
const userInput = {
  name: escapeHTML(req.body.name),
  bio: escapeHTML(req.body.bio),
  website: escapeHTML(req.body.website),
};

const profile = new $userProfile(userInput).constructoString();
```

#### Displaying HTML in Documentation

```javascript
const htmlExample = escapeHTML(`
<winnetou>
  <div id="[[myDiv]]">
    {{content}}
  </div>
</winnetou>
`);

const docPage = new $documentationPage({
  title: "Constructo Examples",
  codeExample: htmlExample,
}).constructoString();
```

#### Escaping All Characters

```javascript
const complexHTML = '<script>alert("XSS")</script>';
const escaped = escapeHTML(complexHTML);
console.log(escaped);
// Output: "&lt;script&gt;alert(&quot;XSS&quot;)&lt;/script&gt;"
```

#### Using in API Responses

```javascript
app.get("/api/snippet", (req, res) => {
  const snippet = `
    <div class="container">
      <h1>Title</h1>
      <p>Content here</p>
    </div>
  `;

  res.json({
    code: escapeHTML(snippet),
    language: "html",
  });
});
```

### Use Cases

- **Security**: Sanitize user-generated content to prevent XSS attacks
- **Code Display**: Show HTML, XML, or other markup code as text
- **Documentation**: Display code examples in tutorials and docs
- **Debugging**: Show HTML structure without rendering it
- **Email Content**: Safely include user input in emails
- **Form Validation**: Display validation error messages safely
- **API Responses**: Return HTML code as JSON data

### Security Considerations

1. **Always Escape User Input**: Never trust user-provided data

   ```javascript
   // Bad
   const html = `<div>${userInput}</div>`;

   // Good
   const html = `<div>${escapeHTML(userInput)}</div>`;
   ```

2. **Escape Before Storage or Display**: Escape when rendering, not when storing

   ```javascript
   // When displaying
   const displayText = escapeHTML(storedUserInput);
   ```

3. **Don't Double-Escape**: Avoid escaping already escaped content
   ```javascript
   const text = "Hello <World>";
   const escaped = escapeHTML(text);
   // Don't do: escapeHTML(escaped)
   ```

### Best Practices

1. **Escape All User Input**: When displaying user-generated content

   ```javascript
   const safeContent = escapeHTML(userGeneratedContent);
   ```

2. **Use for Code Examples**: Display code snippets properly

   ```javascript
   new $codeBlock({
     code: escapeHTML(codeSnippet),
     language: "html",
   }).create("#examples");
   ```

3. **Combine with Validation**: Validate input, then escape for display
   ```javascript
   if (isValid(userInput)) {
     const safe = escapeHTML(userInput);
     // Use safe content
   }
   ```

---

## joinConstructos()

Flattens and joins multiple parts (strings, arrays, or nested structures) into a single HTML string. This function is particularly useful for combining multiple constructos or HTML fragments in server-side rendering.

### Syntax

```typescript
joinConstructos(...parts: any[]): string
```

### Parameters

| Parameter  | Type    | Required | Description                                                                           |
| ---------- | ------- | -------- | ------------------------------------------------------------------------------------- |
| `...parts` | `any[]` | Yes      | Variable number of arguments including strings, arrays, or nested arrays to be joined |

### Return Value

- **Type:** `string`
- **Description:** A single concatenated string with all parts joined together. Nested arrays are automatically flattened.

### Examples

#### Basic Usage

```javascript
const combined = joinConstructos(
  "<header>Header</header>",
  "<main>Content</main>",
  "<footer>Footer</footer>"
);

console.log(combined);
// Output: <header>Header</header><main>Content</main><footer>Footer</footer>
```

#### Combining Constructos

```javascript
const pageHTML = joinConstructos(
  new $header({ title: "Welcome" }).constructoString(),
  new $content({ text: "Main content here" }).constructoString(),
  new $footer({ year: 2024 }).constructoString()
);

res.send(pageHTML);
```

#### Joining Arrays of Constructos

```javascript
const menuItems = [
  new $menuItem({ text: "Home", href: "/" }).constructoString(),
  new $menuItem({ text: "About", href: "/about" }).constructoString(),
  new $menuItem({ text: "Contact", href: "/contact" }).constructoString(),
];

const menu = joinConstructos(...menuItems);
```

#### Nested Arrays (Auto-Flattening)

```javascript
const header = new $header().constructoString();
const sidebar = new $sidebar().constructoString();
const content = new $content().constructoString();
const aside = new $aside().constructoString();
const footer = new $footer().constructoString();

// Nested arrays are automatically flattened
const page = joinConstructos(header, [sidebar, [content, aside]], footer);
```

#### Building Complex Layouts

```javascript
const sections = [
  new $hero({ title: "Welcome" }).constructoString(),
  new $features({ items: featureList }).constructoString(),
  new $testimonials({ reviews }).constructoString(),
  new $cta({ text: "Get Started" }).constructoString(),
];

const landingPage = joinConstructos(
  new $navbar().constructoString(),
  sections,
  new $footer().constructoString()
);
```

#### Dynamic Content Generation

```javascript
app.get("/products", async (req, res) => {
  const products = await getProducts();

  const productCards = products.map(product =>
    new $productCard({
      name: product.name,
      price: product.price,
      image: product.image,
    }).constructoString()
  );

  const pageHTML = joinConstructos(
    new $header({ title: "Products" }).constructoString(),
    new $productGrid({
      cards: joinConstructos(...productCards),
    }).constructoString(),
    new $footer().constructoString()
  );

  res.send(pageHTML);
});
```

#### Email Template Generation

```javascript
const emailHTML = joinConstructos(
  new $emailHeader({
    logo: "https://example.com/logo.png",
  }).constructoString(),
  new $emailBody({
    greeting: "Hello User",
    message: "Thank you for signing up!",
  }).constructoString(),
  new $emailFooter({
    unsubscribeLink: "https://example.com/unsubscribe",
    year: new Date().getFullYear(),
  }).constructoString()
);

sendEmail(recipientEmail, emailHTML);
```

#### Conditional Parts

```javascript
const pageHTML = joinConstructos(
  new $header().constructoString(),
  isLoggedIn
    ? new $userDashboard().constructoString()
    : new $loginForm().constructoString(),
  showAds ? new $adBanner().constructoString() : "",
  new $footer().constructoString()
);
```

#### Combining with loadPartial

```javascript
const pageHTML = joinConstructos(
  loadPartial("./views/partials/header.html"),
  new $content({ text: pageContent }).constructoString(),
  loadPartial("./views/partials/footer.html")
);
```

#### Multiple Sections with Separators

```javascript
const articles = articlesData.map(article =>
  new $article({
    title: article.title,
    content: article.content,
  }).constructoString()
);

const blogPage = joinConstructos(
  new $blogHeader().constructoString(),
  ...articles.flatMap(article => [article, "<hr>"]),
  new $blogFooter().constructoString()
);
```

#### Building Navigation Menus

```javascript
const menuSections = [
  {
    title: "Getting Started",
    items: ["Installation", "Quick Start", "Tutorial"],
  },
  {
    title: "API",
    items: ["Core API", "Router", "Translations"],
  },
];

const menu = joinConstructos(
  ...menuSections.map(section =>
    joinConstructos(
      new $menuTitle({ title: section.title }).constructoString(),
      ...section.items.map(item =>
        new $menuItem({ text: item }).constructoString()
      )
    )
  )
);
```

### Use Cases

- **Page Assembly**: Combine header, content, and footer
- **Component Composition**: Build complex components from smaller ones
- **List Generation**: Join multiple list items or cards
- **Email Templates**: Assemble email HTML from parts
- **Dynamic Layouts**: Conditionally include sections
- **Menu Generation**: Build navigation from multiple items
- **Report Generation**: Combine multiple data sections
- **API Responses**: Build HTML responses from fragments

### Array Handling

The function automatically flattens nested arrays:

```javascript
// These are equivalent:
joinConstructos("a", "b", "c");
joinConstructos(["a", "b", "c"]);
joinConstructos("a", ["b", "c"]);
joinConstructos(["a", ["b", ["c"]]]);

// All output: "abc"
```

### Performance Considerations

1. **Efficient String Concatenation**: The function efficiently handles large numbers of parts
2. **Memory Usage**: Be mindful when joining very large arrays
3. **Pre-computation**: Consider caching frequently used combinations

```javascript
// Cache static parts
const cachedLayout = joinConstructos(
  staticHeader,
  "{{DYNAMIC_CONTENT}}",
  staticFooter
);

// Replace dynamic part
const page = cachedLayout.replace("{{DYNAMIC_CONTENT}}", dynamicContent);
```

### Best Practices

1. **Organize Parts Logically**: Group related constructos together

   ```javascript
   const pageHTML = joinConstructos(
     // Header section
     navbar,
     hero,

     // Main content
     content,
     sidebar,

     // Footer section
     footer
   );
   ```

2. **Use Spread Operator for Arrays**: Spread arrays when joining

   ```javascript
   const items = [item1, item2, item3];
   const result = joinConstructos(...items);
   ```

3. **Handle Empty Arrays**: joinConstructos handles empty arrays gracefully

   ```javascript
   const items =
     products.length > 0
       ? products.map(p => new $productCard(p).constructoString())
       : [new $emptyState().constructoString()];

   const html = joinConstructos(...items);
   ```

4. **Combine with Other SSR Functions**: Use together with `loadPartial` and `escapeHTML`
   ```javascript
   const page = joinConstructos(
     loadPartial("./header.html"),
     new $content({ text: escapeHTML(userContent) }).constructoString(),
     loadPartial("./footer.html")
   );
   ```

---

## Constructo Methods for SSR

When using constructos in server-side rendering, you'll use the `.constructoString()` method instead of `.create()`.

### constructoString()

Returns the HTML string representation of a constructo without attaching it to the DOM.

#### Syntax

```typescript
constructoString(): string
```

#### Example

```javascript
import { $myComponent } from "./components.wcto";

// Client-side (attaches to DOM)
new $myComponent({ text: "Hello" }).create("#app");

// Server-side (returns HTML string)
const html = new $myComponent({ text: "Hello" }).constructoString();
```

#### Complete SSR Example

```javascript
const express = require("express");
const { joinConstructos, loadPartial } = require("winnetoujs/modules/ssr");
const { $html, $navbar, $footer } = require("./winnetou-ssr/dist/app.js");

const app = express();

app.get("/page", (req, res) => {
  const content = loadPartial("./views/page.html");

  const html = new $html({
    title: "My Page",
    navbar: new $navbar({ items: menuItems }).constructoString(),
    content: content,
    footer: new $footer({ year: 2024 }).constructoString(),
  }).constructoString();

  res.send(html);
});

app.listen(3000);
```

---

## Type Definitions

For TypeScript users, the SSR module exports the following types:

```typescript
/**
 * Loads content from a file
 * @param filePath - Path to the file to load
 * @returns The file content as a string
 */
export function loadPartial(filePath: string): string;

/**
 * Escapes HTML special characters
 * @param html - The HTML string to escape
 * @returns The escaped HTML string
 */
export function escapeHTML(html: string): string;

/**
 * Joins multiple parts into a single string
 * @param parts - Variable number of parts to join
 * @returns The joined string
 */
export function joinConstructos(...parts: any[]): string;
```

---

## Complete API Reference

### Summary Table

| Function              | Purpose                | Input              | Output                |
| --------------------- | ---------------------- | ------------------ | --------------------- |
| `loadPartial()`       | Load file content      | File path (string) | File content (string) |
| `escapeHTML()`        | Escape HTML characters | HTML string        | Escaped HTML string   |
| `joinConstructos()`   | Join multiple parts    | Variable parts     | Single string         |
| `.constructoString()` | Get constructo HTML    | None (method)      | HTML string           |

### Module Availability

The SSR module is available when compiling with:

- `-n` or `--node` flag (CommonJS format)
- `-e` or `--node-esm` flag (ES Module format)

### Browser Compatibility

⚠️ **Important**: The SSR module is designed for **Node.js server environments only**. It will not work in browser environments as it requires Node.js file system access.

For browser-side rendering, use the standard `.create()` method instead of `.constructoString()`.

---

## Common Patterns

### Pattern 1: Complete Page Rendering

```javascript
app.get("/page/:id", async (req, res) => {
  const pageData = await getPageData(req.params.id);

  const html = new $html({
    title: pageData.title,
    description: pageData.description,
    content: joinConstructos(
      new $header({ title: pageData.title }).constructoString(),
      loadPartial(`./views/pages/${req.params.id}.html`),
      new $footer({ year: new Date().getFullYear() }).constructoString()
    ),
  }).constructoString();

  res.send(html);
});
```

### Pattern 2: Rendering Lists

```javascript
const items = data.map(item =>
  new $listItem({
    title: escapeHTML(item.title),
    description: escapeHTML(item.description),
  }).constructoString()
);

const list = new $list({
  items: joinConstructos(...items),
}).constructoString();
```

### Pattern 3: Conditional Rendering

```javascript
const content = isAuthenticated
  ? new $userDashboard({ user }).constructoString()
  : new $loginPrompt().constructoString();

const page = new $html({
  content,
  navbar: new $navbar({ isAuth: isAuthenticated }).constructoString(),
}).constructoString();
```

### Pattern 4: Template Caching

```javascript
const cache = new Map();

function renderPage(template, data) {
  const key = `${template}-${JSON.stringify(data)}`;

  if (!cache.has(key)) {
    const html = new $page({
      content: loadPartial(`./views/${template}.html`),
      ...data,
    }).constructoString();

    cache.set(key, html);
  }

  return cache.get(key);
}
```

---

## Migration Guide

### From Client-Side to Server-Side

**Client-Side:**

```javascript
import { $myComponent } from "./components.wcto";

new $myComponent({ text: "Hello" }).create("#app");
```

**Server-Side:**

```javascript
const { $myComponent } = require("./components.wcto");

const html = new $myComponent({ text: "Hello" }).constructoString();
res.send(html);
```

### Key Differences

| Aspect      | Client-Side            | Server-Side                      |
| ----------- | ---------------------- | -------------------------------- |
| Import      | ES6 import             | CommonJS require or ES6 import   |
| Method      | `.create('#selector')` | `.constructoString()`            |
| Return      | Object with ids        | HTML string                      |
| Environment | Browser                | Node.js                          |
| Compilation | `wbr -b -p`            | `wbr -b -n -p` or `wbr -b -e -p` |

---

This API reference provides complete documentation for all SSR utilities available in WinnetouJs. Use these functions to build high-performance, SEO-friendly server-rendered applications.
