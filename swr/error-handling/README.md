# Error handling

```tsx
/*
	If the fetcher throw an error or rejected,

	the error have the value from the fetcher by the hook.

	
*/

const fetcher = url => fetch(url).then(r => r.json())
 
// ...
const { data, error } = useSWR('/api/user', fetcher)
```

---

### **主題：**Custom Error Object

```tsx
/*
	Just customize the error data then throw or reject it in the fetcher
*/
const fetcher = async url => {
  const res = await fetch(url)
 
  // If the status code is not in the range 200-299,
  // we still try to parse and throw it.
  if (!res.ok) {
    const error = new Error('An error occurred while fetching the data.')
    // Attach extra info to the error object.
    error.info = await res.json()
    error.status = res.status
    throw error
  }
 
  return res.json()
}
 
// ...
const { data, error } = useSWR('/api/user', fetcher)
// error.info === {
//   message: "You are not authorized to access this resource.",
//   documentation_url: "..."
// }
// error.status === 403
```

---

### **主題：**Error Retry

```tsx
/*
	SWR use exponential backoff algorithm to retry invoke the fetcher on error.

	If want to override this default behavior via the onErrorRetry option.

	If want to disable retry by setting shouldRetryOnError option to false.
*/

useSWR('/api/user', fetcher, {
  onErrorRetry: (error, key, config, revalidate, { retryCount }) => {
    // Never retry on 404.
    if (error.status === 404) return
 
    // Never retry for a specific key.
    if (key === '/api/user') return
 
    // Only retry up to 10 times.
    if (retryCount >= 10) return
 
    // Retry after 5 seconds.
    setTimeout(() => revalidate({ retryCount }), 5000)
  }
})
```

---

### **主題：Global Error Report**

```tsx
<SWRConfig value={{
  onError: (error, key) => {
    if (error.status !== 403 && error.status !== 404) {
      // We can send the error to Sentry,
      // or show a notification UI.
    }
  }
}}>
  <MyApp />
</SWRConfig>
```