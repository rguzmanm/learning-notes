# Loading Strategies

A loading strategy is a technique that dictactes how, when, and under what conditions **resources** (JS bundles, assets, data, etc.) are fetched from the server.
Modern Angular allows four different strategies:

## Eager Loading

Resources are fetched immediately during the application's initial load.

```ts
// app.routes.ts
import { HomeComponent } from "./home/home.component";

export const routes: Routes = [
  // Imported directly at startup — included in the main bundle
  { path: "home", component: HomeComponent },
];
```

## Route-based Lazy Loading

Resources are fetched on demand when the user navigates to a specific route.

```ts
// app.routes.ts
export const routes: Routes = [
  // Dynamic import — creates a separate code chunk loaded on route match
  {
    path: "admin",
    loadComponent: () =>
      import("./admin/admin.component").then((m) => m.AdminComponent),
  },
];
```

## Preloading

Lazy routes are loaded in the background after the initial load. Angular supports two preloading strategies:

- `PreloadAllModules`: Preloads all lazy routes
- `NoPreloading`: Does not preload any lazy routes (default)

However, it also allows for custom preloading strategies by implementing the `PreloadingStrategy` interface.

```ts
// app.config.ts
import { ApplicationConfig } from "@angular/core";
import {
  provideRouter,
  withPreloading,
  PreloadAllModules,
} from "@angular/router";
import { routes } from "./app.routes";

export const appConfig: ApplicationConfig = {
  providers: [
    // Preloads all lazy-loaded routes in background once the app renders
    provideRouter(routes, withPreloading(PreloadAllModules)),
  ],
};
```

## Defered Loading

Resources are fetched dynamically when needed, through user interactions or specific template conditions (`on viewport`, `on interaction`, `on hover`, `on timer`, or `when <condition>`).

```html
<!-- dashboard.component.html -->

<!-- Component downloads & renders only when scrolled into the viewport -->
@defer (on viewport) {
<app-heavy-analytics-chart [data]="metrics" />
} @placeholder {
<div class="skeleton-loader">Scroll down to view chart...</div>
} @loading {
<p>Downloading chart module...</p>
}
```
