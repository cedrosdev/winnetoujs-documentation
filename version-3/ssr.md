# Server Side Render (SSR)

WinnetouJS 3 LTS now provides a simple SSR option.

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
