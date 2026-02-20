# Server Side Rendering (SSR)

WinnetouJs provides a powerful module for server-side rendering, allowing you to generate HTML on the server before sending it to the client. This approach improves initial page load performance, SEO, and enables your application to work without JavaScript enabled.

## Overview

Server-side rendering with WinnetouJs works by compiling your constructos in a Node.js environment, where they can be used to generate HTML strings that are sent to the client. This is particularly useful for:

- **Improved SEO**: Search engines can crawl fully-rendered HTML pages
- **Faster Initial Load**: Users see content immediately without waiting for JavaScript to execute
- **Better Performance**: Reduced client-side processing on initial page load
- **Progressive Enhancement**: Your application works even with JavaScript disabled

## WBR Compiler Options for SSR

WinnetouJs provides specific compiler flags for Node.js environments:

### CommonJS Format

Use the `-n` or `--node` flag to compile for Node.js using CommonJS format:

```bash
node wbr -b -n -p
```

This generates code using `module.exports` and `require()`.

### ES Module Format

Use the `-e` or `--node-esm` flag to compile for Node.js using ES Module format:

```bash
node wbr -b -e -p
```

This generates code using `export` and `import` statements.

### Compiler Flags Explained

- `-b`: Bundle the application
- `-n`: Compile for Node.js environment (CommonJS)
- `-e`: Compile for Node.js environment (ESM)
- `-p`: Production mode (minified and optimized)

## Server Configuration

### TypeScript/JavaScript Configuration

Your server's `tsconfig.json` or `jsconfig.json` must include `allowJs: true` to import the compiled `.wcto.js` files:

```json
{
  "compilerOptions": {
    "module": "nodenext",
    "target": "es2022",
    "checkJs": true,
    "allowJs": true,
    "paths": {
      "@libs/*": ["./libs/*"]
    }
  },
  "exclude": ["node_modules", "**/node_modules/*"]
}
```

The `allowJs` option is essential for importing WinnetouJs constructos in your Node.js server.

In `win.config.json`, ensure you specify the correct output directory and source folder for your constructos:

```json
{
  "apps": ["./src/app.ts"],
  "outputDir": "./dist",
  "constructosSourceFolder": "./src"
}
```

In `package.json`, include the necessary dependencies for your server:

```json
{
  "scripts": {
    "winnetou": "node wbr -b -n -v -w",
    "sass:prod": "sass sass/main.scss:dist/css/main.min.css --style compressed --no-source-map --quiet --load-path='./src'",
    "types": "tsc src/app.ts --declaration --emitDeclarationOnly --outDir dist --module nodenext --target es2022"
  },
  "dependencies": {
    "typescript": "^4.9.5",
    "express": "^4.18.2",
    "winnetoujs": "^3.0.0"
  },
  "devDependencies": {
    "@types/express": "^4.17.13",
    "@types/node": "^18.7.18"
  }
}
```

> [!IMPORTANT]
> ⚠️ **WARNING**: In order to use suggestions when importing constructos, you must have `typescript` installed as a dependency in your project and run `npm run types` to generate the declaration files in the `dist` folder. This allows your server code to have proper type definitions for the imported constructos. Ensure `dist` is the same as the `outputDir` specified in your `win.config.json`. If suggestions are not working, restart your code editor after running the types generation command.

## Project Structure

Create a dedicated folder for your SSR components, typically named `winnetou-ssr`:

```
your-project/
├── winnetou-ssr/
│   ├── src/
│   │   ├── app.ts
│   │   ├── docs-template.wcto.html
│   │   └── components.wcto.html
│   ├── dist/
│   │   └── app.js
│   ├── win.config.json
│   └── package.json
├── server/
│   ├── index.js
│   └── views/
├── package.json
└── README.md
```

## Setting Up SSR

### Step 1: Create WinnetouJs SSR Project

Inside the `winnetou-ssr` folder, set up a standard WinnetouJs project with your constructos.

Create `win.config.json`:

```json
{
  "apps": ["./src/app.ts"],
  "outputDir": "./dist",
  "constructosSourceFolder": "./src"
}
```

### Step 2: Create Constructos

Create your constructos in `.wcto.html` files as usual:

