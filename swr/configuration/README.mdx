# Configuration

範例ㄧ、Single Configuration

```tsx
/*
	Wrapped a SWRConfig provider with default configuration.
*/

<SWRConfig value={options}>
  <Component/>
</SWRConfig>

/*
	per-hook 提供的 config will override the configuration by SWRConfig provider.
*/
import useSWR, { SWRConfig } from 'swr'
 
function Dashboard () {
  const { data: events } = useSWR('/api/events')
  const { data: projects } = useSWR('/api/projects')
	// override 
  const { data: user } = useSWR('/api/user', { refreshInterval: 0 })
 
  // ...
}
 
function App () {
  return (
    <SWRConfig 
      value={{
        refreshInterval: 3000,
        fetcher: (resource, init) => fetch(resource, init).then(res => res.json())
      }}
    >
      <Dashboard />
    </SWRConfig>
  )
}
```

範例二、Nested Configurations

```tsx
/*
	Nested configurations
	
	SWRConfig merges the configuration from the parent context.

	If the config value is primitive will be override.

	If the config value is mergeable like object, will be merged.
*/

import { SWRConfig, useSWRConfig } from 'swr'
 
function App() {
  return (
    <SWRConfig
      value={{
        dedupingInterval: 100,
        refreshInterval: 100,
        fallback: { a: 1, b: 1 },
      }}
    >
      <SWRConfig
        value={{
					// will override the parent value since the value is primitive
          dedupingInterval: 200, 
					// will merge with the parent value since the value is a mergeable object
          fallback: { a: 2, c: 2 },
        }}
      >
        <Page />
      </SWRConfig>
    </SWRConfig>
  )
}

/*
	Final configuration is, {
    dedupingInterval: 200,
    refreshInterval: 100,
    fallback: { a: 2,  b: 1, c: 2 },
	}
*/
```

範例三、The configuration value is function

```tsx

/*
	The configuration is function will receive parent configuration as params.

	Could calculate or extend the configuration base on parent configuration.
*/

import { SWRConfig, useSWRConfig } from 'swr'
 
function App() {
  return (
    <SWRConfig
      value={{
        dedupingInterval: 100,
        refreshInterval: 100,
        fallback: { a: 1, b: 1 },
      }}
    >
      <SWRConfig
        value={parent => ({
          dedupingInterval: parent.dedupingInterval * 5,
          fallback: { a: 2, c: 2 },
        })}
      >
        <Page />
      </SWRConfig>
    </SWRConfig>
  )
}
 
/*
	Final configuration is, {
    dedupingInterval: 500,
    fallback: { a: 2, c: 2 },
	}
*/
```

---

### **主題：**Cache provider

```tsx
/*
	The SWR will cache data in the key-value pairs object that, default is a Map.
	
	We could maintain our own cache data.
*/

<SWRConfig value={{ provider: () => new Map() }}>
  <Dashboard />
</SWRConfig>
```

---

### **主題：**Access ****Global**** Configuration

```tsx
/*
	There has a hook could get the global configuration,

	include `mutate` or `cache`.

	If no SWRConfig founed will return default configuration.
*/
import { useSWRConfig } from 'swr'
 
function ChildComponent () {
  const { refreshInterval, mutate, cache, ...restConfig } = useSWRConfig()
 
  // ...
}
```