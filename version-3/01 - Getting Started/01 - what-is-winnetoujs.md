# What is WinnetouJs?

WinnetouJs is a JavaScript library and framework that provides a comprehensive set of tools and functionalities for building web applications. It is designed to simplify the development process by offering a modular and flexible framework that allows developers to create robust and scalable applications with ease.

WinnetouJs transforms html files into reusable javascript components. It ensure you will not mix html and javascript code in the same file. It also provides a way to manage application state, handle routing, and interact with APIs.

The components are called `CONSTRUCTOS` and are created in `.wcto.html` files:

```html
<!-- myConstructos.wcto.html -->

<winnetou description="fixed text paragraph">
  <p id="[[fixedText]]">This is a fixed text paragraph.</p>
</winnetou>

<winnetou description="dynamic text paragraph">
  <p id="[[dynamicText]]">{{text}}</p>
</winnetou>
```

Then import the constructos in your javascript code:

```javascript
// app.ts

import { $fixedText, $dynamicText } from "./myConstructos.wcto";
new $fixedText().create("#app");
new $dynamicText({
  text: "This is a dynamic text paragraph.",
}).create("#app");
```

This is the basic usage of WinnetouJs framework. There are many more features and functionalities that you can explore in the documentation.

Start with scaffolding a new project using the WinnetouJs CLI tool. Refer to the [Your First WinnetouJs App](https://winnetoujs.com/docs/your-first-winnetoujs-app) guide for more information on how to set up your development environment and create your first WinnetouJs application.

Happy coding!