**docs-template.wcto.html:**

```html
<winnetou description="Main HTML template">
  <html id="[[html]]" lang="en">
    <head>
      <meta charset="UTF-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <title>{{metaTitle:string}}</title>
      <meta name="description" content="{{metaDescription:string}}" />
      <link rel="canonical" href="{{canonicalPath:string}}" />
    </head>
    <body>
      {{navbar:string}}
      <main>{{content:string}}</main>
      {{footer:string}}
    </body>
  </html>
</winnetou>

<winnetou description="Navigation bar">
  <nav id="[[navbar]]" class="navbar">{{menu:string}}</nav>
</winnetou>

<winnetou description="Menu item">
  <a id="[[menuItem]]" href="{{href:string}}" class="{{class?:string}}">
    {{text:string}}
  </a>
</winnetou>

<winnetou description="Footer component">
  <footer id="[[footer]]" class="footer">
    <p>© {{currentYear:number}} All rights reserved</p>
    <a href="{{editLink:string}}" target="_blank">
      {{iconExternalLink:string}} Edit this page
    </a>
  </footer>
</winnetou>
```

### Step 3: Export Constructos and SSR Utilities

Create `app.ts` to import and export your constructos along with SSR utilities:

```typescript
import {
  joinConstructos,
  escapeHTML,
  loadPartial,
} from "winnetoujs/modules/ssr";

import { $html, $navbar, $menuItem, $footer } from "./docs-template.wcto";

export {
  joinConstructos,
  escapeHTML,
  loadPartial,
  $html,
  $navbar,
  $menuItem,
  $footer,
};
```

### Step 4: Compile for Node.js

Compile your WinnetouJs project for Node.js:

```bash
cd winnetou-ssr
wbr -b -n -p
```

Or for ESM:

```bash
wbr -b -e -p
```

## Using SSR in Your Server

### Example with Express.js (CommonJS)

```javascript
const express = require("express");
const path = require("path");

// Import compiled WinnetouJs constructos
const {
  joinConstructos,
  loadPartial,
  escapeHTML,
  $html,
  $navbar,
  $menuItem,
  $footer,
} = require("./winnetou-ssr/dist/app.js");

const app = express();

// Serve static files
app.use(express.static("public"));

// Home route
app.get("/", (req, res) => {
  res.send(loadPartial("./views/home/home.html"));
});

// Documentation route
app.get("/docs/:route", async (req, res) => {
  const routeName = req.params.route;

  // Load content from file
  const content = loadPartial(
    path.join(__dirname, `./views/docs/${routeName}.html`),
  );

  // Check if file exists
  if (!content) {
    return res.status(404).send("Not found");
  }

  // Build menu
  const menuItems = [
    { href: "/docs/getting-started", text: "Getting Started" },
    { href: "/docs/constructos", text: "Constructos" },
    { href: "/docs/routing", text: "Routing" },
  ];

  const menuHTML = menuItems
    .map((item) =>
      new $menuItem({
        href: item.href,
        text: item.text,
        class: item.href.includes(routeName) ? "active" : "",
      }).constructoString(),
    )
    .join("");

  const navbar = new $navbar({
    menu: menuHTML,
  }).constructoString();

  const footer = new $footer({
    currentYear: new Date().getFullYear(),
    editLink: `https://github.com/yourrepo/edit/main/views/docs/${routeName}.html`,
    iconExternalLink: "<svg>...</svg>",
  }).constructoString();

  // Generate complete HTML
  const html = new $html({
    content,
    canonicalPath: `/docs/${routeName}`,
    metaDescription: content.substring(0, 155).replace(/\n/g, " "),
    metaTitle: `${routeName} - Documentation`,
    navbar,
    footer,
  }).constructoString();

  res.send(html);
});

app.listen(3000, () => {
  console.log("Server running on http://localhost:3000");
});
```

### Example with Express.js (ESM)

```javascript
import express from "express";
import path from "path";
import { fileURLToPath } from "url";

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

// Import compiled WinnetouJs constructos
import {
  joinConstructos,
  loadPartial,
  escapeHTML,
  $html,
  $navbar,
  $footer,
} from "./winnetou-ssr/dist/app.js";

