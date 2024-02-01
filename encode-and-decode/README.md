# Summary

最近在學`字元編碼` 即 encode and decode，因為電腦只認 `0`、`1`但這通常是給電腦看的

因此就會透過某種編碼方式把圖片、音樂、影片…etc等，轉換成電腦能夠儲存和傳輸甚至顯示

---

常見的如：[ASCII](https://zh.wikipedia.org/wiki/ASCII)、[UTF-8](https://zh.wikipedia.org/wiki/UTF-8)(ASCII 的超集)、UTF-16、etc… 其實只是指某種編碼`方式 or 規則`

電腦是由英語系國家發展過來，一開始只要能夠涵蓋`英文大小寫`、`標點符號`

早期大多以 ASCII 編碼方式就可以了，以`七個位元(bit)表示一個字元`

但現在各種國家、語言，但這些文字，一個位元根本不夠存，

那如果改用兩個位元，是不是可以解決？   可以但

再更多其他國家的語言，兩位元不夠怎麼辦，三個位元？？？

這樣同時也是很浪費空間的一種做法，而且很不實際

因此就有了 UTF-8、UTF-16，它們以聰明的方法去做字元表來 mapping
這裡就不多做介紹了，有興趣可以去了解～～～ 我剛學一半而已

底下會用 base64 來介紹一下編碼，因為這個的規則比較少，很容易理解

---

# 先說明一個常被誤解的`加密、編碼`問題，

加密：為了保密而對資訊做一種轉換，但也是編碼的一種方式
(這裡有太多數學運算、密碼學等知識，我是真的應該學不會了，先跳過）

編碼： 透過某種規則(mapping表，我自己稱的，不知道是不是學術名稱，但應該就類似一種規則)，將資訊轉換成二進制，讓電腦、網路可以傳輸 or 儲存)，這是能被反轉的

因此 `base64` 只是一種編碼方式，只要有[對應的表](https://datatracker.ietf.org/doc/html/rfc4648#:~:text=Table%201%3A%20The%20Base%2064%20Alphabet)就能把 base64 的字傳進行反轉

---

base64 還有一種變體是 `base64url`，

因為 base64 的規則裡會使用到 `+`、`/`

但這兩個字元在 URL 裡是有意義的，

因此 base64url 也有自己[對應的表](https://datatracker.ietf.org/doc/html/rfc4648#:~:text=Table%202%3A%20The%20%22URL%20and%20Filename%20safe%22%20Base%2064%20Alphabet)(大都跟 base64一樣)，但會把 

`+` 換成 `-`

`/` 換成 `_`

---

Web 有
[atob(ascii to binary)](https://developer.mozilla.org/en-US/docs/Web/API/atob)、[btoa(binary to ascii)](https://developer.mozilla.org/en-US/docs/Web/API/btoa) 可以處理 base64轉換，

但這兩個函式並沒有辦法完全將任意的字串轉換成 base64，

它能處理的範圍只有 `0x00` 到 `0xFF` 而已，因為它誕生的比 web 還早，早期應該沒人覺得要支援二進制

P.S. 印象中之前開發，也有看到這兩個 func 被標記為 deprecate
（但應該像 js null 一樣成為歷史共業了吧??? 開玩笑的，不確定)

因此後面有出 [Encoding API](https://developer.mozilla.org/en-US/docs/Web/API/Encoding_API) ，被設計出來處理各種字元編碼轉換的，底下有幾種方法

TextEncoder 、TextDecoder、TextEncoderStream、TextDecoderStream，後兩者是以流(stream)的方式

```tsx
const encoder = new TextEncoder();
const encoded = encoder.encode("🤣");
console.log(encoded);
// Uint8Array(4) [240, 159, 164, 163]
```

NodeJs 目前只能先用 `Buffer` 操作，底下有 `toString` 可以轉換

```tsx
const encoded = Buffer.from("🤣").toString("base64" /* 這裡其實可以選擇很多種編碼方式，如：上述的 base64url */);

const decoded = Buffer.from("8J+kow==", "base64").toString();
```

而 Encoding API 是被 [WHATWG](https://encoding.spec.whatwg.org/) 提出的標準規格，~~NodeJs 應該會支援但不知道現在支援沒~~特定版本以上才 global 支援(v11.0.0)

---

表情符號也是類似的原理，所以才能被傳輸

---

**Refs:**

[https://just-func.com/blogs/base64](https://just-func.com/blogs/base64)

[https://liniju.medium.com/web-dev-roadmap2-bee711060ec8](https://liniju.medium.com/web-dev-roadmap2-bee711060ec8)

[https://pjchender.dev/webdev/note-unicode/](https://pjchender.dev/webdev/note-unicode/)
