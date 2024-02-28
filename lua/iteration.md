# Iteration

1. for-do

```lua
-- 條件之間用 , 隔開
-- 1 、 -1 相當於 ++ or --
for i = 1, 10, 1 do
  print(i)
end

for i = 10, 1, -1 do
  print(i)
end
```

2. for-in

```lua
-- for-in + (ipairs or pairs) iterate array or table

-- Notice: array index start at 1 in lua
array = {"one", "two", "three"}

for i, v in ipairs(array) do
  print(i, v)
end
--[[
result:
1 one
2 two
3 three
--]]
```

```lua
tb = {
  ["key1"] = "one",
  ["key2"] = "two",
  ["key3"] = "three",
}

for k, v in pairs(tb) do
  print(k, v)
end
--[[
result:
key1 one
key2 two
key3 three
--]]
```

3. repeat-until

```lua
day = 0
completed = 0
repeat
  completed = completed + day
  print("第" .. day .. "天： 寫 learning 文章，完成"..completed.."篇")
  day = day + 1
until completed > 60
```

4. while-do

```lua
destination = 10
current_pos = 0
while current_pos < destination do
  current_pos = current_pos + 1
  print(current_pos)
end
```
