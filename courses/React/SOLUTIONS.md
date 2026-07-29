## 1 - Online Listener
```
useEffect(() => {
    // Add event listener when component mounts
    window.addEventListener("online", handleOnline);

    // Remove event listener when component unmounts
    return () => window.removeEventListener("online", handleOnline);
    }, []);
```

## 2 - Resize Listener
```
useEffect(() => {
    // Set up resize event listener
    window.addEventListener('resize', handleResize);

    // Clean up to prevent memory leaks
    return () => window.removeEventListener('resize', handleResize);
}, []);
```

## 3 - Timer
```
useEffect(() => {
    // Start interval
    const intervalId = setInterval(() => {
        setCount(prev => prev + 1);
    }, 1000);
    // Clear interval when component unmounts
    return () => clearInterval(intervalId)    
}, [])
```

## 4 - Changing Dependency
```
useEffect(() => {
    // Log current search term 
    console.log(searchTerm);
    // Run again when searchTerm changes
}, [searchTerm]);
```

## 5 - Cleanup Required?

| Effect | Cleanup |
|--------|---------|
| Updating `document.title` | Not needed |
| Starting an interval | `clearInterval()` |
| Adding a keyboard event listener | `removeEventListener()` |
| Logging a state value | Not needed |
| Opening a WebSocket | `socket.close()` |
| Subscribing to notifications | `unsubscribe()` (depending on the library) |
| Starting a timeout | `clearTimeout()` |

## 6 - Remove Item by Value
```
// Remove every item equal to selectedItem 
setCart((currentCart) => 
    currentCart.filter((item) => item !== selectedItem)
);
```

## 7 - Remove Item by Index
```
setCart((currentCart) => {
    // return array that excludes the clicked item
    return currentCart.filter((_, index) => index !== clickedIndex);
})
```

## 9 - Toggle an Item
```
const toggleItem = ({target}) => {
    const selectedItem = target.value;

    setItems((prev) => {
        // check if selectedItem has been clicked
        if (prev.includes(selectedItem)) {
            // Remove selected item from the array
            return prev.filter(item => item !== selectedItem);
        } else {
            // Add selected item to array
            return [selectedItem, ...prev];
        }
    });
}
```

## 10 - Event Target
```
const handleChange = (event) => {
    // Get name from user input
    setName(event.target.value);
};

// With destructuring
const handleChange = ({target}) => {
    setName(target.value);
};
```

## 11 - Controlling Input
```
// User inputs name
// onChange calls handleChange
// handleChange calls setName
// React updates name
// value={name} updates display

const handleChange = ({ target }) => {
    // Get name from input
    setName(target.value)
}

<div>
    <input
        type="text"
        value={name}
        onChange={handleChange}
    />
</div>
```

## 12 - Previous state
```
// Use the callback because the new state is 
// based on the previous state
setCount((previousCount) => previousCount + 1);
```

## 13 - Dependency Array Comparison
* No dependency array - The effect runs after every render
* Empty dependency array - The effect runs only on initial render
* A dependency array containing count - The effect runs on initial render and when count changes

## 14 - Structural Error
* `intervalId` is unavailable because it is declared inside `useEffect`, so it is outside the scope of the component-level cleanup function.
* The first `return` exits the component, so the JSX return is never reached.
* The cleanup function belongs inside `useEffect` and must be returned from it.

Corrected code:
```
export default function Timer() {
  useEffect(() => {
    const intervalId = setInterval(() => {
      setTime((previousTime) => previousTime + 1);
    }, 1000);
    return () => {
        clearInterval(intervalId);
    };
  }, []);

  return <h1>{time}</h1>;
```

## 15 - Fix Cleanup
```
useEffect(() => {
    window.addEventListener("online", handleOnline);
    // Clean up eventListener
    return () => {
        window.removeEventListener("online", handleOnline)
    }
}, [])
```
