# WBR: Winnetou Bundle Runtime

## Overview

WBR (Winnetou Bundle Runtime) is the powerful build tool at the heart of WinnetouJs. It handles two critical tasks: compiling constructos (`.wcto.html` files) into JavaScript classes and bundling your entire application into optimized, production-ready files.

**Key Features:**

- ⚡ Ultra-fast compilation powered by esbuild
- 🔄 Watch mode for instant development feedback
- 📦 Automatic tree-shaking for minimal bundle size
- 🎯 TypeScript and JavaScript support
- 🔧 Simple configuration with `win.config.json`
- 🚀 Production-optimized builds
- 📊 Verbose output for debugging
- 🎨 Works seamlessly with Sass and other tools

## How WBR Works

WBR performs two main operations:

1. **Constructo Compilation**: Transpiles `.wcto.html` files into `.wcto.js` files containing reusable JavaScript classes
2. **Application Bundling**: Bundles your JavaScript/TypeScript code with esbuild into optimized output files

**The WBR Workflow:**

```
┌─────────────────┐
│  .wcto.html     │  ──┐
│  (Constructos)  │    │
└─────────────────┘    │
                       │  WBR Compilation
┌─────────────────┐    │
│  .ts/.js        │  ──┤
│  (Your Code)    │    │
└─────────────────┘    │
                       ▼
                 ┌──────────────┐
                 │  .wcto.js    │
                 │  (Generated) │
                 └──────────────┘
                       │
                       │  esbuild Bundling
                       ▼
                 ┌──────────────┐
                 │  Bundle.js   │
                 │  (Optimized) │
                 └──────────────┘
```

## Installation

WBR comes included with WinnetouJs. When you install WinnetouJs, the WBR script is automatically available:

```bash
npm install winnetoujs
```

After installation, you'll have a `wbr.js` file in your project root (or you can access it via `node_modules/.bin/wbr`).

## Configuration

### 1. Create win.config.json

Create a `win.config.json` file in your project root:

**`win.config.json`**

```json
{
  "apps": ["./src/app.ts"],
  "outputDir": "./dist/js",
  "constructosSourceFolder": "./src"
}
```

**Configuration Options:**

- **`apps`** (Array of strings): Entry point files for your application(s). Supports both `.js` and `.ts` files.

  - Example: `["./src/app.ts", "./src/admin.ts"]`
  - WBR will create separate bundles for each entry point

- **`outputDir`** (String): Directory where compiled bundles will be saved

  - Example: `"./dist/js"`, `"./public/js"`, `"./build"`

- **`constructosSourceFolder`** (String): Root folder where WBR searches for `.wcto.html` files
  - Example: `"./src"`, `"./components"`, `"./app"`
  - WBR recursively searches this folder and all subdirectories

**Important Notes:**

- These settings are used **only by WBR** during build time
- They are **not** included in your application bundle
- Constructos are compiled in-place (next to their `.wcto.html` files)

### 2. Add NPM Scripts

Add WBR scripts to your `package.json` for convenient usage:

**`package.json`**

```json
{
  "scripts": {
    "dev": "node wbr -b -v -w",
    "build": "node wbr -b -p",
    "wbr:dev": "node wbr -b -v -w",
    "wbr:prod": "node wbr -b -p"
  }
}
```

**Recommended Script Setup:**

- **`dev`**: Development mode with watch and verbose output
- **`build`**: Production build with optimizations
- **`wbr:dev`**: Alternative development command
- **`wbr:prod`**: Alternative production command

## WBR Command-Line Options

WBR provides several command-line flags to control build behavior:

### Available Options

```bash
node wbr [options]
```

| Option | Alias             | Description                                   |
| ------ | ----------------- | --------------------------------------------- |
| `-b`   | `--bundleRelease` | Compile and bundle the project                |
| `-p`   | `--production`    | Production mode (minification, optimizations) |
| `-w`   | `--watch`         | Watch mode (auto-rebuild on file changes)     |
| `-v`   | `--verbose`       | Verbose output (detailed build information)   |

