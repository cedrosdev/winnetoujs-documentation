# What's new in version 3

WinnetouJs has shifted its primary focus toward exceptional speed and top-notch performance. By meticulously removing all unnecessary features, the framework now produces extremely lightweight bundles, significantly reducing their size. Additionally, the compilation process has become astonishingly fast, ensuring developers experience rapid, efficient builds and smoother workflows.

## Main changes

### WinnetouJs Modules

Winnetou was divided in modules in order to user can import only what is important for project. Modules are:

- colorThemes
- router
- select
- style
- translations

To use modules, import methods like:

```javascript
import { Router } from "winnetoujs/modules/router";
```

### ESBuild Engine

We have replaced Webpack in order to use esbuild.

### Sass

We have discontinued the embedded compilation of sass.

### VDOM

VDOM is completely removed from WinnetouJs.

### Color Themes

Now must be called on app startup

### WStyle Philosophy

It's not a WinnetouJs feature, it's a javascript methodology.

```javascript
import { wstyle } from "winnetoujs/modules/style";
export const leftMenu = wstyle({
  width: "100%",
  height: "50px",
  color: "white",
  "border-radius": "5px",
  display: "flex",
  "align-items": "center",
  "justify-content": "center",
  cursor: "pointer",
  "font-size": "16px",
  "text-transform": "uppercase",
});
```

And then put the style in `style` class of constructo:

```javascript
import { leftMenu } from "./styles";
new $leftMenu({
  onclick: "",
  style: leftMenu,
  text: strings.endpoints,
}).create(this.output);
```

> Be careful, Winnetou Styles can increase the final bundle size, use wisely.

### Typescript

Now WinnetouJS works with typescript.

### Translations

Now the original language file must be a js/ts file and not a json one. This ensure that use can use Go To Reference between strings and code, without the need to use extensions.

```javascript
import { W } from "winnetoujs";
export default W.strings = {
  title: "Hello Strings!",
  buttonText: "Change Lang",
  buttonText2: "Original Lang",
};
```

In this new way user can use f12 to go directly to
string and apply changes and wbr has one less
calculation to do. And reverse f12 works as well.

Now updateTranslations has a new calling method (note that `translationPublicPath` is declared directly within the method, not in win.config.json):

```javascript
W.updateTranslations({
  stringsClass: strings,
  translationsPublicPath: "/translationsFiles",
}).then(res => app.start());
```

Inside `/translationsFiles` folder, put the json translations files as usual. Do not put original language json file in `translationsPublicPath` in
order to clear `localStorage` and load strings from
winnetou.js it self. It's more efficient.

### Icons

Do not digest icons anymore. Icons libraries has their own integration methods which is most of the time the fasted way and also makes the final winnetou bundle lighter.

### Plugins

Now must provide compiled constructos js files because wbr doesn't search node_modules anymore.

### Constructos

WinnetouJs now uses folder paradigm, which provides a better readability for large projects. It mean that wbr will transpile constructos directly in the folder where placed wcto.html file.

### Files Naming

WinnetouJs now use wcto to identify their own files. It mean that constructor must have `wcto.html` extension and output files will have `wcto.js` extension. It allows you to configure excludes patterns in your code editor easily.

In Visual Studio Code use search.exclude to not show transpile constructos files in search bar:

```json
"search.exclude": {
    "**/node_modules": true,
    "**/bower_components": true,
    "**/*.code-search": true,
    "**/*.wcto.js": true
}
```

### Router

This is a new router paradigm, that allow user to use go to reference:

```javascript
import { Router } from "winnetoujs/modules/router";
import { Settings } from "../constructos/settings/settings";

class MyRouter_ {
  constructor() {
    this.createRoutes();
  }
  /** @private */
  routes = {};

  methods = {
    settings: {
      go: () => Router.navigate("/settings"),
      set: () => {
        this.routes["/settings"] = () => {
          new Settings("right-win-base").render();
        };
      },
    },
  };

  /** @private */
  createRoutes() {
    Object.keys(this.methods).forEach(key => {
      this.methods[key].set();
    });
    Router.createRoutes(this.routes);
  }
}

export const MyRouter = new MyRouter_();
```

### WBR

WBR hasn't transpile method anymore. It'll always compile bundle because esbuild is ultra fast.
Args has this settings:

```javascript
program
  .name("wbr")
  .description("Winnetou Bundle Runtime (WBR) - Version 3")
  .version("3.0.0")
  .option("-w, --watch", "Watch mode")
  .option("-b,--bundleRelease", "Compile project")
  .option("-p, --production", "Production mode")
  .option("-v, --verbose", "Verbose output")
  .parse();
```

# win.config.json

WinnetouJs setting file is extremely simpler.

```json
{
  "apps": ["./src/ts/app.ts"],
  "outputDir": "./dist/js",
  "constructosSourceFolder": "./src/constructos"
}
```

# Sass integration

We recommend you to use sass. Create this scripts in your package.json:

```json

"scripts": {
    "wbr:prod": "node wbr -b -p",
    "wbr:dev": "node wbr -b -v --watch",
    "sass:app:dev": "sass --style expanded --watch --embed-sources sass/app.scss:../../Public/reports/dist/css/app.css",
    "sass:login:dev": "sass --embed-sources --watch --style expanded sass/login.scss:../../Public/reports/dist/css/login.css",
    "sass:app:prod": "sass sass/app.scss:../../Public/reports/dist/css/app.css --style compressed --no-source-map --quiet",
    "sass:login:prod": "sass sass/login.scss:../../Public/reports/dist/css/login.css --style compressed --no-source-map --quiet"
},

```
