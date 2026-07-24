## Notes

This is not intended to be an exhaustive React manual. It documents concepts I need to study further, mistakes I am likely to repeat, and explanations that helped difficult material make sense. I will edit, expand, reorganize, or remove material as my understanding improves.

---

## Updating From Previous State

When the next value depends on the previous value, use the callback form:

```jsx
// Increase the count using its previous value.
setCount((previousCount) => previousCount + 1);
```

The callback receives the most recent state value.

---

# Arrow Function Parameters

## One Simple Parameter

Parentheses are optional when there is one simple parameter:

```js
// Return the doubled value.
value => value * 2
```

Parentheses may still be included:

```js
// Return the doubled value.
(value) => value * 2
```

## Multiple Parameters

Multiple parameters require parentheses:

```js
// Compare the current index with the selected index.
(item, index) => index !== selectedIndex
```

## Destructured Object Parameter

Braces indicate object destructuring:

```js
// Extract target from the event object.
({ target }) => target.value
```

This function receives an object and immediately extracts its `target` property.

It is similar to:

```js
// Read target from the event object.
(event) => {
  const { target } = event;
  return target.value;
}
```

---

# Arrays in State

## Do Not Mutate State Directly

React state arrays should generally be replaced with new arrays rather than modified in place.

Useful tools include:

* `map()`
* `filter()`
* `includes()`
* spread syntax

## Adding an Item

```jsx
// Add the selected item to the beginning of the cart.
setCart((currentCart) => [selectedItem, ...currentCart]);
```

The spread operator copies the current array into a new array.

## Checking Whether an Item Exists

```js
// Check whether the selected item is already in the cart.
currentCart.includes(selectedItem);
```

`includes()` returns either `true` or `false`.

## Toggling an Item

```jsx
// Add the item if absent or remove it if already selected.
setCart((currentCart) =>
  currentCart.includes(selectedItem)
    ? currentCart.filter((item) => item !== selectedItem)
    : [selectedItem, ...currentCart]
);
```

## Removing an Item by Value

```jsx
// Remove every item matching the selected value.
setCart((currentCart) =>
  currentCart.filter((item) => item !== selectedItem)
);
```

`filter()` keeps elements for which its callback returns `true`.

Therefore:

```js
item !== selectedItem
```

means:

> Keep every item that is not the selected item.

## Removing an Item by Index

```jsx
// Remove only the item at the clicked index.
setCart((currentCart) =>
  currentCart.filter((_, index) => index !== clickedIndex)
);
```

This is useful when:

* Duplicate values may exist
* The exact clicked position matters
* Removing by value might remove more than one item

## `filter()` Mental Model

Do not think:

> Tell `filter()` what to delete.

Think:

> Tell `filter()` what to keep.

For removal by index:

```js
index !== clickedIndex
```

keeps every element except the one at the clicked index.

## Indexes Versus IDs

Indexes may be acceptable for small exercises, but stable unique IDs are usually safer in larger applications.

IDs help when:

* Items have duplicate values
* Items are reordered
* Items are inserted or removed
* React needs stable keys

---

# Event Handling

## Accessing `event.target`

A conventional event handler can be written as:

```jsx
// Update the name from the input event.
const handleChange = (event) => {
  setName(event.target.value);
};
```

The event object contains information about what happened.

* `event.target` refers to the element that triggered the event.
* `event.target.value` contains the current value of an input.

## Destructuring `target`

The same handler can use object destructuring:

```jsx
// Update the name from the input event.
const handleChange = ({ target }) => {
  setName(target.value);
};
```

It can also be written as a single-expression arrow function:

```jsx
// Update the name from the input event.
const handleChange = ({ target }) => setName(target.value);
```

These forms perform the same basic task.

---

# Controlled Inputs

A controlled input gets its displayed value from React state.

```jsx
// Store the input value in state.
const [name, setName] = useState("");
```

The input displays the state value:

```jsx
<input value={name} />
```

A change handler updates the state:

```jsx
// Update state whenever the user types.
const handleChange = ({ target }) => {
  setName(target.value);
};
```

The input connects to that handler:

