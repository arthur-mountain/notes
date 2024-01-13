**Component And Data**

1. 把資料先轉換過後，再丟進 component 內
    1. 白話一點就是：聽 component 的，照著 component 的規則走
    2. 規則是指 component interface(props 的 type definition)
    3. 對我來說，component 會有種 [Facade pattern](https://refactoring.guru/design-patterns/facade) 的感覺
    
2. 把資料直接丟進 component 內，component 內要轉換資料
    1. 白話一點就是：不管怎樣，無腦丟進去就對了，component 內自己會處理(data processing or adapter data)
    2. 同樣會有一套規則(type definition)，但 component 自己會轉換成這套規則所需的資料結構
    3. 對我來說，component 會 implement adapter function
    即 component 自行包(or 處理) 一層，類 [adapter pattern](https://refactoring.guru/design-patterns/adapter)

影響：

第一種作法：

1. 會需要 data processing 的 function
2. 如果這個 component 在不同 repo 下被使用，且 data processing function 沒辦法被 shared 的話，那可能會需要再不同 repo 下重複 implement
3. Component 很單純，即 Pure UI Component 的感覺
    1. 只要界定一個介面(即 props type definition)，外面的人(或者說要使用這個 component 的地方) 就是要照它的規則走

第二種作法：

1. 不用 implement duplicate processing function
2. 如果再不同 repo 下使用，也可以無腦丟資料進去，因為 component 會自行 adapter data
    1. Component 要先 implement 該資料要走哪個 adapter 規則的情況下才成立
    2. 因為每個無腦丟進來的資料不會一樣或類似，因此 data processing function 要建立不同規則(switch case) 來處理不同的資料結構
3. Component 會處理 UI 以外的邏輯，可能只是單純的 data processing，但相對的不太算是 Pure UI Component

結論：

一開始我是使用第一種方法的也是我習慣使用的，
但其實我自己也是覺得第二種作法的比較好一點

雖然 component 不單純了，但其實只是 component 內需要多 implement adapter function 除了上述的影響外，其實在 test 上也很單純(通常好測試的情況下，其實可以表示這個 function or component 都還算是單純的)

那為什麼明明覺得第二種比較好，卻還是以第一種的方式去實作呢，

主要是因為，

1. 以開源專案來看，通常會是第一種方法，
    
    如果我們開發者想用他們實作出來的 library 的話，就得照他們的規則走
    
2. 走第一種方法即 facade pattern 的方式的話，
    1. Component 的本身變動很少，因為規則已經定義好了
    2. 剩下就是給要使用的人去煩惱，怎麼符合 component expose 出來的規則
    3. 因為每個要 adapter 的資料結構都不一樣，把資料處理在放到外層
    
    那每個資料處理 function 就符合單一原則，因為只要外層自己處理好自己的資料結構即可
    4. 感覺好像也不賴(?)，只要丟進來的資料符合規則，就可以確保一定不會壞掉
    5. 若原始資料有改變，只要改對應的 data processing function 即可
        1. 以單一原則來看是好的

但其實主要還是根據使用情境來看就是了

因為改成第二種寫法後，能夠很明確的覺得程式整體變簡單了

(即使 component 做了一定程度上的封裝，不是純 UI Component)

P.S.

第一種作法不是不好，如果 data processing function 是能在多個 repo 共用的話，其實就跟第二種作法是一樣的意思了