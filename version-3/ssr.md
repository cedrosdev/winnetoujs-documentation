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
