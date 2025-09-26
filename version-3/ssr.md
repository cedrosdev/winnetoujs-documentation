# Server Side Render (SSR)

WinnetouJS 3 LTS now provides a simple SSR option. It provides a modern and light-weighted template engine for SEO and performance optimization.

## constructoString method

To use WinnetouJs in server side just use `constructoString()` method and send it to frontend as HTML.

```javascript
let template = new $html({
  content: ssr(title, list, title2),
}).constructoString();
res.send(template);
```

## Setting up with node + express

First create a node project with a file tree like:

![SSR File Tree](https://github.com/cedrosdev/winnetoujs-documentation/blob/e7c87e4ec8069a888884a95d688c77998ad07275/images/ssr-file-tree-1.png)

### jsconfig

```json
{
  "compilerOptions": {
    "module": "nodenext",
    "target": "es2022",
    "checkJs": true,
    "resolveJsonModule": true
  },
  "exclude": ["node_modules", "**/node_modules/*"]
}
```

### WinnetouJs

Inside `winnetou-ssr` folder, start a new npm project by `npm init -y` and instal winnetoujs by `npm i winnetoujs`. After create a `win.config.json` and `jsconfig.json` files.

> win,config.json:

```json
{
  "apps": ["./src/app.ts"],
  "outputDir": "./dist",
  "constructosSourceFolder": "./src"
}
```

Inside `src` folder create `app.ts` file like:

```javascript
import { ssr, escapeHTML } from "winnetoujs/modules/ssr";
import { $html, $simpleText } from "./index.wcto";
export { $html, $simpleText, ssr, escapeHTML };
```

The `app.ts` file will be a bridge between winnetoujs and the server files, importing and exporting what will be used.

### Constructos

In `ssr` mode, we can't add constructos inside other constructos using `create` method because node will not simulate a DOM tree in order to reduce cpu usage. So for winnetoujs constructos get to work, we must use `{{content}}` approach, storing `construtoString` contents in variables and sending it to frontend with `ssr` method.

```html
<winnetou>
  <html id="[[html]]">
    <head>
      <title>Test winnetou</title>
      <style>
        body,
        html {
          margin: 0;
          padding: 0;
          font-family: Arial, Helvetica, sans-serif;
          background-color: #333;
          color: white;
        }
        .app {
          padding: 200px;
        }
        .title {
          font-size: 24px;
          margin-bottom: 20px;
        }
        .client {
          font-size: 18px;
          margin: 5px 0;
        }
      </style>
    </head>
    <body>
      <div id="app" class="app">{{content}}</div>
    </body>
  </html>
</winnetou>
<winnetou>
  <div id="[[simpleText]]" class="{{className}}">{{text}}</div>
</winnetou>
```

### The ssr method

> ssr(arr:string[]):string

The `ssr` method receive an array of `constructoString` and concat it into a single string. It flatten inner arrays as well.

## escapeHTML

> escapeHTML(val:string):string

The `escapeHTML` method receives a string and escapes it.

### Compiling with WBR

Use `node wbr -b` to compile `app.ts` to `dist` folder to import it in your server files. Note that we not recommend you to use --production flag because it makes bundle to lose their types.

## Server side

In your index.js, import constructos like:

```javascript
const express = require("express");
const { $html, $simpleText, ssr } = require("./winnetou-ssr/dist/app.js");
const app = express();
const port = 3100;

// Root route
app.get("/", (req, res) => {
  let title = new $simpleText({
    text: "Clients List",
    className: "title",
  }).constructoString();

  let list = [];
  for (let i = 1; i <= 5; i++) {
    list.push(
      new $simpleText({
        text: `Client test ${i}`,
        className: "client",
      }).constructoString()
    );
  }

  let title2 = new $simpleText({
    text: "Another Title",
    className: "title",
  }).constructoString();

  let template = new $html({
    content: ssr(title, list, title2),
  }).constructoString();
  res.send(template);
});

// Start the server
app.listen(port, () => {
  console.log(`Server is running on http://localhost:${port}`);
});
```
