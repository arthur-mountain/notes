# Function

```lua
-- 宣告函式，以「end」結束。 兩種宣告方式都可以
function hello ()
  print("Arthur")
end

hello = function ()
  print("Arthur")
end
```

2. 函式是一等公民，可以當作參數傳遞，也有必包

3. 函式之不定常數

```lua
function hello (...)
  -- 使用 {...} 存成 members variable
  local members = {...}

  print("Member length: ", #members)

  for _, m in pairs(members) do
    print("Hi, "..m)
  end
end


hello("Arthur", "Daily")
```

4. IIFE

```lua
(function()
  print("It's IIFE in lua")
end)()
```

5. load(相當於 eval)

```lua

-- Create a global variable
load([[
  g1 = 1 -- create a global variable g1
]])()

print(g1) -- => Output: 1

-- Notice: load return a function
f = load[[g2 = 2]]
print(type(f)) -- => function

print(g2) -- => Output: nil
f()
print(g2) -- => Output: 2
```

6. pcall, xpcall

```lua
-- Similar to apply or call in functional programming
pcall(print, "Hello, World") -- Output: Hello, World
xpcall(print, function(msg)end, "Hello, Lua") -- Output: Hello, Lua
```
