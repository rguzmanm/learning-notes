# Host Elements

A host element is the **HTML element** on which an Angular component (or directive) is attached. It is the component's **outer wrapper**. That means the contents of a component's template are rendered inside its host element.

In this component:

```ts
@Component({
  selector: "app-user-card",
  template: ` <h2>{{ name() }}</h2> `,
})
export class UserCard {
  name = signal("Alex");
}
```

The `<app-user-card>` element is the **host element**.

```html
<!-- app.html -->
<p>User:</p>
<app-user-card />
```

## Binding to the host element

You can bind properties, attributes, styles, and events directly into a component's host element from its `@Component` decorator using the `host` property. .

Technically, you can do something like this to change the background color:

```html
<!-- app.html -->
<p>User:</p>
<app-user-card [style.backgroundColor]="'yellow'" />
```

Or you could bind it in the host element, instead of letting the parent repeat it every time it uses an `app-user-card`

```ts
@Component({
  selector: "app-user-card",
  template: ` <h2>{{ name() }}</h2> `,
  host: {
    "[style.backgroundColor]": "'yellow'",
  },
})
export class UserCard {
  name = signal("Alex");
}
```