```jsx
<input
  value={name}
  onChange={handleChange}
/>
```

The data flow is:

```text
User types
↓
onChange fires
↓
handleChange reads target.value
↓
setName updates state
↓
React renders with the new value
```

---

# Effects With `useEffect`

## Purpose

`useEffect()` runs effect code after React finishes rendering.

Basic structure:

```jsx
// Run an effect after rendering.
useEffect(() => {
  // Effect code
}, []);
```

`useEffect()` receives:

1. An effect callback function
2. A dependency array

```jsx
useEffect(effectFunction, dependencyArray);
```

## Effect Timing

A simplified sequence is:

```text
Component renders
↓
React updates the page
↓
Effect runs
```

The effect does not run in the middle of rendering.

## Calling `useEffect`

`useEffect` is a function being called.

Incorrect:

```jsx
useEffect = (() => {
  // Effect code
}, []);
```

This attempts to assign a value to `useEffect`.

Correct:

```jsx
// Call the useEffect function.
useEffect(() => {
  // Effect code
}, []);
```

---

# Dependency Arrays

## No Dependency Array

```jsx
// Run after every render.
useEffect(() => {
  // Effect code
});
```

The effect runs after every render.

## Empty Dependency Array

```jsx
// Run once after the initial render.
useEffect(() => {
  // Effect code
}, []);
```

The effect runs after the component first appears.

It does not rerun because of ordinary state changes.

Its cleanup function still runs when the component unmounts.

## Dependency Included

```jsx
// Run whenever count changes.
useEffect(() => {
  console.log(count);
}, [count]);
```

This effect runs:

* After the initial render
* Whenever `count` changes

Before the effect runs again, React runs the previous cleanup function if one exists.

## Dependency Array Placement

The dependency array is the second argument passed to `useEffect()`.

```jsx
useEffect(() => {
  // Effect code
}, []);
```

The ending:

```jsx
}, []);
```

contains:

* `}`: closes the callback body
* `,`: separates the arguments
* `[]`: provides the dependency array
* `)`: closes the `useEffect()` call
* `;`: ends the statement

## Dependency Array Mental Model

```text
No array       → Run after every render
[]             → Run once after the initial render
[value]        → Run initially and whenever value changes
```

---

# Cleanup Functions

## Basic Structure

An effect may return a function:

```jsx
// Set up an effect and provide cleanup instructions.
useEffect(() => {
  // Setup work

  return () => {
    // Cleanup work
  };
}, []);
```

React treats a function returned by an effect as the cleanup function.

## Cleanup Does Not Run Immediately

Consider:

```jsx
useEffect(() => {
  console.log("A");

  return () => {
    console.log("B");
  };
}, []);
```

After the initial render, React prints:

```text
A
```

React saves the returned function for later.

It does not immediately print `B`.

## When Cleanup Runs

Cleanup runs:

1. Before the effect runs again
2. When the component unmounts

For an effect with dependencies:

```text
Render
↓
Effect runs
↓
Dependency changes
↓
Cleanup runs
↓
Effect runs again
```

For an effect with an empty dependency array:

```text
Initial render
↓
Effect runs
↓
Component eventually unmounts
↓
Cleanup runs
```

## Mental Model

The effect contains setup instructions.

The returned function contains teardown instructions.

```jsx
useEffect(() => {
  // Build the tent.

  return () => {
    // Take down the tent.
  };
}, []);
```

React performs the setup first and saves the teardown instructions until they are needed.

## When Cleanup Is Needed

Ask:

> Did this effect create something that continues to exist after the effect callback finishes?

Cleanup is commonly needed when an effect:

* Starts an interval
* Starts a timeout that may need cancellation
* Adds an event listener
* Creates a subscription
* Opens a network connection
* Opens a WebSocket
* Starts another ongoing process

## Setup and Cleanup Pairs

| Setup                | Cleanup                 |
| -------------------- | ----------------------- |
| `setInterval()`      | `clearInterval()`       |
| `setTimeout()`       | `clearTimeout()`        |
| `addEventListener()` | `removeEventListener()` |
| `subscribe()`        | `unsubscribe()`         |
| `connect()`          | `disconnect()`          |

