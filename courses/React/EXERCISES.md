# React Exercises

This document contains practice exercises for concepts covered in `MANUAL.md`.

The exercises are kept separate so that `MANUAL.md` can remain a focused study and reference document.

Exercises should be completed one at a time. Solutions may be added after each exercise has been attempted and understood.

Exercises were generated with AI assistance.

Solutions should always be written manually.

---

## Exercise 1: Online Listener

Assume `handleOnline` already exists.

Write an effect that:

* Adds an `"online"` event listener to `window`
* Uses `handleOnline` as the handler
* Removes the event listener during cleanup
* Runs only after the initial render
* Contains comments

---

## Exercise 2: Resize Listener

Assume `handleResize` already exists.

Write an effect that:

* Adds a `"resize"` event listener to `window`
* Uses `handleResize` as the handler
* Removes the event listener during cleanup
* Runs once after mounting
* Contains comments explaining setup and cleanup

---

## Exercise 3: Timer

Assume `setCount` already exists.

Write an effect that:

* Starts an interval
* Increases the count every second
* Stores the interval ID
* Clears the interval during cleanup
* Runs once after mounting
* Contains comments

---

## Exercise 4: Changing Dependency

Assume `searchTerm` is stored in state.

Write an effect that:

* Logs the current `searchTerm`
* Runs after the initial render
* Runs again whenever `searchTerm` changes
* Does not include unnecessary cleanup
* Contains a comment explaining the dependency

---

## Exercise 5: Identify Cleanup

For each effect, decide whether cleanup is probably needed:

* Updating `document.title`
* Starting an interval
* Adding a keyboard event listener
* Logging a state value
* Opening a WebSocket
* Subscribing to notifications
* Starting a timeout

For each item that needs cleanup, identify the operation that would undo or stop it.

---

## Exercise 6: Remove an Item by Value

Assume `selectedItem` contains the value of the item that was clicked.

Write a state update that:

* Uses the callback form of the state setter
* Uses `filter()`
* Removes every item equal to `selectedItem`
* Does not mutate the current array
* Contains a comment

---

## Exercise 7: Remove an Item by Index

Assume `clickedIndex` contains the index of the item that was clicked.

Write a state update that:

* Uses the callback form of the state setter
* Uses `filter()`
* Removes only the item at `clickedIndex`
* Uses `_` for the unused item parameter
* Contains a comment

---

## Exercise 8: Add an Item to an Array

Assume `selectedItem` contains the item to add.

Write a state update that:

* Uses the callback form of the state setter
* Creates a new array
* Adds `selectedItem` to the beginning
* Preserves the current items
* Contains a comment

---

## Exercise 9: Toggle an Item

Assume `selectedItem` contains the clicked item.

Write a state update that:

* Checks whether `selectedItem` already exists
* Removes it if it exists
* Adds it if it does not exist
* Uses `includes()`
* Uses `filter()`
* Uses spread syntax
* Does not mutate the current array
* Contains a comment

---

## Exercise 10: Event Target

Write a `handleChange` function that:

* Receives an event object
* Reads `event.target.value`
* Updates `name` state
* Contains a comment

Then rewrite the same function using destructuring in the parameter.

---

## Exercise 11: Controlled Input

Assume `name` and `setName` already exist.

Write:

* A `handleChange` function
* An `<input>` element
* A `value` prop connected to `name`
* An `onChange` prop connected to `handleChange`
* Comments explaining the data flow

---

## Exercise 12: Previous State

Assume `count` and `setCount` already exist.

Write a state update that:

* Increases `count` by `1`
* Uses the callback form
* Uses a descriptive parameter name
* Contains a comment explaining why previous state is used

---

## Exercise 13: Dependency Array Comparison

For each effect, describe when it runs:

```jsx
useEffect(() => {
  console.log("A");
});

useEffect(() => {
  console.log("B");
}, []);

useEffect(() => {
  console.log("C");
}, [count]);
```

Explain the difference between:

* No dependency array
* An empty dependency array
* A dependency array containing `count`

---

## Exercise 14: Find the Structural Error

Identify what is wrong with this code:

```jsx
export default function Timer() {
  useEffect(() => {
    const intervalId = setInterval(() => {
      setTime((previousTime) => previousTime + 1);
    }, 1000);
  }, []);

  return () => {
    clearInterval(intervalId);
  };

  return <h1>{time}</h1>;
}
```

Explain:

* Why `intervalId` is unavailable
* Why the JSX return is unreachable
* Where the cleanup function belongs

---

## Exercise 15: Fix the Immediate Cleanup

Identify the problem and rewrite the effect correctly:

```jsx
useEffect(() => {
  window.addEventListener("online", handleOnline);
  window.removeEventListener("online", handleOnline);
}, []);
```

Your corrected version should:

* Keep the event listener active
* Return the cleanup function
* Run once after mounting
* Contain comments

---

## Exercise 16: Find the Missing `return`

Identify the problem and rewrite the effect correctly:

```jsx
useEffect(() => {
  window.addEventListener("resize", handleResize);

  () => {
    window.removeEventListener("resize", handleResize);
  };
}, []);
```

Explain why React does not recognize the arrow function as cleanup.

---

## Exercise 17: Timer Scope

Write a complete effect where:

* `intervalId` is declared inside the effect
* The cleanup function can still access it
* The interval updates state every second
* The interval is cleared on unmount
* Comments explain the variable scope

---

## Exercise 18: Component Body or Effect?

For each task, decide whether it belongs directly in the component body or inside `useEffect()`:

* Calculating a total from props
* Starting an interval
* Returning JSX
* Adding a window event listener
* Filtering an array for display
* Updating `document.title`
* Declaring an event handler
* Opening a WebSocket

Explain each choice.

---

## Exercise 19: Calling Versus Assigning

Identify which version is correct and explain why.

```jsx
useEffect = (() => {
  console.log("Hello");
}, []);

useEffect(() => {
  console.log("Hello");
}, []);
```

---

## Exercise 20: Build a Small Timer Component

Create a component that:

* Imports `useEffect` and `useState`
* Stores elapsed time in state
* Starts at `0`
* Increases once per second
* Stores the interval ID
* Clears the interval on unmount
* Displays the current time
* Contains comments for state, setup, cleanup, and rendering
