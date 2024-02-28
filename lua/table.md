# Table

1. Create a table

```lua
me = {
  name = "arthur",
  age = 26,
  birth = {month = 2, day = 4},
  slogan = "good morning",
  say = function (self)
    print(self.name .. ": " .. self.slogan)
  end
}

me:say() --> arthur: good morning
```

2. Table inherit

```lua
-- 透過 metatable 底下的 __index
-- 可以是另一個 table or 另一個 function
-- 當 key 在當前 table 找不到時，會去找 __index 的值
-- 因此可以模擬繼承
setmetatable(me, {
               __index = function(key)
                 return "default"
               end
})

-- abc 不在 me table 裡，所以會去找 __index
print(me.abc) --> default
print(me.name) --> arthur
```

```lua
-- __newindex 只有在添加新欄位時被觸發
-- 類似於 JS proxy object
-- 而原本設定新值會受到__newindex影響，所以會需要使用更底層的方式指定值(rawset)。這並不是經常使用的方式，但這裡有必要這樣做。
me.__newindex = function (self, key, new_value)
  local read_only<const> = { -- Mark read_only
    company = true
  }

  if not read_only[key] then --> Check is read_only key
    rawset(self, key, new_value)
  end
end
```

3. Key 除了 nil or NaN, 可以是任何值

```lua
obj = {} -- create an empty table
obj[1] = 1
obj[1.0] = 2 -- P.S. this will override obj[1]
obj["string"] = 1
obj[math.huge] = 1

----------------

print(obj[nil]) --> nil
obj[nil] = 1 --> Error: nil不是合法的key值，儘管取值不會出錯

print(obj[0/0]) --> nil
print(0/0) --> -nan
obj[0/0] = 1 --> Error: NaN不是合法的key值
```

4. Value 除了 nil, 可以是任何值

```lua
-- nil表示該key不存在，也會被當作刪除該key
obj[1] = nil -- 刪除`obj[1]`
print(obj[1]) --> nil --表示obj[1]不存在



```