---

# Event Listener Cleanup

## Incorrect: Removing the Listener Immediately

```jsx
useEffect(() => {
  window.addEventListener("online", handleOnline);
  window.removeEventListener("online", handleOnline);
}, []);
```

This adds the listener and then removes it immediately.

The listener does not remain available to respond to events.

## Correct: Return the Removal Instructions

```jsx
// Register the online event listener.
useEffect(() => {
  window.addEventListener("online", handleOnline);

  // Remove the listener during cleanup.
  return () => {
    window.removeEventListener("online", handleOnline);
  };
}, []);
```

React performs the setup now and saves the cleanup function for later.

## The Handler Must Match

The event name and function must match between setup and cleanup:

```jsx
window.addEventListener("online", handleOnline);
window.removeEventListener("online", handleOnline);
```

Passing a different function to `removeEventListener()` would not remove the original listener.

## Forgetting to Return Cleanup

Incorrect:

```jsx
useEffect(() => {
  window.addEventListener("online", handleOnline);

  () => {
    window.removeEventListener("online", handleOnline);
  };
}, []);
```

The cleanup arrow function exists, but it is not returned.

React does not know that it should use it as cleanup.

Correct:

```jsx
useEffect(() => {
  window.addEventListener("online", handleOnline);

  return () => {
    window.removeEventListener("online", handleOnline);
  };
}, []);
```

---

# Timers and Intervals

## `setInterval()` Returns an ID

`setInterval()` does two things:

1. Starts an interval
2. Returns an identifier for that interval

```jsx
// Start the interval and store its ID.
const intervalId = setInterval(() => {
  // Repeated work
}, 1000);
```

That identifier can later be passed to `clearInterval()`:

```jsx
// Stop the stored interval.
clearInterval(intervalId);
```

## Why Store the ID?

This starts an interval but discards its ID:

```jsx
setInterval(() => {
  // Repeated work
}, 1000);
```

This starts the interval and remembers which interval it is:

```jsx
const intervalId = setInterval(() => {
  // Repeated work
}, 1000);
```

The stored ID allows the program to stop that specific interval later.

## Updating State Inside an Interval

```jsx
// Increase time once every second.
const intervalId = setInterval(() => {
  setTime((previousTime) => previousTime + 1);
}, 1000);
```

The callback form of `setTime()` is used because the next time value depends on the previous time value.

## Do Not Replace the Callback With the Setter

Incorrect:

```jsx
const intervalId = setInterval(setTime, 1000);
```

`setTime` is a state setter. Passing it directly does not tell React to increment the time.

Correct:

```jsx
const intervalId = setInterval(() => {
  setTime((previousTime) => previousTime + 1);
}, 1000);
```

An instruction to assign the `setInterval()` code to `intervalId` means:

> Keep the interval callback and store the value returned by `setInterval()`.

It does not mean:

> Replace the interval callback with `setTime`.

---

# Timer Effect Pattern

## Correct Pattern

```jsx
// Start the timer when the component mounts.
useEffect(() => {
  const intervalId = setInterval(() => {
    setTime((previousTime) => previousTime + 1);
  }, 1000);

  // Stop the timer when the component unmounts.
  return () => {
    clearInterval(intervalId);
  };
}, []);
```

## Why Use an Empty Dependency Array?

The timer should be created once after the initial render.

Without an empty dependency array, the effect would run after every render.

Because the interval changes state every second, each state update causes another render. Recreating the interval after every render could cause duplicate or unnecessarily restarted intervals.

```jsx
[]
```

communicates:

> Start this interval after the initial render and keep it until the component unmounts.

## Scope of `intervalId`

This works:

```jsx
useEffect(() => {
  const intervalId = setInterval(() => {
    // Timer work
  }, 1000);

  return () => {
    clearInterval(intervalId);
  };
}, []);
```

The cleanup function is inside the same scope as `intervalId`, so it can access the variable.

This does not work:

```jsx
useEffect(() => {
  const intervalId = setInterval(() => {
    // Timer work
  }, 1000);
}, []);

return () => {
  clearInterval(intervalId);
};
```

Problems:

* `intervalId` exists only inside the effect callback.
* The cleanup function is outside the effect.
* The component-level return prevents React from reaching its JSX return.

---

# Component Returns Versus Effect Returns

A component can contain both a cleanup `return` and a JSX `return`, but those returns belong to different functions.

## Effect Return

```jsx
useEffect(() => {
  // Effect setup

  return () => {
    // Effect cleanup
  };
}, []);
```

This `return` belongs to the effect callback.

It returns a cleanup function to React.

## Component Return

```jsx
return (
  <main>
    <h1>Hello</h1>
  </main>
);
```

This `return` belongs to the component function.

It returns JSX to React.

## Correct Nesting

```jsx
export default function Example() {
  // Manage work outside the rendering process.
  useEffect(() => {
    // Setup

    return () => {
      // Cleanup
    };
  }, []);

  // Render the interface.
  return (
    <main>
      <h1>Hello</h1>
    </main>
  );
}
```

## Structural Mental Model

```text
Component function
├── useEffect call
│   └── Effect callback
│       └── Returns cleanup function
│
└── Returns JSX
```

The effect returns cleanup.

The component returns JSX.

---

# Complete Timer Example

```jsx
import React, { useEffect, useState } from "react";

export default function Timer() {
  // Store the elapsed time.
  const [time, setTime] = useState(0);

  // Store the current input value.
  const [name, setName] = useState("");

  // Start the interval after the component mounts.
  useEffect(() => {
    const intervalId = setInterval(() => {
      setTime((previousTime) => previousTime + 1);
    }, 1000);

    // Stop the interval when the component unmounts.
    return () => {
      clearInterval(intervalId);
    };
  }, []);

  // Update the name when the input changes.
  const handleChange = ({ target }) => {
    setName(target.value);
  };

  // Render the timer and controlled input.
  return (
    <>
      <h1>Time: {time}</h1>

      <input
        value={name}
        onChange={handleChange}
      />
    </>
  );
}
```

---

# Common Mistakes

## Starting Ongoing Work in the Component Body

Incorrect:

```jsx
export default function Timer() {
  // This creates another interval during every render.
  const intervalId = setInterval(() => {
    // Timer work
  }, 1000);

  return <h1>Timer</h1>;
}
```

The component function runs during every render, so another interval is created each time.

Correct:

```jsx
useEffect(() => {
  // Start one interval after mounting.
  const intervalId = setInterval(() => {
    // Timer work
  }, 1000);

  // Remove the interval during cleanup.
  return () => {
    clearInterval(intervalId);
  };
}, []);
```

## Forgetting to Return Cleanup

Incorrect:

```jsx
useEffect(() => {
  window.addEventListener("online", handleOnline);

  () => {
    window.removeEventListener("online", handleOnline);
  };
}, []);
```

Correct:

```jsx
useEffect(() => {
  window.addEventListener("online", handleOnline);

  return () => {
    window.removeEventListener("online", handleOnline);
  };
}, []);
```

## Putting Cleanup Outside the Effect

Incorrect:

```jsx
useEffect(() => {
  const intervalId = setInterval(() => {
    // Timer work
  }, 1000);
}, []);

return () => {
  clearInterval(intervalId);
};
```

Correct:

```jsx
useEffect(() => {
  const intervalId = setInterval(() => {
    // Timer work
  }, 1000);

  return () => {
    clearInterval(intervalId);
  };
}, []);
```

## Forgetting the Dependency Array

```jsx
useEffect(() => {
  // This effect runs after every render.
});
```

For an effect intended to run once after mounting:

```jsx
useEffect(() => {
  // This effect runs after the initial render.
}, []);
```

## Assigning Instead of Calling

Incorrect:

```jsx
useEffect = (() => {
  // Effect work
}, []);
```

Correct:

```jsx
useEffect(() => {
  // Effect work
}, []);
```

## Immediately Undoing Setup

Incorrect:

```jsx
useEffect(() => {
  window.addEventListener("online", handleOnline);
  window.removeEventListener("online", handleOnline);
}, []);
```

Correct:

```jsx
useEffect(() => {
  window.addEventListener("online", handleOnline);

  return () => {
    window.removeEventListener("online", handleOnline);
  };
}, []);
```

