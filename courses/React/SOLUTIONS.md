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