### Option Combinations

**Development Mode:**

```bash
node wbr -b -v -w
# or
node wbr --bundleRelease --verbose --watch
```

- Bundles the project
- Shows verbose output
- Watches for file changes
- Rebuilds automatically on changes

**Production Mode:**

```bash
node wbr -b -p
# or
node wbr --bundleRelease --production
```

- Bundles the project
- Enables production optimizations
- Minifies output
- No source maps

**Verbose Development:**

```bash
node wbr -b -v
```

- Bundles the project
- Shows detailed build information
- Useful for debugging build issues

**Simple Build:**

```bash
node wbr -b
```

- Bundles the project
- Minimal output
- Development mode (not minified)

## Using WBR in Development

### Starting Development Mode

Run WBR in watch mode for automatic rebuilding:

```bash
npm run dev
# or
npm run wbr:dev
# or
node wbr -b -v -w
```

**What happens in development mode:**

1. ✅ WBR scans `constructosSourceFolder` for `.wcto.html` files
2. ✅ Compiles all constructos to `.wcto.js` files
3. ✅ Bundles application using esbuild
4. ✅ Outputs bundle to `outputDir`
5. ✅ Watches for file changes
6. ✅ Shows verbose build information

**Console Output Example:**

```
🔧 WBR - Winnetou Bundle Runtime v3.0.0
📁 Config loaded: win.config.json
🔍 Scanning for constructos in: ./src
✅ Found 15 constructo files
🔨 Compiling constructos...
   ✓ ./src/components/header.wcto.html → header.wcto.js
   ✓ ./src/components/footer.wcto.html → footer.wcto.js
   ✓ ./src/forms/loginForm.wcto.html → loginForm.wcto.js
   ... (12 more)
📦 Bundling application...
   Entry: ./src/app.ts
   Output: ./dist/js/app.winnetouBundle.js
✨ Build completed in 234ms
👀 Watching for changes...
```

### Development Workflow

1. **Start WBR in watch mode:**

   ```bash
   npm run dev
   ```

2. **Edit your constructos or code:**

   - Modify `.wcto.html` files
   - Update `.ts` or `.js` files
   - Change styles or assets

3. **WBR auto-rebuilds:**

   - Detects file changes
   - Recompiles affected constructos
   - Rebuilds bundle
   - Shows build results

4. **Refresh browser:**
   - See changes immediately
   - No manual rebuild needed

## Production Builds

### Creating Production Bundle

Build an optimized production bundle:

```bash
npm run build
# or
npm run wbr:prod
# or
node wbr -b -p
```

**Production optimizations:**

- ✅ Code minification
- ✅ Dead code elimination (tree-shaking)
- ✅ Module deduplication
- ✅ No source maps
- ✅ Optimized file size
- ✅ Performance optimizations

**Output Example:**

```
Development bundle:  1.2 MB
Production bundle:   245 KB  (79% reduction!)
```

### Production Bundle Naming

WBR creates bundles with descriptive names:

**Development:**

```
app.winnetouBundle.js
```

**Production:**

```
app.winnetouBundle.min.js
```

### Using Production Bundle

Include the production bundle in your HTML:

**`index.html`**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>My WinnetouJs App</title>
    <link rel="stylesheet" href="/dist/css/main.css" />
  </head>
  <body>
    <div id="app"></div>

    <!-- Production bundle -->
    <script type="module" src="/dist/js/app.winnetouBundle.min.js"></script>
  </body>