## Discarding an Interval ID

This starts an interval but does not save its ID:

```jsx
setInterval(() => {
  // Timer work
}, 1000);
```

This saves the ID:

```jsx
const intervalId = setInterval(() => {
  // Timer work
}, 1000);
```

## Confusing Component Timing With Effect Timing

Incorrect mental model:

> The component runs once because the effect has `[]`.

Correct mental model:

* The component may render many times.
* The effect with `[]` runs once after the initial render.
* The cleanup runs when the component unmounts.

## Returning Cleanup From the Component

Incorrect:

```jsx
export default function Timer() {
  return () => {
    clearInterval(intervalId);
  };

  return <h1>Timer</h1>;
}
```

The first return ends the component function. The JSX is unreachable.

Correct:

```jsx
export default function Timer() {
  useEffect(() => {
    const intervalId = setInterval(() => {
      // Timer work
    }, 1000);

    return () => {
      clearInterval(intervalId);
    };
  }, []);

  return <h1>Timer</h1>;
}
```

---

# `useEffect` Review Checklist

Before considering an effect finished, check:

* [ ] Is `useEffect` being called rather than assigned?
* [ ] Is the effect callback inside `useEffect()`?
* [ ] Is the dependency array present?
* [ ] Does the dependency array match the intended behavior?
* [ ] Does the effect start something that continues running?
* [ ] If so, is there a cleanup function?
* [ ] Is the cleanup function returned?
* [ ] Is the cleanup function inside the effect?
* [ ] Can the cleanup function access the resource it must clean up?
* [ ] Does the cleanup operation match the setup operation?
* [ ] Is the effect above the JSX return?
* [ ] Is the component still returning JSX?
* [ ] Are the logical sections commented?

---

# Timer Review Checklist

* [ ] The interval is created inside `useEffect`.
* [ ] `setInterval()` is assigned to a variable.
* [ ] The interval callback updates state correctly.
* [ ] Previous state is used when the next value depends on it.
* [ ] The cleanup function is returned from the effect.
* [ ] `clearInterval()` receives the stored interval ID.
* [ ] The dependency array is present.
* [ ] The interval is not created directly in the component body.
* [ ] The component has a reachable JSX return.
* [ ] Logical sections contain comments.

---

# Debugging Questions

## Structure

* Is the code inside the component?
* Is the effect above the JSX return?
* Are the braces and parentheses correctly matched?
* Is cleanup inside the effect?
* Is the JSX return reachable?

## Rendering

* Is this code running directly in the component body?
* Will it run again on every render?
* Is a state update causing another render?

## Effects

* Is the dependency array missing?
* Is the dependency array empty when it should contain a value?
* Does the effect create something repeatedly?
* Does cleanup run before the effect reruns?

## Scope

* Where was this variable declared?
* Can the cleanup function access it?
* Is the variable trapped inside a different block or function?

## Functions

* Am I calling the function?
* Am I assigning to the function name by mistake?
* Am I passing a function or calling it immediately?
* Does the function return a value I need to store?

## Timers

* Did I save the interval ID?
* Did I pass that exact ID to `clearInterval()`?
* Is more than one interval being created?
* Is the interval being created during every render?

---

# Future Me

If this material has become fuzzy, remember:

* A component function can run many times.
* An effect runs after rendering.
* `[]` applies to the effect, not the component.
* Cleanup must be returned from inside the effect.
* React saves the cleanup function for later.
* Cleanup runs before an effect reruns and when the component unmounts.
* `setInterval()` returns an ID.
* `clearInterval()` needs that ID.
* Ongoing work should not begin directly in the component body.
* The component must still return JSX.
* Add comments even when an exercise platform does not require them.

Do not overcomplicate the timer pattern:

```jsx
// Start the timer after mounting.
useEffect(() => {
  const intervalId = setInterval(() => {
    setTime((previousTime) => previousTime + 1);
  }, 1000);

  // Stop the timer during cleanup.
  return () => {
    clearInterval(intervalId);
  };
}, []);
```

The effect builds it.

The cleanup tears it down.
