# Directives

Directives are classes that **add behavior to elements in the HTML template**. They allow you to modify how an element behaves, how it looks, or even whether it exists in the DOM.

Use directives to **encapsulate reusable behavior** that you need to apply to an existing element. For example to:

- Automatically focus an input
- Open or close a native HTML dialog
- Change an element's appearance based on some condition

Use the `@Directive()` decorator to tell Angular a class is a directive, and `selector` property in to tell Angular where to apply the directive.
Angular has three types of directives:

- **Attribute Directives**

They change the **apperance or behavior** of an existing DOM element. They do **NOT** add or remove elements from the DOM.
The following example changes the background color of an element on hover.

```ts
@Directive({
  selector: "[appHighlight]",
  standalone: true,
  host: {
    "(mouseenter)": "isHighlighted = true",
    "(mouseleave)": "isHighlighted = false",
    "[style.background-color]": 'isHighlighted ? "yellow" : null',
  },
})
export class HighlightDirective {
  isHighlighted = false;
}
```

It then can be used like this:

```html
<button appHighlight>Hover over me</button>
```

The selector matches an element containing an attribute named `appHighlight`
In this case, the button is the directive's host element

- **Structural Directives**

  Shorthand Syntax

- **Components**

  A component is technically a special kind of directive that has its own template. The main distinction is that **components define a piece of UI**, while **attribute directives enhance existing UI** and **structural directives manipulate the DOM**

## Directive Composition

https://angular.dev/guide/directives/directive-composition-api