</html>
```

**Important:** Always use `type="module"` for WinnetouJs bundles, as esbuild outputs ESM (ES Module) format.

## Project Structure

### Recommended Structure

```
my-winnetou-app/
├── src/
│   ├── app.ts                    # Main entry point
│   ├── components/
│   │   ├── header/
│   │   │   ├── header.wcto.html  # Constructo
│   │   │   ├── header.wcto.js    # Generated (auto)
│   │   │   ├── header.ts         # Logic
│   │   │   └── _header.scss      # Styles
│   │   ├── footer/
│   │   │   ├── footer.wcto.html
│   │   │   ├── footer.wcto.js    # Generated (auto)
│   │   │   └── footer.ts
│   ├── pages/
│   │   ├── home/
│   │   │   ├── home.wcto.html
│   │   │   ├── home.wcto.js      # Generated (auto)
│   │   │   └── home.ts
│   ├── translations/
│   │   └── en-us.ts
│   └── styles/
│       └── main.scss
├── dist/                          # Build output
│   ├── js/
│   │   └── app.winnetouBundle.min.js
│   └── css/
│       └── main.css
├── public/                        # Static assets
│   ├── index.html
│   └── translations/
│       ├── pt-br.json
│       └── es-es.json
├── win.config.json                # WBR configuration
├── package.json
├── tsconfig.json
└── wbr.js                         # WBR script
```

### Generated Files

WBR generates `.wcto.js` files alongside `.wcto.html` files:

```
components/
├── header.wcto.html    # You create this
├── header.wcto.js      # WBR generates this
└── header.ts           # You create this
```

**Hiding Generated Files:**

To avoid clutter in VS Code, add to workspace settings:

**`.vscode/settings.json`**

```json
{
  "search.exclude": {
    "**/node_modules": true,
    "**/*.wcto.js": true
  },
  "files.exclude": {
    "**/*.wcto.js": true
  }
}
```

## Multiple Entry Points

WBR supports multiple application entry points:

**`win.config.json`**

```json
{
  "apps": ["./src/app.ts", "./src/admin.ts", "./src/public-site.ts"],
  "outputDir": "./dist/js",
  "constructosSourceFolder": "./src"
}
```

**Output:**

```
dist/js/
├── app.winnetouBundle.min.js
├── admin.winnetouBundle.min.js
└── public-site.winnetouBundle.min.js
```

**Use Cases:**

- Separate bundles for admin and user interfaces
- Different bundles for different pages
- Micro-frontend architecture
- Code splitting by application section

## Integration with Other Tools

### With Sass

WBR works seamlessly with Sass. Run both in parallel:

**`package.json`**

```json
{
  "scripts": {
    "dev": "npm run wbr:dev & npm run sass:dev",
    "build": "npm run wbr:prod && npm run sass:prod",
    "wbr:dev": "node wbr -b -v -w",
    "wbr:prod": "node wbr -b -p",
    "sass:dev": "sass --embed-sources --watch --style expanded sass/main.scss:dist/css/main.css --load-path='./src'",
    "sass:prod": "sass sass/main.scss:dist/css/main.min.css --style compressed --no-source-map --quiet --load-path='./src'"
  }
}
```

**Running in Development:**

```bash
npm run dev
```

This starts both WBR and Sass in watch mode simultaneously.

### With TypeScript

WBR uses esbuild, which handles TypeScript natively. No additional setup needed!

**`tsconfig.json`** (optional, for IDE support):

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "node",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "resolveJsonModule": true,
    "paths": {
      "@components/*": ["./src/components/*"],
      "@libs/*": ["./libs/*"]
    }
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "**/*.wcto.js"]
}
```

### With Dev Server

Combine WBR with a development server:

**Using `live-server`:**

```bash
npm install -D live-server
```

**`package.json`**

```json
{
  "scripts": {
    "dev": "npm run wbr:dev & npm run serve",
    "serve": "live-server --port=3000 --entry-file=index.html",
    "wbr:dev": "node wbr -b -v -w"
  }
}
```

**Using `http-server`:**

```bash
npm install -D http-server
```

```json
{
  "scripts": {
    "dev": "npm run wbr:dev & npm run serve",
    "serve": "http-server -p 3000",
    "wbr:dev": "node wbr -b -v -w"
  }
}
```

### With Vite (Alternative)

For advanced features, use Vite with WinnetouJs:

```bash
npm install -D vite
```

**`vite.config.js`**

```javascript
import { defineConfig } from "vite";

export default defineConfig({
  build: {
    outDir: "dist",
    emptyOutDir: true,
  },
  server: {
    port: 3000,
    open: true,
  },
});
```

