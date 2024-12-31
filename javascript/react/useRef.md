```javascript
<div ref={outerDivRef}>Hello!</div>;
// in this case, outerDivRef is like the following
// It is a persistent object that contains a single property, current, which React uses to store the reference value.
outerDivRef = { current: <div> } // After the DOM element is mounted
```

#### More detailed example

```javascript
import React, { useRef, useEffect } from "react";

function App() {
  const outerDivRef = useRef(); // Step 1: Create a ref object

  useEffect(() => {
    console.log("outerDivRef:", outerDivRef); // Logs the ref object: { current: <div> }
    console.log("outerDivRef.current:", outerDivRef.current); // Logs the <div> DOM element
  }, []);

  return <div ref={outerDivRef}>Hello!</div>; // Step 2: Assign ref to the DOM element
}
```

```javascript
{
  current: <div>Hello!</div>;
} // outerDivRef
<div>Hello!</div>; // outerDivRef.current
```
