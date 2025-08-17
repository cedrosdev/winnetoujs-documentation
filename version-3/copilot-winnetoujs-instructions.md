---
applyTo: "**"
---

# WinnetouJs Instructions

## How it works

When WinnetouJs is installed, it provides a WBR.js file which is responsible to transpile wcto.html files into wcto.js files. User will write html components (called `constructos`) inside wcto.html file and WBR will compile it into reusable js classes which can be imported inside js/ts apps.

## Installation

`npm i winnetoujs`

## Setting Up

### Create a `win.config.json` file in root folder with these params:

```json
{
  "apps": ["./src/app.ts"],
  "outputDir": "./dist/js",
  "constructosSourceFolder": "./src"
}
```

- apps: Array of strings, will be transpiled by esbuild. Supports js and ts files;
- outputDir: String, the location of output bundles;
- constructosSourceFolder: String, locations where wbr will search for wcto.html files.

Attention: These params will be used only by WBR to compile constructos and bundle code. None of these instructions will be used inside js/ts app files.

### Workspace settings

Add these config to workspace settings in order to hide wcto.js generated files:

```json
"settings": {
    "search.exclude": {
        "**/node_modules": true,
        "**/*.wcto.js": true
    },
    "files.exclude": {
        "**/*.wcto.js": true
   }
}
```

### NPM scripts

To maximize performance when compiling, add these settings to `package.json` file:

```json
"scripts": {
    "wbr:prod": "node wbr -b -p",
    "wbr:dev": "node wbr -b -v -w",
}
```

- b: to bundle release
- p: production mode
- w: watch mode

## Constructos

- Create file with wcto.html extension to add constructos.
- The wcto.js compiled files will be generated aside of wcto.html ones.
- Put all similar constructos in one single html file.
- Group then in folders, use this files structure:

```
application/
├── src/
│   ├── app.ts
│   ├── dashboard/
│   │   ├── dashboard.wcto.html
│   │   ├── dashboard.scss
│   │   └── dashboard.ts
│   ├── chart-card/
│   │   ├── chart-card.wcto.html
│   │   ├── chart-card.scss
│   │   └── chart-card.ts
│   ├── menu-item/
│   │   ├── menu-item.wcto.html
│   │   ├── menu-item.scss
│   │   └── menu-item.ts
│   └── sidebar/
│       ├── sidebar.wcto.html
│       ├── sidebar.scss
│       └── sidebar.ts
├── app/
│   ├── en-us.ts
│   └── router.ts
├── sass/
│   ├── app.scss
│   ├── login.scss
│   └── _variables.scss
├── package.json
├── wbr.config.json
├── wbr.js
└── README.md
```