const app = express();

app.get("/docs/:route", async (req, res) => {
  const content = loadPartial(
    path.join(__dirname, `./views/docs/${req.params.route}.html`),
  );

  const html = new $html({
    content,
    metaTitle: `${req.params.route} - Docs`,
    metaDescription: "Documentation page",
    canonicalPath: `/docs/${req.params.route}`,
    navbar: new $navbar({ menu: "..." }).constructoString(),
    footer: new $footer({ currentYear: 2024 }).constructoString(),
  }).constructoString();

  res.send(html);
});

app.listen(3000);
```

## SSR Utility Functions

WinnetouJs provides three essential utility functions for server-side rendering:

### loadPartial()

Loads and returns the content of a file from the server's file system. Useful for loading HTML templates, partials, or any text-based files.

It use cache to avoid multiple file reads for the same file.

**Syntax:**

```javascript
loadPartial(filePath: string): string
```

**Example:**

```javascript
const content = loadPartial("./views/home/home.html");
const template = loadPartial("./views/partials/header.html");
const textContent = loadPartial("./views/article.txt");
```

**Use Cases:**

- Load HTML template files
- Load EJS, Handlebars, or other template files
- Load text content for rendering
- Include partial views in your pages

```javascript
let html = new $html({
  content: loadPartial("./views/content.html"),
  header: loadPartial("./views/partials/header.html"),
}).constructoString();
```

### escapeHTML()

Escapes HTML special characters to prevent HTML injection and to display HTML code as plain text in the browser.

**Syntax:**

```javascript
escapeHTML(html: string): string
```

**Example:**

```javascript
const escaped = escapeHTML("<div>Hello World</div>");
// Returns: "&lt;div&gt;Hello World&lt;/div&gt;"
```

**Character Conversions:**

- `<` → `&lt;`
- `>` → `&gt;`
- `&` → `&amp;`
- `"` → `&quot;`
- `'` → `&#39;`

**Use Cases:**

- Display code examples on documentation pages
- Show HTML markup as text
- Prevent XSS attacks
- Render user-generated content safely

```javascript
const codeExample = escapeHTML('<button onclick="alert()">Click</button>');
const html = new $codeBlock({
  code: codeExample,
}).constructoString();
```

### joinConstructos()

Flattens and joins multiple parts (strings, arrays, or nested structures) into a single HTML string. Particularly useful for combining multiple constructos or HTML fragments.

**Syntax:**

```javascript
joinConstructos(...parts: any[]): string
```

**Example:**

```javascript
const combinedHTML = joinConstructos(
  new $header({ title: "Welcome" }).constructoString(),
  new $content({ text: "Main content" }).constructoString(),
  new $footer({ year: 2024 }).constructoString(),
);
```

**Advanced Usage:**

```javascript
// Combine arrays and strings
const sections = [
  new $section1().constructoString(),
  new $section2().constructoString(),
];

const page = joinConstructos(
  "<header>Header</header>",
  sections,
  "<footer>Footer</footer>",
);

// Nested arrays are automatically flattened
const components = joinConstructos(header, [sidebar, [content, aside]], footer);
```

**Use Cases:**

- Combine multiple constructos into a single response
- Build complex page layouts
- Merge partial templates
- Generate email templates

```javascript
const emailHTML = joinConstructos(
  new $emailHeader({ logo: logoURL }).constructoString(),
  new $emailBody({ content: message }).constructoString(),
  new $emailFooter({ unsubscribeLink }).constructoString(),
);

sendEmail(emailHTML);
```

## Working with Dynamic Content

### Generating Lists

```javascript
app.get("/products", async (req, res) => {
  const products = await getProductsFromDatabase();

  const productCards = products
    .map((product) =>
      new $productCard({
        name: product.name,
        price: product.price,
        image: product.image,
      }).constructoString(),
    )
    .join("");

  const html = new $html({
    content: productCards,
    metaTitle: "Products",
    metaDescription: "Browse our products",
    canonicalPath: "/products",
  }).constructoString();

  res.send(html);
});
```

### Conditional Rendering

