# Error Handling

1. pcall

```lua
function div(a,b)
  return a/b
end

ok, c = pcall(div, 10, 2)

if ok then
  print(c) --> Output: 5.0
end

 -- Error, without returned value
ok, c = pcall(div, 10, "str")

if not ok then
  print("Error Message: ")
  print(c)
end
```

2. xpcall

```lua
-- Allowed passed an error handler
function errorHandle(msg)
  print("Error Message"..msg)
  return .5
end

ok, c = xpcall(div, errorHandle, 10, "str")

print("----------")
print(ok, c) -- false 0.5
```
