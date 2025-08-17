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

### jsconfig.json

To obtain auto completions for constructos use this config in jsconfig.json (put it in root):

```json
{
  "compilerOptions": {
    "module": "esnext",
    "target": "es2022",
    "checkJs": true,
    "moduleResolution": "node",
    "resolveJsonModule": true
  },
  "exclude": ["node_modules", "**/node_modules/*"]
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
│   │   ├── _dashboard.scss
│   │   └── dashboard.ts
│   ├── chart-card/
│   │   ├── chart-card.wcto.html
│   │   ├── _chart-card.scss
│   │   └── chart-card.ts
│   ├── menu-item/
│   │   ├── menu-item.wcto.html
│   │   ├── _menu-item.scss
│   │   └── menu-item.ts
│   └── sidebar/
│       ├── sidebar.wcto.html
│       ├── _sidebar.scss
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

### Constructos html structure

- Every constructo must be created inside `<winnetou>` tags, like:

```html
<winnetou>
  <div id="[[myDiv]]">{{content}}</div>
</winnetou>
```

Attention: Do not duplicate <winnetou> tag, like `<winnetou></winnetou>... rest of code</winnetou>`, it is a bad pattern. Avoid it. Winnetou tag must be: `<winnetou> ...content </winnetou>`.

- Winnetou tags can have description attr to describe constructo:

```html
<winnetou description="A price card">...content</winnetou>
```

### Constructos ID

- Ids must be inside double square-brackets `[[id]]`.
- When a constructo html file is compiled, the id of constructo will be the name of class when it is imported into code, like:

```html
<winnetou>
  <div id="[[myFirstDiv]]"></div>
</winnetou>
```

Will be compiled to `$myFirstDiv`, like:

```javascript
import { $myFirstDiv } from "./commonConstructos.wcto";
new $myFirstDiv().create("#app");
```

- ids will be returned by `create` method, like:

```javascript
const myConstructo = new $myFirstDiv().create("#app");
console.log(myConstructo.ids.myFirstDiv); // myFirstDiv-win-1
```

When constructos are placed in html, WinnetouJs will create a string id to it, with this formula: `originalId-win-randomNumber`. User can override randomNumber with `identifier` option:

```javascript
let left = new $myDiv({}, { identifier: "leftDiv" }).create("#app");
console.log(left.ids.myDiv); // "myDiv-win-leftDiv"
```

This is useful for DOM manipulations.

- constructos can have many ids inside it, first id will be constructo class name, all ids will be returned in create method:

```html
<winnetou>
  <div id="[[mainId]]">
    <span id="[[text1]]">{{title}}</span>
    <span id="[[text2]]">{{text}}</span>
  </div>
</winnetou>
```

```javascript
const card = $mainId().create("#app").ids;
document.getElementById(card.text1).style.color = "blue";
```

### Constructos props

- To create props, use double-brackets `{{prop}}`.
- Props can be optional, use ? token to do it: `{{color?}}`
- Props can have typescript/jsdoc types, use : token to do it: `{{color:string}}`
- Props can have a description, use parenthesis to do it: `{{color(A RGB value)}}`
- All options can be mixed, like: `{{color(A rgb value)?:string}}`

Example of constructos with props:

```html
<winnetou description="Personalized title string">
  <h1 id="[[personalizedTitle]]" class="{{class:'normal'|'danger'}}">
    {{text(The text of title):string}}
  </h1>
</winnetou>
```

```javascript
import { $personalizedTitle } from "./titles.wcto";
new $personalizedTitle({
  class: "danger",
  text: "This is a title",
}).create("#app");
```

### Constructos Methods

- Constructos has two methods: `create()` and `constructoString()`.

#### create(id:string)

- Used to attach constructo to DOM. User must provide a string id as param: `"#app"`.
- options can be provided: `clear:true` to clear html node before insert new constructo in it and `reverse:true` to attach constructo in top of html node.

```javascript
new $title({ text: "Simple title" }).create("#titles", {
  clear: true,
  reverse: true,
});
```

#### constructoString()

- When you need to put a constructo inside another constructo, use constructoString:

```javascript
import { $div, $div2 } from "./components.wcto";
new $div({
  content: new $div2({ content: "inner" }).constructoString(),
}).create("#app");
```
