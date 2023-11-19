# Prefetching

### **主題：**The easy, fast and native way

```tsx
/*
	The easy way is HTML <head> with rel="preload". It’s easy, fast and native.
*/

<link rel="preload" href="/api/data" as="fetch" crossorigin="anonymous">
```

---

### **主題：**SWR preload

```tsx
/*
	Just call the preload function before render component.

	preload just a function that could call at the hook or event handler.
*/
import { useState } from 'react'
import useSWR, { preload } from 'swr'
 
const fetcher = (url) => fetch(url).then((res) => res.json())
 
// Preload the resource before rendering the User component below,
// this prevents potential waterfalls in your application.
// You can also start preloading when hovering the button or link, too.
preload('/api/user', fetcher)
 
function User() {
  const { data } = useSWR('/api/user', fetcher)
  ...
}
 
export default function App() {
  const [show, setShow] = useState(false)
  return (
    <div>
      <button onClick={() => setShow(true)}>Show User</button>
      {show ? <User /> : null}
    </div>
  )
}
```

---

### **主題：**SWR preload with react suspense

```tsx
/*
	The below useSWR hooks will suspend the rendering in the component,
	
	but the requests to `/api/user` and `/api/movies` have started by `preload` already, so the waterfall problem doesn't happen.
*/
import useSWR, { preload } from 'swr'
 
// Should call before rendering
preload('/api/user', fetcher);
preload('/api/movies', fetcher);
 
const Page = () => {
	// Both of data has preload alreay.
  const { data: user } = useSWR('/api/user', fetcher, { suspense: true });
  const { data: movies } = useSWR('/api/movies', fetcher, { suspense: true });

  return (
    <div>
      <User user={user} />
      <Movies movies={movies} />
    </div>
  );
}
```