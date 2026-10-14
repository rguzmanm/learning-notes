# Route State

Angular allows you to read and use route information through the `ActivatedRoute` service. For instance: URL segments, route parameters, query parameters, route data, route fragments, etc.

There are two APIs available:

- `ActivatedRoute`: It provides access to information about a route associated with a component that is loaded in an outlet, exposed as RxJS Observables. That means it emits new values if route parameters change on the same component instance.
- `ActivatedRouteSnapshot`: It provides the same information as `ActivatedRoute` at a particular moment in time as static properties instead of observables.

```ts
@Component({
  selector: "app-user",
  template: `<div>User ID: {{ userId }}</div>`,
})
export class UserComponent {
  private route = inject(ActivatedRoute);

  // Reactive approach with signals (converted from an Observable)
  userId = toSignal(this.route.paramMap.pipe(map((params) => params.get("id"))));

  // Synchronous approach (read once)
  userIdSnapshot = this.route.snapshot.paramMap.get("id");
}
```

## Router State Properties

The following are some of the most commonly used properties available on the router state:

### Route parameters

They allow you to pass data to a component through the URL. They are used to identify a specific item or resource needed to render the page. They must be defined in the route configuration.

Example URL: `/user/42`

```ts
export const routes: Routes = [
  { path: "users/:id", component: UserComponent },
];
```

### Query parameters

Optional key-value pairs added after a `?` symbol that can persist across navigation events. They are typically used for **page-wide** controls like searching, filtering, or pagination.

Example URL: `/search?category=shoes&page=2`

### Matrix parameters

They are optional parameters that belong to a specific URL segment, rather than applying to the entire route. Unlike query parameters which appear after a `?` symbol and apply globally, matrix parameters use semicolons and are scoped to individual path segments.

Example URL: `/products;category=shoes/detail`

### Fragment

It is a section marker added after a `#` symbol. It acts like a bookmark, telling the browser to scroll to a specific element on the page.

Example URL: `/help#contact-us`

### Data

It is custom information attached to a route behind the scenes. It is used to pass static details like page titles, permissions, or other metadata to a component without putting it in the URL. Regardless of that, the data is still bundled in the client-side JS files. Thus, it is not suitable for sensitive information.
