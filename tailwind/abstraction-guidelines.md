## Abstraction Guidelines

**Don't abstract repeated CSS, abstract repeated ideas.**

That means, repeat utilities freely. Abstract concepts, not styling.

You should **keep utilities in the HTML** when:

- They are just styling decisions: `p-2`, `flex`, `gap-4`, etc
- The repetition doesn't represent a meaningful UI concept
- You're only trying to reduce duplicated class names

Use the **Base layer** when you want to define **global defaults** for HTML elements (**defaults** is the key word). If every element of this type should look in a certain way, put it in the **Base layer**

Use the **Components layer** if you're defining **reusable UI components** with a clear semantic meaning. Examples: `.card`, `.modal`, `.badge`,

In frameworks like Angular, React, etc., it is preferable to create **framework components** (`app-button`,`app-card`) over CSS components. Let the component encapsulate the repeated Tailwind utilities

**Don't abstract just because the classes repeat**. `class="p-4 rounded-lg shadow bg-white"` is **not** a component; it is just a collection of styling decisions. BUT, if every occurence represents a **Product card** or **Bagde**, then it is a real component worth abstracting.

### The rule of three

1. First occurrence → Use utilities.
2. Second occurrence → Use utilities.
3. Third occurrence → Ask: **Am I repeating a UI concept or just CSS?**

If it's a concept, abstract it.

https://adamwathan.me/css-utility-classes-and-separation-of-concerns/?utm_source=chatgpt.com