```javascript
app.get("/user/:id", async (req, res) => {
  const user = await getUserById(req.params.id);

  const profileContent = user
    ? new $userProfile({
        name: user.name,
        email: user.email,
        avatar: user.avatar,
      }).constructoString()
    : new $notFound({
        message: "User not found",
      }).constructoString();

  const html = new $html({
    content: profileContent,
    metaTitle: user ? user.name : "User Not Found",
    metaDescription: user ? `Profile of ${user.name}` : "User not found",
    canonicalPath: `/user/${req.params.id}`,
  }).constructoString();

  res.send(html);
});
```

### Nested Constructos

```javascript
const card = new $card({
  header: new $cardHeader({
    title: "Product Name",
    icon: new $icon({ name: "star" }).constructoString(),
  }).constructoString(),
  body: new $cardBody({
    description: "Product description here",
  }).constructoString(),
  footer: new $cardFooter({
    price: "$99.99",
  }).constructoString(),
}).constructoString();
```

## Complete Server Example

Here's a comprehensive example showing all SSR features together:

```javascript
const express = require("express");
const path = require("path");

const {
  joinConstructos,
  loadPartial,
  escapeHTML,
  $html,
  $navbar,
  $menuItem,
  $menuTitle,
  $footer,
  $searchCard,
} = require("./winnetou-ssr/dist/app.js");

const app = express();
const PORT = 3000;

// Helper function to remove HTML tags
function removeHTMLTags(str) {
  return str.replace(/<[^>]*>/g, "");
}

// Helper to get menu structure
async function getMenu() {
  return [
    {
      folder: "Getting Started",
      files: [
        {
          name: "what-is-winnetoujs",
          displayName: "What is WinnetouJs",
          url: "...",
        },
        { name: "your-first-app", displayName: "Your First App", url: "..." },
      ],
    },
    {
      folder: "Advanced",
      files: [
        { name: "ssr", displayName: "Server Side Rendering", url: "..." },
      ],
    },
  ];
}

// Helper to render menu
async function renderMenu(currentFolder, currentFile) {
  const menu = await getMenu();
  let nextLink = null;
  let foundCurrent = false;

  const renderedMenu = menu
    .map((folder) => {
      const items = folder.files
        .map((file) => {
          const isActive =
            folder.folder === currentFolder && file.name === currentFile;

          if (foundCurrent && !nextLink) {
            nextLink = `/docs/${file.name}`;
          }

          if (isActive) {
            foundCurrent = true;
          }

          return new $menuItem({
            href: `/docs/${file.name}`,
            text: file.displayName,
            class: isActive ? "active" : "",
          }).constructoString();
        })
        .join("");

      return joinConstructos(
        new $menuTitle({ title: folder.folder }).constructoString(),
        items,
      );
    })
    .join("");

  return { ssr: renderedMenu, next: nextLink };
}

// Routes
app.get("/", (req, res) => {
  res.send(loadPartial("./views/home/home.html"));
});

app.get("/docs/:route", async (req, res) => {
  const menu = await getMenu();
  let fileFound = false;

  for (const folder of menu) {
    for (const file of folder.files) {
      if (file.name === req.params.route) {
        fileFound = true;

        const renderedMenu = await renderMenu(folder.folder, file.name);

        const navbar = new $navbar({
          menu: renderedMenu.ssr,
        }).constructoString();

        const content = loadPartial(
          path.join(__dirname, `./views/docs/${req.params.route}.html`),
        );

        const footer = new $footer({
          currentYear: new Date().getFullYear(),
          editLink: file.url,
          iconExternalLink: "<svg>...</svg>",
          iconArrowRight: "<svg>...</svg>",
          nextLink: renderedMenu.next ?? "/docs",
          nextText:
            renderedMenu.next?.replace("/docs/", "").replace(/-/g, " ") ??
            "Back to start",
        }).constructoString();

        const html = new $html({
          content,
          canonicalPath: `/docs/${req.params.route}`,
          metaDescription: removeHTMLTags(content.substring(0, 155)).replace(
            /\n/g,
            " ",
          ),
          metaTitle: `${file.name} - WinnetouJs`,
          title: `Documentation - ${file.name}`,
          folder: folder.folder,
          menu: renderedMenu.ssr,
          navbar,
          path: file.displayName,
          footer,
        }).constructoString();

        return res.send(html);
      }
    }
  }

  if (!fileFound) {
    return res.status(404).send("Not found");
  }
});

app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

## Best Practices

### 1. Separate SSR and Client Code

Keep your SSR constructos in a dedicated folder (`winnetou-ssr`) separate from client-side code.

### 2. Use Type Safety

Enable `allowJs` and `checkJs` in your configuration for better type checking:

```json
{
  "compilerOptions": {
    "checkJs": true,
    "allowJs": true
  }
}
```

### 3. Cache Compiled Templates

For better performance, cache your compiled constructos:

```javascript
const templateCache = new Map();

