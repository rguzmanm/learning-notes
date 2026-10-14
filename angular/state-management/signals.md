# Signals

Signals are a wrapper around a value that notifies interested consumers when that value changes.

## Signal (Basic / Writable Signal)

They provide and API for updating their values directly. Either by using setting it directly with `.set()` or using `update()` to compute a new value from the previous one.

```ts
const count = signal(0);
count.set(1);
count.update((value) => value + 1);
```

## Computed Signal (Derived Read-Only State)

Computed signals are **read-only** signals that derive their value from other signals. You defined them by using the `computed()` function and specifying a derivation.

```ts
const count = signal(0);
const doubled = computed(() => count() * 2);
```

The derivation function will not run until the first time the signal is read. The calculated value is cached and only recalculated when the signals it depends on change.

## Linked Signals

It derives its default state from another signal, but remains writable so you can manually override its value. Whenever the upstream source signal changes, the `linkedSignal` will automatically update.
It works similar to `signal()`, but instead of passing a default value, you pass a computation function like `computed()`. When the value of the computation changes the value of the `linkedSignal` is also updated.

```ts
@Component({
  /* ... */
})
export class ShippingMethodPicker {
  shippingOptions: Signal<ShippingMethod[]> = getShippingOptions();
  selectedOption = linkedSignal(() => this.shippingOptions()[0]);

  changeShipping(index: number) {
    this.selectedOption.set(this.shippingOptions()[index]);
  }
}
```

When creating a `linkedSignal`, you can pass an object with separate `source` and `computation` properties, instead of providing just a computation.
The `source` can be any signal, like `computed` or component `input`. The `linkedSignal` change its value when the `source` changes or when any signal referenced in the `computation` changes, updating its value to the result of the provided `computation`.

The computation is a function that receives the new value of `source` and a `previous` object. The previous object has two properties— `previous.source` is the previous value of `source`, and `previous.value` is the previous value of the `linkedSignal`. You can use these previous values to decide the new result of the computation.

```ts
interface ShippingMethod {
  id: number;
  name: string;
}

@Component({
  /* ... */
})
export class ShippingMethodPicker {
  constructor() {
    this.changeShipping(2);
    this.changeShippingOptions();
    console.log(this.selectedOption()); // {"id":2,"name":"Postal Service"}
  }

  shippingOptions = signal<ShippingMethod[]>([
    { id: 0, name: "Ground" },
    { id: 1, name: "Air" },
    { id: 2, name: "Sea" },
  ]);

  selectedOption = linkedSignal<ShippingMethod[], ShippingMethod>({
    // `selectedOption` is set to the `computation` result whenever this `source` changes.
    source: this.shippingOptions,
    computation: (newOptions, previous) => {
      // If the newOptions contain the previously selected option, preserve that selection.
      // Otherwise, default to the first option.
      return newOptions.find((opt) => opt.id === previous?.value.id) ?? newOptions[0];
    },
  });

  changeShipping(index: number) {
    this.selectedOption.set(this.shippingOptions()[index]);
  }

  changeShippingOptions() {
    this.shippingOptions.set([
      { id: 0, name: "Email" },
      { id: 1, name: "Sea" },
      { id: 2, name: "Postal Service" },
    ]);
  }
}
```
