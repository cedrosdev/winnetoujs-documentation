# Creating a new project

You can create a new WinnetouJs project installing the npm package or scaffolding a new template project using npx.

## Using npx

You can scaffold a new WinnetouJs project using the following command:

```bash
npx create-winnetoujs-app my-winnetoujs-app
```

This will create a new directory called `my-winnetoujs-app` with the basic structure of a WinnetouJs application.
Navigate to the project directory:

```bash
cd my-winnetoujs-app
```

Start the development server:

```bash
npm start
```

This command will install the dependencies and start a local development server. You can view your application in the browser at `http://localhost:3123`, if port is already in use another one will be set.

You can now start building your WinnetouJs application by editing the files in the `src` directory.

## Using npm

You can also create a new WinnetouJs project by installing the npm package manually.

First, create a new directory for your project and navigate to it:

```bash
mkdir my-winnetoujs-app
cd my-winnetoujs-app
```

Initialize a new npm project:

```bash
npm init -y
```

Install the WinnetouJs package as a development dependency:

```bash
npm install winnetoujs --save-dev
```

Create the following directory structure:

```
my-winnetoujs-app/
├── src/
│   ├── index.html
│   ├── app.ts
│   └── myConstructos.wcto.html
├── package.json
└── jsconfig.json
└── win.config.json
└── index.html
```

### index.html

Note that the script tag must have `type="module"` attribute. It enabled the full power of WBR output.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>WinnetouJs App</title>
    <link rel="stylesheet" href="/dist/css/main.css" />
  </head>
  <body>
    <div id="app"></div>
    <script type="module" src="/dist/app.js"></script>
  </body>
</html>
```

### win.config.json

Create a `win.config.json` file in the root of your project with the following content:

```json
{
  "apps": ["./src/app.ts"],
  "outputDir": "./dist/js",
  "constructosSourceFolder": "./src"
}
```

You can adjust the paths according to your project structure. The `apps` array should contain the entry point(s) of your application, `outputDir` is where the compiled JavaScript files will be placed, and `constructosSourceFolder` is where your `.wcto.html` files are located.

The html constructos files can be in subfolders, wbr will search recursively. Wbr will transpile all .wcto.html files into .wcto.js files in the same folder.

Refer to the [WinnetouJs Documentation Website](https://winnetoujs.com/docs) for more information about compiler options.

Wbr is fast and efficient, it uses esbuild under the hood.

### jsconfig.json

Create a `jsconfig.json` file in the root of your project with the following content:

```json
{
  "compilerOptions": {
    "module": "nodenext",
    "target": "es2022",
    "checkJs": true,
    "resolveJsonModule": true,
    "paths": {
      "@libs/*": ["./libs/*"],
      "@strings": ["./src/strings"]
    }
  },
  "exclude": ["node_modules", "**/node_modules/*"]
}
```

This configuration helps your IDE understand the module resolution and provides better autocompletion and error checking.

### VSCode Settings

If you are using Visual Studio Code, you can create a `.vscode` folder in the root of your project and add a `settings.json` file with the following content:

```json
{
  "files.watcherExclude": {
    "**/node_modules": true,
    "**/bower_components": true,
    "**/*.code-search": true,
    "**/*.wcto.js": true
  },
  "search.exclude": {
    "**/node_modules": true,
    "**/bower_components": true,
    "**/*.code-search": true,
    "**/*.wcto.js": true
  }
}
```

### package.json scripts

Add the following scripts to your `package.json` file:

```json
"scripts": {
    "serve": "npx live-server --port=3123 --open=./index.html  --watch=./dist,./index.html",
    "start:dev": "node wbr --bundleRelease --watch --verbose",
    "start:prod": "node wbr --bundleRelease --production",
    "sass:prod": "sass --watch sass/main.scss:dist/css/main.min.css --style compressed --no-source-map --load-path='./src/",
    "sass:dev": "sass --watch sass/main.scss:dist/css/main.css --style expanded --source-map --load-path='./src/'"
  },
```

## VsCode Extension

You can install the [WinnetouJs3 VSCode Extension](https://marketplace.visualstudio.com/items?itemName=cedros-development.vscode-winnetoujs3) to get goto constructo functionality. It will help you to navigate through your constructos files easily. Just press `Ctrl` and click on the constructo name to open the corresponding `.wcto.html` file.

## Next Steps

Now you able to start building your WinnetouJs application by editing the files in the `src` directory and creating your first constructos in `.wcto.html` files.
