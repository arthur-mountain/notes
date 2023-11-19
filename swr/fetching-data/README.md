# Fetching Data

### **主題：**基本使用

範例一、React useEffect Data Fetching

```tsx
/*
	如果用最簡單的 useEffect fetch data 的話，Fetch data at top level component and block all child render.
		
	當然可以用 useContext 來處理，但相對會變得更複雜，需要包一層 Provider、處理 useContext 及 re-render 問題
*/

function Page () {
  const [user, setUser] = useState(null)

  // Fetch data at top level component
  useEffect(() => {
    fetch('/api/user')
      .then(res => res.json())
      .then(data => setUser(data))
  }, [])

  // Block all child render
  if (!user) return <Spinner/>
 
  return <div>
    {/* multiple child component need user data */}
  </div>
	}
```

範例二、SWR Data Fetching

```tsx
/*
	1. Top level component don't fetch any data or pass data as props.

	2. Child components 只會依賴於它們所需的資料，且每個 component 都是獨立的.

	3. 如果swr fetch data 時 key 是一樣的，只會呼叫一次 api call，如下：child component 呼叫了 useUser(999)兩次，但兩次的 swr has same key, only one request sent to the api, the request will be deduped, cached and shared.
*/

const fetcher: Fetcher<User, string> = (...args) => fetch(...args).then(res => res.json())

function useUser (id) {
  const { data, error, isLoading } = useSWR<User, Error>(`/api/user/${id}`, fetcher)
 
  return {
    user: data,
    isLoading,
    isError: error
  }
}

// page component
function Page () {
  return <div>
    <Navbar />
    <Content />
  </div>
}
 
// child components
function Content () {
  const { user, isLoading } = useUser(999)

  if (isLoading) return <Spinner />

  return <h1>Welcome back, {user.name}</h1>
}
 
function Navbar () {
  return <div>
    ...
    <Avatar />
  </div>
}
 
function Avatar () {
  const { user, isLoading } = useUser(999)

  if (isLoading) return <Spinner />

  return <img src={user.avatar} alt={user.name} />
}
```

---

### **主題：**Data Fetching

範例ㄧ、Native web fetch

```tsx
const fetcher = url => fetch(url).then(r => r.json())
 
function App () {
  const { data, error } = useSWR('/api/data', fetcher)
  // ...
}
```

範例二、Axios

```tsx
import axios from 'axios'
 
const fetcher = url => axios.get(url).then(res => res.data)
 
function App () {
  const { data, error } = useSWR('/api/data', fetcher)
  // ...
}
```

範例三、GraphQL

```tsx
import { request } from 'graphql-request'
 
const fetcher = query => request('/api/graphql', query)
 
function App () {
  const { data, error } = useSWR(
    `{
      Movie(title: "Inception") {
        releaseDate
        actors {
          name
        }
      }
    }`,
    fetcher
  )
  // ...
}
```

---

### **主題：**Fetcher ****Arguments****

範例ㄧ、Single argument

```tsx
useSWR('/api/user', () => fetcher('/api/user'))

useSWR('/api/user', url => fetcher(url))

useSWR('/api/user', fetcher)
```

範例二、Multiple Arguments

```tsx
/*
	Use an array/object as key that will pass in fetcher as parameter,

	and also SWR knew the key change should revalidate.
*/
const { data: user } = useSWR(['/api/user', token], ([url, token]) => fetchWithToken(url, token))

// The wrong example like this:
// useSWR('/api/user', url => fetchWithToken(url, token))
// the key don't contain token, so the SWR don't revalidate when token change.
```

範例三、How to fetch data that depends on previous request

```tsx
/*
	Just pass the data to SWR, 

	If **key is null** the SWR knew to wait for the data back.
*/
const { data: user } = useSWR(['/api/user', token], fetchWithToken)
 
// ...and then pass it as an argument to another useSWR hook
const { data: orders } = useSWR(user ? ['/api/orders', user] : null, fetchWithUser)

/*
	Another way is pass an object as key directly, as same as above begavior.
*/
const { data: orders } = useSWR({ url: '/api/orders', args: user }, fetcher)
```

---

**主題：**Conditional Fetching

```tsx
/*
	The Fetcher should depends on others data.

	If the key is falsy value or returns a falsy value, 
	SWR will not start the request.
*/

// conditionally fetch
const { data } = useSWR(shouldFetch ? '/api/data' : null, fetcher)
 
// ...or return a falsy value
const { data } = useSWR(() => shouldFetch ? '/api/data' : null, fetcher)
 
// In this case `user.id` throws when `user` isn't loaded.
const { data: user } = useSWR('/api/user')
const { data: projects } = useSWR(() => '/api/projects?uid=' + user.id)

```