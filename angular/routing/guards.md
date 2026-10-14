# Guards

Guards are conditional **functions** that control if a user can navigate or leave particular route.
They are a complement to server-side guards, and should **NEVER** be trusted as the on mechanism for access control.

All the Route Guards share the same possible return types:

- `boolean`: true to allow navigation, false to block it.
- `UrlTree` or `RedirectCommand`: Redirect to a different route, instead of blocking
- `Promise<T>` or `Observable<T>`\*: The Router uses the first emitted value and then unsubscribes.

\* `T` can be `boolean`, `UrlTree`, or `RedirectCommand`.

Angular provides the following types of guards:

## CanActivate

Determines whether a user can enter a specific route. Typically used for authentication and authorization.

## CanActivateChild

Determines whether a user can access any of the nested child routes under a parent route. Typically used to protect all nested routes of a parent instead of attaching a guard to each child route.

## CanDeactivate

Checks whether a user can leave a route. Typically used to prevent data loss on dirty forms.

## CanMatch

Determines whether a route can be matched during path matching. Unlike other guards, if it return false, Angular skips that route entry and continues checking the next route definition with the same path.

# UrlTree and Redirect Command

`UrlTree` is a tree-like structure representing a URL path. That is, its fragments, query params, segments, etc.

RedirectCommand is a wrapper around `UrlTree` that extends its capabilities, allowing to define **how** to perform the navigation (e.g., replaceUrl, skipLocationChange, etc.)

```ts
class MyComponent {
  private readonly router = inject(Router);

  constructor() {
    const tree: UrlTree = this.router.parseUrl(
      "/team/33/(user/victor//support:help)?debug=true#fragment",
    );
    const f = tree.fragment; // returns 'fragment'
    const q = tree.queryParams; // returns {debug: 'true'}
  }
}
```

Both options allow you to redirect to a different route. The key difference is that `UrlTree` performs a standard `router.navigateByUrl()` call when returned by a guard, while `RedirectCommand` provides more control over the navigation behavior.

Redirect with `UrlTree`:

```ts
export const authGuardUrlTree: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);

  if (authService.isLoggedIn()) {
    return true;
  }

  // Aborts current navigation and redirects to /login with query parameters
  return router.createUrlTree(["/login"], {
    queryParams: { returnUrl: state.url },
  });
};
```

Redirect with `RedirectCommand`:

```ts
export const authGuardRedirectCommand: CanActivateFn = (route, state) => {
  const authService = inject(AuthService);
  const router = inject(Router);

  if (authService.isLoggedIn()) {
    return true;
  }

  const loginUrlTree: UrlTree = router.createUrlTree(["/login"]);

  // Wraps UrlTree with navigation options
  return new RedirectCommand(loginUrlTree, {
    replaceUrl: true, // Replaces the unauthorized URL in history to prevent back-button loops
  });
};
```
