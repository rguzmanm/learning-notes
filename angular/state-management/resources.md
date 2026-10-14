# Resource API

It is a signal-based primitive designed to integrate asynchronous operations (such as HTTP requests, and backend data fetching) into Angular's synchronous reactivity model. It is NOT recommended for write operations.

Standard signals (`signal`, `computed`) update synchronously, The Resource API creates a wrapper that tracks the full async lifecycle (loading, success, error, reload, aborting) and exposes those states directly as signals.

In other words, its purpose is to bridge the gap between asynchronous requests and synchronous signals.

It offers these functions:

## resource

The `resource` function works with **promises**. Use it if you prefer to use `fetch()` to retrieve data.

It accepts an options object with two main properties:

1. `params` (optional): A reactive function (similar to `computed()`) that returns the parameters needed by the async request. When any signal read inside this function changes, the `loader` function is called again with the updated values.
2. `loader`: An `async` function responsible for fetching or producing the data. It receives an object containing three properties:
   - `params`: The current parameters from the `params` function.
   - `abortSignal`: An `AbortSignal` that can be used to cancel pending network requests (e.g., via `fetch()`) if new parameters arrive before the previous request finishes or if the component is destroyed
   - `previous`: Contains status (`idle`, `loading`, `resolved`, `error`, etc.) and value metadata from the previous request execution.

The object returned by `resource()` exposes several signals and methods to interact with data:

- `value`: A signal that contains the resolved value of the async operation.
- `isLoading`: A boolean signal that indicates whether the async operation is currently loading.
- `status`: A status signal returning `idle`, `loading`, `resolved`, `error`, `reloading` or `local`.
- `error`: A signal that contains the error object if the async operation fails.
- `reload()`: A method that can be called to manually trigger a reload of the async operation.

Example:

```ts
import { Component, signal, resource } from "@angular/core";

@Component({
  template: `
    <button (click)="userId.set(userId() + 1)">Next User</button>

    @if (userResource.isLoading()) {
      <p>Loading profile...</p>
    } @else if (userResource.error()) {
      <p>Error loading user!</p>
    } @else if (userResource.value(); as user) {
      <div>
        <h3>{{ user.name }}</h3>
        <p>{{ user.email }}</p>
      </div>
    }
  `,
})
export class UserProfileComponent {
  userId = signal(1);

  userResource = resource({
    // 1. Reactive params react to changes in userId()
    params: () => ({ id: this.userId() }),

    // 2. Async loader runs whenever params update
    loader: async ({ params, abortSignal }) => {
      const response = await fetch(`/api/users/${params.id}`, { signal: abortSignal });
      if (!response.ok) throw new Error("Failed to fetch user");
      return response.json();
    },
  });
}
```

## rxResource

The `rxResource` function works with **observables**. Use it if you prefer to use RxJS or if you need to pipe the result through a set of observable operators.

Instead of taking an `async` function that returns a Promise, its loader returns and Observable. The loader function is called `stream` to better reflect the reactive nature of observables.

```ts
import { rxResource } from "@angular/core/rxjs-interop";

const userResource = rxResource({
  params: () => ({ id: this.userId() }),
  stream: ({ params }) => this.http.get<User>(`/api/users/${params.id}`),
});
```

## httpResource

The `httpResource` function works with **HTTP requests**. Use it if you prefer to use `HttpClient` to retrieve data. Because it's built on top of `HttpClient`, `httpResource` supports all the same features, such as interceptors.

It eliminates the need to write manual loader functions or manage `HttpClient` service injections for standard data fetching. Instead, you provide a reactive function that returns a URL string or a full configuration object.

Example:

```ts
export class UserProfileComponent {
  userId = input.required<number>();

  // Single-line reactive GET request
  user = httpResource(() => `/api/users/${this.userId()}`);

  // Advanced request configuration
  posts = httpResource(() => ({
    url: `/api/users/${this.userId()}/posts`,
    method: "GET",
    headers: { "Cache-Control": "no-cache" },
    reportProgress: true,
  }));
}
```

By default, `httpResource` returns and parses the response as JSON. However, you can specify other return types by using additional functions:

```ts
httpResource.text(() => ({ … })); // returns a string in value()
httpResource.blob(() => ({ … })); // returns a Blob object in value()
httpResource.arrayBuffer(() => ({ … })); // returns an ArrayBuffer in value()
```

## debounced

It creates a `resource` that represents a debounced version of a source signal. That means it delays reacting to the signal's value until it stops changing for a specified amount of time.
It accepts a source signal and a wait duration in milliseconds (or a custom wait function that returns a `Promise<void>`).
Same as other resource functions, the returned `value()` always contains the last settled value, and `status()` tells you whether a new value is still pending.

```ts
import { debounced, signal } from "@angular/core";

query = signal("");
debouncedQuery = debounced(this.query, 300);
```
