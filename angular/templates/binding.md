# Bindings

Binding is how you **connect data or behavior in your TypeScript code to your HTML template, or connect events from the template back to your TypeScript code**

## Interpolation

It is a one-way data binding mechanism that allows to embed dynamic expressions and component property values into the HTML templates using **double curly braces**:

```ts
@Component({
  template: ` <p>Your color preference is {{ theme }}.</p> `,
})
export class App {
  theme = "dark"; // For values that change over time, use signals.
}
```

## Property Binding

Angular supports binding dynamic values into object properties (DOM) and HTML attributes using **square brackets**. You can use it in:

- Native element properties

```html
<button [disabled]="isFormValid()">Save</button>
```

- Component and directive properties

```html
<!-- Component -->
<my-listbox [value]="mySelection()" />

<!-- Directive -->
<img [ngSrc]="profilePhotoUrl()" />
```

- Attributes with no corresponding DOM properties

```html
<!-- You can do this using the `attr` prefix -->
<ul [attr.role]="listRole()"></ul>
```

- CSS Classes and Styles

```html
<ul [class.expanded]="isExpanded()"></ul>

<section [style.display]="isExpanded() ? 'block' : 'none'"></section>
```

## Event Binding

It is a one-way data bnding mechanism that allows your app to respond to user actions. It supports defining event listeners on an element in your template by specifying the event name inside **partentheses** along with a statement to execute when the event occurs.

```html
<input type="text" (keyup)="updateField($event)" />
```

In every template event listener, Angular provides a variable named `$event` that represents a **reference to the event object**.

```ts
@Component({
  ...
})
export class App {
  updateField(event: KeyboardEvent): void {
    console.log(`The user pressed: ${event.key}`);
  }
}
```

## Two-way Binding

It is a mechanism that syncrhonizes data between a component's TypeScript class and it template in **both directions**. It uses the `[(...)]` syntax, informally called _banana-in-a-box_ syntax.

There are two ways two-way binding is typically used:

- **Two-way binding with (template-driven) form controls**

  Used to keep data in sync with a form control as a user interacts with it using the `ngModel` directive.

  ```ts
  // user-form.ts
  @Component({
    selector: "app-user-form",
    imports: [FormsModule],
    templateUrl: "./user-form.html",
  })
  export class UserForm {
    username = "Alex";
  }
  ```

  ```html
  <input id="username" [(ngModel)]="username" />

  <p>Your username is: {{ username }}</p>
  ```

  When the user types into the input, username is updated, and when username changes, the input reflects the new value

- **Two-way binding between components**

  Used to keep a parent and a child component in sync. The child exposes a writable `model()` signal, and the parent binds to it using the `[(...)]` syntax.

  ```ts
  // counter.ts
  @Component({
    selector: "app-counter",
    template: `
      <button (click)="increment()">+</button>
      <span>{{ count() }}</span>
    `,
  })
  export class Counter {
    count = model(0);

    increment(): void {
      this.count.update((value) => value + 1);
    }
  }
  ```

  ```ts
  // app.ts
  @Component({
    selector: "app-root",
    imports: [Counter],
    template: `
      <app-counter [(count)]="initialCount" />

      <p>The current count is: {{ initialCount() }}</p>
    `,
  })
  export class App {
    initialCount = signal(10);
  }
  ```

  When the child updates `count`, the parent's `initialCount` is updated, and when `initialCount` changes, the child reflects the new value.

  The `[(count)]` syntax is just a shorthand that can be split into its two separate parts:
  1. `[count]="initialCount()"` (Property binding): Passes the value down to the child component.
  2. `(countChange)="initialCount.set($event)"` (Event binding): Captures the changes coming out of the child component.
