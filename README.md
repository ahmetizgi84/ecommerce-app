# Vuejs

In most build-tool-enabled Vue projects, we author Vue components using an HTML-like file format called Single-File Component

```js

<script>
export default {
  data() {
    return {
      count: 0
    }
  }
}
</script>

<template>
  <button @click="count++">Count is: {{ count }}</button>
</template>

<style scoped>
button {
  font-weight: bold;
}
</style>

```

# API Styles

Vue components can be authored in two different API styles: Options API and Composition API.

## Options API

With Options API, we define a component's logic using an object of options such as `data`, `methods`, and `mounted`.

```js

<script>
export default {
  // Properties returned from data() become reactive state
  // and will be exposed on `this`.
  data() {
    return {
      count: 0
    }
  },

  // Methods are functions that mutate state and trigger updates.
  // They can be bound as event listeners in templates.
  methods: {
    increment() {
      this.count++
    }
  },

  // Lifecycle hooks are called at different stages
  // of a component's lifecycle.
  // This function will be called when the component is mounted.
  mounted() {
    console.log(`The initial count is ${this.count}.`)
  }
}
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>

```

## Composition API

With Composition API, we define a component's logic using imported API functions. In SFCs, Composition API is typically used with `<script setup>`. The `setup` attribute is a hint that makes Vue perform compile-time transforms that allow us to use Composition API with less boilerplate. For example, imports and top-level variables / functions declared in `<script setup>`are directly usable in the template.

```js

<script setup>
import { ref, onMounted } from 'vue'

// reactive state
const count = ref(0)

// functions that mutate state and trigger updates
function increment() {
  count.value++
}

// lifecycle hooks
onMounted(() => {
  console.log(`The initial count is ${count.value}.`)
})
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>

```

For production use:

- Go with Options API if you are not using build tools, or plan to use Vue primarily in low-complexity scenarios, e.g. progressive enhancement.

- Go with Composition API + Single-File Components if you plan to build full applications with Vue.

# Creating a Vue Application

`npm init vue@latest`

# Text Interpolation

The most basic form of data binding is text interpolation using the "Mustache" syntax (double curly braces):

```js
<span>Message: {{ msg }}</span>
```

It will also be updated whenever the msg property changes.

# Raw HTML

The double mustaches interpret the data as plain text, not HTML. In order to output real HTML, you will need to use the `v-html` directive:

```js

<p>Using text interpolation: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

# Attribute Bindings

Mustaches cannot be used inside HTML attributes. Instead, use a `v-bind` directive:

```js
<div v-bind:id="dynamicId"></div>
```

short version

```js
<div :id="dynamicId"></div>
```

# Boolean Attributes

Boolean attributes are attributes that can indicate true / false values by their presence on an element. For example, disabled is one of the most commonly used boolean attributes.

```js
<button :disabled="isButtonDisabled">Button</button>
```

# Declaring Reactive State

With Options API, we use the `data` option to declare reactive state of a component.

```js
export default {
  data() {
    return {
      count: 1,
    };
  },

  // `mounted` is a lifecycle hook which we will explain later
  mounted() {
    // `this` refers to the component instance.
    console.log(this.count); // => 1

    // data can be mutated as well
    this.count = 2;
  },
};
```

# Declaring Methods

To add methods to a component instance we use the `methods` option. This should be an object containing the desired methods:

```js
export default {
  data() {
    return {
      count: 0,
    };
  },
  methods: {
    increment() {
      this.count++;
    },
  },
  mounted() {
    // methods can be called in lifecycle hooks, or other methods!
    this.increment();
  },
};
```

- You should avoid using arrow functions when defining `methods`, as that prevents Vue from binding the appropriate this value:

Just like all other properties of the component instance, the `methods` are accessible from within the component's template. Inside a template they are most commonly used as event listeners:

```js
<button @click="increment">{{ count }}</button>
```

# DOM Update Timing

To wait for the DOM update to complete after a state change, you can use the `nextTick()` global API:

```js
import { nextTick } from "vue";

export default {
  methods: {
    increment() {
      this.count++;
      nextTick(() => {
        // access updated DOM
      });
    },
  },
};
```

# Stateful Methods

In some cases, we may need to dynamically create a method function, for example creating a debounced event handler:

```js
import { debounce } from "lodash-es";

export default {
  methods: {
    // Debouncing with Lodash
    click: debounce(function () {
      // ... respond to click ...
    }, 500),
  },
};
```

- However, this approach is problematic for components that are reused because a debounced function is stateful: it maintains some internal state on the elapsed time. If multiple component instances share the same debounced function, they will interfere with one another.

- To keep each component instance's debounced function independent of the others, we can create the debounced version in the `created` lifecycle hook:

```js
export default {
  created() {
    // each instance now has its own copy of debounced handler
    this.debouncedClick = _.debounce(this.click, 500);
  },
  unmounted() {
    // also a good idea to cancel the timer
    // when the component is removed
    this.debouncedClick.cancel();
  },
  methods: {
    click() {
      // ... respond to click ...
    },
  },
};
```

# Computed Properties

In-template expressions are very convenient, but they are meant for simple operations. Putting too much logic in your templates can make them bloated and hard to maintain. For example, if we have an object with a nested array:

```js
export default {
  data() {
    return {
      author: {
        name: "John Doe",
        books: [
          "Vue 2 - Advanced Guide",
          "Vue 3 - Basic Guide",
          "Vue 4 - The Mystery",
        ],
      },
    };
  },
};
```

And we want to display different messages depending on if `author` already has some books or not:

```js
<p>Has published books:</p>
<span>{{ author.books.length > 0 ? 'Yes' : 'No' }}</span>

```

At this point, the template is getting a bit cluttered. We have to look at it for a second before realizing that it performs a calculation depending on `author.books`. More importantly, we probably don't want to repeat ourselves if we need to include this calculation in the template more than once.

That's why for complex logic that includes reactive data, it is recommended to use a computed property. Here's the same example, refactored:

```js
export default {
  data() {
    return {
      author: {
        name: "John Doe",
        books: [
          "Vue 2 - Advanced Guide",
          "Vue 3 - Basic Guide",
          "Vue 4 - The Mystery",
        ],
      },
    };
  },
  computed: {
    // a computed getter
    publishedBooksMessage() {
      // `this` points to the component instance
      return this.author.books.length > 0 ? "Yes" : "No";
    },
  },
};
```

```js
<p>Has published books:</p>
<span>{{ publishedBooksMessage }}</span>
```
