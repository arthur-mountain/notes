# Array

1. Create an array

```lua
programming_languages = {
  "C",
  "C#",
  "C++",
  "Java",
  "Swift",
  "Python",
  "Haskell",
}

arr = {
  [0] = 1, -- This value will be ignored, cause array index start at 1
  [1] = 1,
  [2] = 1,
  [3] = 1,
  [4] = 1,
  [5] = 1,
}

print(#arr) --> 5
```

2. Index start at 1 in an array

```lua
print(programming_languages[1]) -- => Output: "C"
```

3. Length of array

```lua
print(#programming_languages) -- => Output: 7
```

4. Iterate an array

```lua
-- for do
for i=1, #programming_languages, 1 do
  print(i, #programming_languages[i], programming_languages[i])
end


-- for-in
for i in ipairs(programming_languages) do
  print(i, #programming_languages[i], programming_languages[i])
end


for i, lang in ipairs(programming_languages) do
  print(i, #lang, lang)
end
```

5. Append item into end of array

```lua
-- Append with new index
print(#programming_languages) --> Output: 7
programming_languages[#programming_languages + 1] = 'ECMAScript'
print(#programming_languages) --> Output: 8


-- Append with method of table.insert
print(#programming_languages) --> Output: 8
table.insert(programming_languages, 'Shell Script')
print(#programming_languages) --> Output: 9

```

6. Delete item from array

```lua
-- Set value to nil will be deleted
print(#programming_languages) --> Output: 9
programming_languages[#programming_languages] = nil
print(#programming_languages) --> Output: 8

-- Notice: Always delete the last item!!!
-- Otherwise the length of array will not be changed
print(#programming_languages) --> Output: 8
programming_languages[2] = nil -- delete "C#"
print(#programming_languages) --> Output: 8  ---- What's!?


-- Also the ipairs will not working expectly.
for i, lang in ipairs(programming_languages) do
  print(i, #lang, lang)
end
--[[
result:
1 1 C
--]]


-- But for-do is working, length not changed the nil value printed
for i=1, #programming_languages, 1 do
  print(i, programming_languages[i])
end
--[[
result:
1 C
2 nil
3 C++
4 Java
5 Swift
6 Python
7 Haskell
8 ECMAScript
--]]
```
