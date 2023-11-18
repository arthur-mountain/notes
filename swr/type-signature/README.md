# Type Signature

(這主題比較深硬，給自己筆記用的，可跳過)

```tsx
/*
	pseudo code for introduce swr hook type signature
*/
const { data, error, isLoading, isValidating, mutate } = useSWR(key, fetcher, options)

type SWRKey = string | any[] | Object | Function | null;
type SWRFetcher = (key: SWRKey) => Promise<Data>;

type useSWR<Data, Error = any> = (
	// A unique key for the request
	key: SWRKey,
	// A Promise returning function to fetch your data
	fetcher: SWRFetcher,
	// An object of options for this SWR hook
	options: SWRConfiguration<Data, Error>,
) => {
	// The data from fetcher that return resolved.
	data: Data | undefined;
	// The Error thrown by fetcher.
	error: Error | undefined;
	// The request is fetching.
	isLoading: boolean;
	// The request is revalidation(re-fetching).
	isValidating: boolean;
	// An data reutrning function and mutate cache data directly.
	mutate: (data: Data, options: SwrConfiguration) => Data; 
}

type SWRConfiguration<Data, Error> = {
	// Enable React Suspense mode.
	suspense: boolean;

	/*
		1. If true, only refetches if there's any cache data else it will not refetch.
		2. If false, revalidate data every time useSwr call.
	*/
	revalidateOnMount: boolean;
	// Revalidate data when the window focused.
	revalidateOnFocus: boolean;
	// Revalidate when network connected.
	revalidateOnReconnect: boolean;

	// Polling data in the interval time.
	refreshInterval: number | (lastCachedData: Data) => number; 
	// Polling data when window is invisible(if refreshInterval is enabled).
	refreshWhenHidden: boolean;
	// Polling data when the browser is offline(determined by navigator.onLine).
	refreshWhenOffline: boolean;

	// Dedupe requests with the same key in milliseconds.
	dedupingInterval: number;

	// The timeout to trigger the onLoadingSlow event in milliseconds.
	loadingTimeout: number;

	// Should retry when fetcher has an error.
	shouldRetryOnError: boolean;
	// Error retry interval in milliseconds.
	errorRetryInterval: number;
	// Max error retry count.
	errorRetryCount: number;

	// Multiple fallback data object.
	fallback: { ['swr-key': string]: Different data type };
	// The initial data to be returned(only for this is per-hook)
	fallbackData: Data;

	// If the swr hook key changed, return data of the previous key until the new data has been loaded.
	keepPreviousData: boolean;

	// Callback function when a request exceed the loadingTimeout.
	onLoadingSlow:(key: SWRKey, config: SWRConfiguration<Data>) => void;
	// Callback function when a request finishes successfully.
	onSuccess:(data: Data, key: SWRKey, config: SWRConfiguration<Data>) => void;
	// Callback function when a request returns an error.
	onError:(data: Data, key: SWRKey, config: SWRConfiguration<Data>) => void;
	onErrorRetry: (
		err: Error,
		key: SWRKey,
		config: SWRConfiguration<Data>,
		revalidate: SWRFetcher,
		revalidateOps: SWRConfiguration<Data>
	) => void;

 	// Callback function when a request is ignored due to race conditions
	onDiscarded(key: SwrKey) => void;

	// Comparison function used to detect when returned data has changed, to avoid spurious rerenders.
	compare(a:Data, b:Data) => Data;
	
	// Function to detect whether pause revalidations, will ignore fetched data and errors when it returns true. Returns false by default.
	isPaused: () => void;
	// Array of middleware functions.
	use: (
		(useSWRNext: (key, fetcher, config) => useSWR) => (key, fetcher, config) => useSWR
	)[]
}
```