Run WBR separately for constructo compilation, and let Vite handle dev server and HMR.

## Performance Optimization

### Tree-Shaking

WBR automatically tree-shakes your code:

**Before (total bundle):**

```javascript
// Import everything
import * as Winnetou from "winnetoujs";
```

**After (tree-shaken):**

```javascript
// Import only what you use
import { W } from "winnetoujs";
import { ColorThemes } from "winnetoujs/modules/colorThemes";
```

**Bundle Size Reduction:**

- Full library: ~500 KB
- Tree-shaken (typical app): ~50-100 KB
- Savings: 80-90%

### Code Splitting

For large apps, use multiple entry points:

```json
{
  "apps": ["./src/app.ts", "./src/vendor.ts", "./src/admin.ts"]
}
```

Load only what's needed per page:

```html
<!-- Home page -->
<script type="module" src="/dist/js/app.winnetouBundle.min.js"></script>

<!-- Admin page -->
<script type="module" src="/dist/js/admin.winnetouBundle.min.js"></script>
```

### Bundle Analysis

Check bundle size after build:

```bash
ls -lh dist/js/
```

**Development bundle:**

```
-rw-r--r-- 1 user user 1.2M  app.winnetouBundle.js
```

**Production bundle:**

```
-rw-r--r-- 1 user user 245K  app.winnetouBundle.min.js
```

## Troubleshooting

### WBR Not Found

**Issue:** `command not found: wbr` or `cannot find module 'wbr'`

**Solutions:**

1. Ensure WinnetouJs is installed:

   ```bash
   npm install winnetoujs
   ```

2. Check if `wbr.js` exists in project root:

   ```bash
   ls -la wbr.js
   ```

3. If missing, copy from node_modules:

   ```bash
   cp node_modules/winnetoujs/wbr.js ./
   ```

4. Use full path:
   ```bash
   node ./wbr.js -b -v
   ```

### Constructos Not Compiling

**Issue:** `.wcto.js` files not generated

**Solutions:**

1. Check `constructosSourceFolder` in `win.config.json`:

   ```json
   {
     "constructosSourceFolder": "./src" // Verify this path
   }
   ```

2. Ensure `.wcto.html` files are in the source folder:

   ```bash
   find ./src -name "*.wcto.html"
   ```

3. Run with verbose flag to see details:

   ```bash
   node wbr -b -v
   ```

4. Check for syntax errors in `.wcto.html` files

### Bundle Not Created

**Issue:** No output bundle in `outputDir`

**Solutions:**

1. Use `-b` flag:

   ```bash
   node wbr -b  # Must include -b flag
   ```

2. Check `outputDir` path in config:

   ```json
   {
     "outputDir": "./dist/js" // Verify this exists
   }
   ```

3. Create output directory if missing:

   ```bash
   mkdir -p dist/js
   ```

4. Check entry file exists:
   ```json
   {
     "apps": ["./src/app.ts"] // Verify this file exists
   }
   ```

### Watch Mode Not Working

**Issue:** Files change but WBR doesn't rebuild

**Solutions:**

1. Ensure `-w` flag is used:

   ```bash
   node wbr -b -w  # Include -w
   ```

2. Check file system permissions

3. Try restarting watch mode:

   ```bash
   # Stop with Ctrl+C, then restart
   npm run dev
   ```

4. Some editors save files differently; try:
   - Disabling "safe write" in your editor
   - Using `--watch` instead of `-w`

### Import Errors in Bundle

**Issue:** `Cannot find module` errors in browser

**Solutions:**

1. Use `type="module"` in script tag:

   ```html
   <script type="module" src="/dist/js/app.winnetouBundle.min.js"></script>
   ```

2. Check import paths in your code:

   ```javascript
   // ✅ Correct
   import { $header } from "./components/header.wcto";

   // ❌ Wrong
   import { $header } from "./components/header.wcto.js";
   ```

3. Verify bundle file exists:
   ```bash
   ls -la dist/js/
   ```