function getTemplate(name) {
  if (!templateCache.has(name)) {
    const content = loadPartial(`./views/${name}.html`);
    templateCache.set(name, content);
  }
  return templateCache.get(name);
}
```

### 4. Handle Errors Gracefully

```javascript
app.get("/docs/:route", async (req, res) => {
  try {
    const content = loadPartial(`./views/docs/${req.params.route}.html`);

    if (!content) {
      return res
        .status(404)
        .send(new $notFound({ message: "Page not found" }).constructoString());
    }

    // ... render page
  } catch (error) {
    console.error("Error rendering page:", error);
    res.status(500).send("Internal Server Error");
  }
});
```

### 5. Optimize Meta Tags for SEO

```javascript
const html = new $html({
  metaTitle: `${pageTitle} | Your Site`,
  metaDescription: cleanDescription.substring(0, 160),
  canonicalPath: req.path,
  ogImage: "/images/og-image.jpg",
  ogType: "article",
}).constructoString();
```

### 6. Use joinConstructos for Complex Layouts

```javascript
const pageHTML = joinConstructos(
  new $header().constructoString(),
  new $sidebar().constructoString(),
  new $mainContent().constructoString(),
  new $footer().constructoString(),
);
```

## Performance Considerations

### Minimize File I/O

Cache frequently accessed files in memory:

```javascript
const fileCache = {};

function loadCached(filePath) {
  if (!fileCache[filePath]) {
    fileCache[filePath] = loadPartial(filePath);
  }
  return fileCache[filePath];
}
```

### Stream Large Responses

For large pages, consider streaming the response:

```javascript
app.get("/large-page", (req, res) => {
  res.write(new $header().constructoString());

  // Stream large content in chunks
  const chunks = getLargeContent();
  chunks.forEach((chunk) => {
    res.write(new $contentChunk({ content: chunk }).constructoString());
  });

  res.write(new $footer().constructoString());
  res.end();
});
```

### Compress Responses

Use compression middleware:

```javascript
const compression = require("compression");
app.use(compression());
```

## Troubleshooting

### Module Import Errors

**Problem:** Cannot import constructos in Node.js

**Solution:** Ensure `allowJs: true` is set in your `tsconfig.json` or `jsconfig.json`

### Constructo Not Found

**Problem:** Constructo returns undefined or null

**Solution:** Verify the export statement in your `app.ts` includes the constructo

### File Not Found

**Problem:** `loadPartial` returns empty or throws error

**Solution:** Use absolute paths with `path.join(__dirname, ...)`

### Styling Issues

**Problem:** Styles not applied in SSR

**Solution:** Include CSS links in your HTML constructo head section

```html
<winnetou>
  <html id="[[html]]">
    <head>
      <link rel="stylesheet" href="/css/main.css" />
    </head>
    <body>
      {{content}}
    </body>
  </html>
</winnetou>
```

## Advantages of WinnetouJs SSR

1. **Component Reusability**: Use the same constructos on both client and server
2. **SEO Optimization**: Serve fully-rendered HTML to search engines
3. **Performance**: Faster initial page loads
4. **Type Safety**: Full TypeScript/JSDoc support
5. **Developer Experience**: Familiar WinnetouJs syntax and patterns
6. **Flexibility**: Works with any Node.js server framework
7. **Progressive Enhancement**: Applications work without JavaScript

By leveraging WinnetouJs SSR, you can build high-performance, SEO-friendly web applications while maintaining the developer experience and component architecture you're familiar with from client-side WinnetouJs development.
