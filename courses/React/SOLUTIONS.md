## 1 - Online Listener
```
useEffect(() => {
    // Add event listener when component mounts
    window.addEventListener("online", handleOnline);

    // Remove event listener when component unmounts
    return () => window.removeEventListener("online", handleOnline);
    }
, [])
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
    // Clear interval
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

## 7 - Toggle an Item
```
const toggleItem = ({target}) => {
    const selectedItem = target.value;

    setItems((prev) => {
        // check if selectedItem has been clicked
        if (prev.includes(selectedItem)) {
            // if selected, remove item
            return prev.filter(item => item !== selectedItem);
        } else {
            // if not selected, add item
            return [selectedItem, ...prev];
        }
    });
}
```
