# DOM

The **Document Object Model** (DOM) is the browser's **in-memory model** of a web page. It represents the markup (HTML) as a **tree of objects** that JavaScript can read and modify.

Each object has **properties** you can interact with through the DOM API:

```js
document.querySelector("h1").textContent = "Hello, world!";
```

In summary:

- **HTML** defines the document's structure.
- **DOM** represents that structure as objects in memory.
- **JavaScript** can manipulate those objects to update the page dynamically.

## Note

In HTML, **attributes** are name-value pairs written in the markup, like `id="save"` or `disabled` in `<button id="save" disabled>`.

However, not all attributes have a corresponding DOM property:

- Often they match: the disabled attribute is reflected in the `button.disabled` property.
- Sometimes the names differ: the `colspan` attribute is exposed as the `colSpan` property.
- Sometimes there is no property at all. Like `aria-*` attributes such as `aria-label`, most elements have no `ariaLabel`-style property
