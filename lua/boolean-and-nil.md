# Nil

```lua
-- nil 的型別也是 nil
type(nil) -- => nil
```

# Boolean

1. true or false

2. nil and false 才是 falsy, 其他皆為 truthy(e.g. 0 or empty string or empty table)

```lua
if 0 then
  print("0 is true")
end

if {} then
  print("{} is true")
end

if "" then
  print [["" is true]]
end
```

3. Lua的邏輯運算與大多數語言相同，除了不等於~=

   p.s. lua 中也可以使用 and、not、or

```lua
if 1 ~= 2 then
  print "1 not equal 2"
end
```

總結：

1. 只有nil和false為falsy，其餘為truthy
2. nil的type為nil
