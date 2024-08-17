# Cache

cache-control 跟 expires 是強緩存，直接在客戶端決定有沒有過期，過期就重新請求，沒過期就續用

if-modified-since 跟 if-none-match:是弱緩存（協商緩存），要跟資源提供方確認有沒有過期，如果過期就會回傳200新資源，沒過期的話就會回傳304，且不帶任何 byte content
