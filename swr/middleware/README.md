# Middleware

### **主題：**Basic Usage

```tsx
function myMiddleware (useSWRNext) {
  return (key, fetcher, config) => {
    // Before hook runs...
 
    // Handle the next middleware, or the `useSWR` hook if this is the last one.
    const swr = useSWRNext(key, fetcher, config)
 
    // After hook runs...
    return swr
  }
}

<SWRConfig value={{ use: [myMiddleware] }}>
 
// or...
 
useSWR(key, fetcher, { use: [myMiddleware] })
```

範例一、****Request Logger****

```tsx
function logger(useSWRNext) {
  return (key, fetcher, config) => {
	   
		// Add logger to the original fetcher.
    const extendedFetcher = (...args) => {
      console.log('SWR Request:', key)
      return fetcher(...args)
    }
 
    // Execute the hook with the new fetcher.
    return useSWRNext(key, extendedFetcher, config)
  }
}
 
// ... inside your component
useSWR(key, fetcher, { use: [logger] })
```

範例二、****Keep Previous Result****

```tsx
import { useRef, useEffect, useCallback } from 'react'
 
// This is a SWR middleware for keeping the data even if key changes.
function laggy(useSWRNext) {
  return (key, fetcher, config) => {
    // Use a ref to store previous returned data.
    const laggyDataRef = useRef()
 
    // Actual SWR hook.
    const swr = useSWRNext(key, fetcher, config)
 
    useEffect(() => {
      // Update ref if data is not undefined.
      if (swr.data !== undefined) {
        laggyDataRef.current = swr.data
      }
    }, [swr.data])
 
    // Expose a method to clear the laggy data, if any.
    const resetLaggy = useCallback(() => {
      laggyDataRef.current = undefined
    }, [])
 
    // Fallback to previous data if the current data is undefined.
    const dataOrLaggyData = swr.data === undefined ? laggyDataRef.current : swr.data
 
    // Is it showing previous data?
    const isLagging = swr.data === undefined && laggyDataRef.current !== undefined
 
    // Also add a `isLagging` field to SWR.
    return Object.assign({}, swr, {
      data: dataOrLaggyData,
      isLagging,
      resetLaggy,
    })
  }
}
```

---

### **主題：**Multiple middleware

```tsx
function Bar () {
  useSWR(key, fetcher, { use: [c] })
  // ...
}
 
function Foo() {
  return (
    <SWRConfig value={{ use: [a] }}>
      <SWRConfig value={{ use: [b] }}>
        <Bar/>
      </SWRConfig>
    </SWRConfig>
  )
}

// is equivalent to:
useSWR(key, fetcher, { use: [a, b, c] })

// The order of middleware executions
/*
enter a
  enter b
    enter c
      useSWR()
    exit  c
  exit  b
exit  a
*/
```