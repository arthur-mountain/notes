# Mutate Data automatically

### **主題：**Type Signature(Mutate)

```tsx
useSWR type signature:
	https://www.notion.so/Type-Signature-3c2edc75852b44408160d879555c61c6

type MutateOptions = {
	// Data to immediately update the client cache.
	optimisticData: Data | (currentData: Data) => Data | Promise<Data>;
	
	// Should the cache revalidate once the asynchronous update resolves.
	revalidate: boolean;

	// The mutation result could be written to the cache or not.
	populateCache: boolean | (updatedResult: Data, currentResult: Data) => Data;

	// Should rollback or not
	rollbackOnError: boolean | (error: Error) => shouldRollback is boolean;	

	// Should the mutate call throw the error when failed.
	throwOnError: boolean;
}

type Mutate = (
	// Same as useSWR.
	key: SWRKey,

	// Data to update immediately the client cache
	data?: Data | (currentData: Data) => Data | Promise<Data>,

	// The mutate options.
	options?: MutateOptions,
) => Data;
```

---

### **主題：**Mutate by global

```tsx
/*
	**Recommended wat to get global mutator by `useSWRConfig` hook.**

	Another way is import mutate globally like, import { mutate } from "swr".

	The globally mutator key is required.

	mutate(
		key: SWRKey, // The cache key.
		data?: Data, // This data will replace immediately the data in the cache.
		options?: SWRConfiguration, // The mutate options like SWRConfiguration.
	)
*/
import { useSWRConfig } from "swr"
 
function App() {
  const { mutate } = useSWRConfig()
  mutate(key, data, options)
}
```

---

### **主題：**Mutate by per-hook

```tsx
/*
	The per-hook mutate bounded with current key.

	The per-hook mutate only need `data` and `options` as parameters.

	mutate(
		data?: Data,
		options?: SWRConfiguration,
	)
*/
import useSWR from 'swr'
 
function Profile () {
  const { data, mutate } = useSWR('/api/user', fetcher)
 
  return (
    <div>
      <h1>My name is {data.name}.</h1>
      <button onClick={async () => {
        const newName = data.name.toUpperCase()

        // Send a request to the API to update the data
        await requestUpdateUsername(newName)

        // Update the local data immediately but not revalidate (refetch)
        mutate({ ...data, name: newName })
      }}>Uppercase my name!</button>
    </div>
  )
}
```

---

### **主題：Revalidation when mutate**

```tsx
/*
	Only if the Mutate without data will trigger revalidation, like

	1. mutate(key) from globally mutate
	
	2. mutate() from per-hook mutate
*/

import useSWR, { useSWRConfig } from 'swr'
 
function App () {
  const { mutate } = useSWRConfig()
 
  return (
    <div>
      <Profile />
      <button onClick={() => {
        // Set the cookie as expired
        document.cookie = `
					token=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;
				`
 
        // Tell all SWRs with this key to revalidate
        mutate('/api/user')
      }}>
        Logout
      </button>
    </div>
  )
```

---

### **主題：Mutate with update function**

```tsx
/*
	Useful for POST or PUT or PUNCH that could update data to remote, and

	update the data to SWR cache.
*/

try {
  const user = await mutate('/api/user', updateUser(newUser))
} catch (error) {
  // Handle an error while updating the user here
}
```

---

### **主題：**Optimistic Updates

1. Update the local data immediately, send a request to update the data under the background.
2. Prefer with `rollbackOnError` option to sure the data is update correct in the database.

範例ㄧ、optimisticData is data that be used to update the cache data

```tsx
import useSWR, { useSWRConfig } from 'swr'
 
function Profile () {
  const { mutate } = useSWRConfig()
  const { data } = useSWR('/api/user', fetcher)
 
  return (
    <div>
      <h1>My name is {data.name}.</h1>
      <button onClick={async () => {
        const newName = data.name.toUpperCase()
        const user = { ...data, name: newName }
        const options = {
          optimisticData: user,
          rollbackOnError(error) {
            // If it's timeout abort error, don't rollback
            return error.name !== 'AbortError'
          },
        }
 
        // Updates the local data immediately
        // Send a request to update the data
        // Triggers a revalidation (refetch) to make sure our local data is correct
        mutate('/api/user', updateFn(user), options);
      }}>
				Uppercase my name!
			</button>
    </div>
  )
}
```

範例二、optimisticData is function could depending on the current data and return new data.

```tsx
import useSWR, { useSWRConfig } from 'swr'
 
function Profile () {
  const { mutate } = useSWRConfig()
  const { data } = useSWR('/api/user', fetcher)
 
  return (
    <div>
      <h1>My name is {data.name}.</h1>
      <button onClick={async () => {
        const newName = data.name.toUpperCase()

				// CurrentUser as parameter and return new user
        mutate('/api/user', updateUserName(newName), {
          optimisticData: currentUser => ({ ...currentUser, name: newName }),
          rollbackOnError: true
        });

      }}>Uppercase my name!</button>
    </div>
  )
}
```

---

### **主題：**Update cache after mutation

```tsx
/*
	If the api response will return new data, then could,
	enable populateCache to received,

	api response as updatedData in the first parameter,
	currentCacheData in the second parameter,

	and reutrn new Data.

	After we update cache depends on api response, then we couldn't need
	revalidate again.
*/

const updateTodo = () => fetch('/api/todos/1', {
  method: 'PATCH',
  body: JSON.stringify({ completed: true })
})
 
mutate('/api/todos', updateTodo, {
  populateCache: (updatedTodo, todos) => {
    // Filter the list, and return it with the updated item
    const filteredTodos = todos.filter(todo => todo.id !== '1')
    return [...filteredTodos, updatedTodo]
  },
  // Since the API already gives us the updated information,
  // we don't need to revalidate here.
  revalidate: false
})
```

---

### **主題：**Mutate data base on current data

```tsx
/*
	The async update function will received cuurent data as parameter

	then return new data.

	The response will return updated data so we couldn't need revalidate again. 
*/

mutate('/api/todos', async todos => {
  // let's update the todo with ID `1` to be completed,
  // this API returns the updated data
  const updatedTodo = await fetch('/api/todos/1', {
    method: 'PATCH',
    body: JSON.stringify({ completed: true })
  })
 
  // filter the list, and return it with the updated item
  const filteredTodos = todos.filter(todo => todo.id !== '1')
  return [...filteredTodos, updatedTodo]
// Since the API already gives us the updated information,
// we don't need to revalidate here.
}, { revalidate: false })
```

---

### **主題：Mutate Multiple Items**

```tsx
/*
	The key could filter by function retrun whuch key should revalidate,

	The key filter function will be applied to all of existing key.

	If multiple SWR cache match this key will revalidate.
*/

import { mutate } from 'swr'
// Or from the hook if you customized the cache provider:
// { mutate } = useSWRConfig()
 
mutate(
  key => typeof key === 'string' && key.startsWith('/api/item?id='),
  undefined,
  { revalidate: true }
)

useSWR(['item', 123], ...)
useSWR(['item', 124], ...)
useSWR(['item', 125], ...)
 
mutate(
  key => Array.isArray(key) && key[0] === 'item',
  undefined,
  { revalidate: false }
)
```

---

### **主題：Clear all cache data**

```tsx
/*
	The key fitler function always return true as wildcard

	and don't revalidate

	that will clear all of cache data.
*/
const clearCache = () => mutate(
  () => true,
  undefined,
  { revalidate: false }
)
 
// ...clear cache on logout
clearCache()
```