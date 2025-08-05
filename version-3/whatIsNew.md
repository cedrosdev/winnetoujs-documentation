# What's new in version 3

WinnetouJs has shifted its primary focus toward exceptional speed and top-notch performance. By meticulously removing all unnecessary features, the framework now produces extremely lightweight bundles, significantly reducing their size. Additionally, the compilation process has become astonishingly fast, ensuring developers experience rapid, efficient builds and smoother workflows.

## Main changes

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