### Large Bundle Size

**Issue:** Bundle is too large

**Solutions:**

1. Use production mode:

   ```bash
   node wbr -b -p
   ```

2. Import only what you need:

   ```javascript
   // ❌ Avoid
   import * as Winnetou from "winnetoujs";

   // ✅ Better
   import { W } from "winnetoujs";
   import { ColorThemes } from "winnetoujs/modules/colorThemes";
   ```

3. Split into multiple bundles (code splitting)

4. Use WStyle sparingly (adds to bundle size)

## Best Practices

### 1. Use Watch Mode in Development

Always run WBR in watch mode during development:

```bash
npm run dev
```

Benefits:

- Instant feedback on changes
- Automatic rebuilds
- Faster development workflow

### 2. Separate Dev and Prod Scripts

Keep development and production builds separate:

```json
{
  "scripts": {
    "dev": "node wbr -b -v -w",
    "build": "node wbr -b -p"
  }
}
```

### 3. Clean Builds for Production

Delete output directory before production builds:

```json
{
  "scripts": {
    "clean": "rm -rf dist",
    "build": "npm run clean && node wbr -b -p"
  }
}
```

Or on Windows:

```json
{
  "scripts": {
    "clean": "rd /s /q dist",
    "build": "npm run clean && node wbr -b -p"
  }
}
```

### 4. Use Descriptive Entry Point Names

Name entry files clearly:

```
src/
├── main-app.ts       # ✅ Clear purpose
├── admin-panel.ts    # ✅ Clear purpose
└── app.ts            # ❌ Vague
```

### 5. Organize Constructos Logically

Group related constructos:

```
src/
├── components/
│   ├── common/       # Reusable components
│   ├── layout/       # Layout components
│   └── forms/        # Form components
```

### 6. Monitor Bundle Size

Regularly check production bundle size:

```bash
npm run build
ls -lh dist/js/
```

Set size budgets and optimize if exceeded.

### 7. Use Verbose Mode for Debugging

When troubleshooting, always use verbose output:

```bash
node wbr -b -v
```

## Advanced Configuration

### Custom Build Scripts

Create advanced build workflows:

**`package.json`**

```json
{
  "scripts": {
    "prebuild": "npm run clean && npm run lint",
    "build": "npm run build:wbr && npm run build:sass",
    "build:wbr": "node wbr -b -p",
    "build:sass": "sass sass/main.scss:dist/css/main.min.css --style compressed --no-source-map",
    "postbuild": "npm run size-check",
    "size-check": "ls -lh dist/js/ && ls -lh dist/css/",
    "clean": "rm -rf dist",
    "lint": "eslint src/**/*.{js,ts}"
  }
}
```

### Environment-Specific Builds

Use different configs for different environments:

**`win.config.dev.json`**

```json
{
  "apps": ["./src/app.ts"],
  "outputDir": "./dist/js",
  "constructosSourceFolder": "./src"
}
```

**`win.config.prod.json`**

```json
{
  "apps": ["./src/app.ts"],
  "outputDir": "./build/assets/js",
  "constructosSourceFolder": "./src"
}
```

Then specify config in script:

```bash
# Custom script to use different config
node wbr -b -p --config=win.config.prod.json
```

## Conclusion

WBR is the powerful, efficient build tool that makes WinnetouJs development fast and enjoyable. With esbuild under the hood, it provides lightning-fast compilation and bundling, while remaining simple to configure and use.

**Key Takeaways:**

- Configure WBR with `win.config.json`
- Use `-b -v -w` for development (bundle, verbose, watch)
- Use `-b -p` for production (bundle, production mode)
- WBR compiles constructos and bundles applications
- Generated `.wcto.js` files are created automatically
- Always use `type="module"` in script tags
- Tree-shaking automatically reduces bundle size
- Watch mode provides instant rebuild feedback
- Integrate with Sass, TypeScript, and dev servers
- Monitor and optimize bundle size regularly

With WBR, you can focus on building great applications while it handles all the complex build processes efficiently and reliably.
