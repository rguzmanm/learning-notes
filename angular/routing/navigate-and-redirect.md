# Navigate

Angular's router handles **in-app navigation** using **relative URLs** only — resolved either from the root domain or relative to the current component's route. External, absolute URLs (e.g. `https://google.com`) are **not** managed by the router; use a plain `<a href="...">` tag instead.

Angular provides two ways to navigate between routes: declarative and programmatic.

## Declarative Navigation (RouterLink)

The `RouterLink` _directive_ is the declarative approach to navigation. It can be applied to any HTML element.

- When used on `<a>` or `<area>` elements, it dynamically generates a bind to the `href` attribute, to preserve browser link behaviors like opening in a new tab.
- When used on any other element, it attaches a `(click)` listener that invokes `router.navigateByUrl()` programatically.

However, it is recommended to use `<a>` elements for navigation, to preserve native link behaviors and keyboard accessibility.

### String vs Array Syntax

Angular allows two syntaxes to define **relative URLs**: strings and arrays.

```html
<!-- Navigates user to /dashboard -->
<a routerLink="dashboard">Dashboard</a>
<a [routerLink]="['dashboard']">Dashboard</a>
```

- **String syntax** is the most common way to define relative URLs.
- **Array syntax** is useful when you need to define dynamic parameters.

```html
<a [routerLink]="['user', currentUserId()]">Current User</a>
```

Additionally, you can specify if the route is relative to the current URL or the root domain based on whether it is prefixed with a forward slash (`/`).

```html
<!-- Navigates to /settings/notifications -->
<a routerLink="notifications">Notifications</a>
<a routerLink="/settings/notifications">Notifications</a>

<!-- Navigates to /team/:teamId/user/:userId -->
<a routerLink="/team/123/user/456">User 456</a>
<a [routerLink]="['/team', teamId(), 'user', userId()]">Current User</a>
```

## Programmatic Navigation (Router)

When you need to navigate to a **relative URL** based on logic, user actions, or application state, you can use these two methods from the `Router` service.

- `router.navigate()`

  Use it to navigate by specifying the URL path **array**.
  Since the `Router` is a singleton, the `navigate()` method is **root-relative by default**, which means the ommiting a forward slash (`/`) will still navigate from the root domain. Use the `relativeTo` option to navigate relative to the current route.

  ```ts
  @Component({
    ...
  })
  export class AppDashboard {
    private router = inject(Router);

    navigateToProfile() {
      // Navigate relative to the root domain
      this.router.navigate(['/users', userId]);
    }
    navigateToEdit(){
      // Navigate to edit route relative to current route
      this.router.navigate(['edit'], {relativeTo: this.route});
    }
  }
  ```

  When using `relativeTo`, never prefix the first command with `/`. A leading `/` makes the navigation realtive to the root domain and ignores `relativeTo` entirely.

  You can use `..` to navigate up one level, or `...` to navigate up multiple levels. Provided they are **all in the first element** of the path array.

  ```ts
  this.router.navigate(["../../settings"], { relativeTo: this.route });
  ```

- `router.navigateByUrl()`

  Use it to navigate by specifying the URL as **string**. This method is ideal when you have a full URL path or when working with externally provided URLs.

  When using it, the route is **always root-relative**, regardless of whether it is prefixed with a forward slash. However, you should **always include the leading slash** to avoid unexpected URL parsing issues.

  ```ts
  router.navigateByUrl("/products/123?view=details#reviews");
  ```

# Redirect

Angular allows you to automatically redirect users from one route to another using the `redirectTo` property in the route configuration.

```ts
const routes: Routes = [
  { path: "marketing", redirectTo: "newsletter" },
  { path: "legacy-user/:id", redirectTo: "users/:id" },
  { path: "**", redirectTo: "/login" },
];
```

## pathMatch

The `pathMatch` property enables to determine how URLs are matched to routes:

- `prefix`: The URL must start with the route path (default behavior).

  ```ts
  export const routes: Routes = [{ path: "news", redirectTo: "blog" }];
  ```

  In this example, all routes prefixed with `news` are redirected to `blog`:
  - `/news` redirects to `/blog`
  - `/news/latest` redirects to `/blog/latest`

- `full`: The entire URL must match the route path exactly.
  ```ts
  export const routes: Routes = [
    { path: "news", redirectTo: "blog", pathMatch: "full" },
  ];
  ```
  In this example, only the exact route `news` is redirected to `blog`.
  - `/news` redirects to `/blog`
  - `/news/latest` does **not** redirect

This property can also accept a function to add logic to how users are redirected. The function should return a `string`, `URLTree`, or a promise or observable that resolves to a `string` or `URLTree`.

```ts
export const routes: Routes = [
  {
    path: "restaurant/:location/menu",
    redirectTo: (activatedRouteSnapshot) => {
      const location = activatedRouteSnapshot.params["location"];
      const currentHour = new Date().getHours();

      // Auto-redirect based on time of day
      if (currentHour >= 5 && currentHour < 11) {
        return `/restaurant/${location}/menu/breakfast`;
      } else if (currentHour >= 11 && currentHour < 17) {
        return `/restaurant/${location}/menu/lunch`;
      } else {
        return `/restaurant/${location}/menu/dinner`;
      }
    },
  },

  // Destination routes
  { path: "restaurant/:location/menu/breakfast", component: Menu },
  { path: "restaurant/:location/menu/lunch", component: Menu },
  { path: "restaurant/:location/menu/dinner", component: Menu },
];
```
