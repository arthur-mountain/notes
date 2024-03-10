# Coroutine

1. Create a thread

```lua
t1 = coroutine.create(function() print("Hi, arthur") end)
print(type(t1)) -- Output: thread
```

2. Run thread

```lua
coroutine.resume(t1)
```

```lua
--  Run thread with parameters

function hello(name)
  while true do
    coroutine.yield("Hello, " .. name)
  end
end


t1 = coroutine.create(hello)
print(coroutine.status(t1)) -- suspended
print(coroutine.resume(t1, "Bob")) -- Output: Hello, Bob


print(coroutine.status(t1)) -- suspended
print(coroutine.resume(t1, "World")) -- Output: Hello, World
```

3. Close thread

```lua

coroutine.close(t1) --> true
coroutine.status(t1) --> dead
```

4. Yield thread

```lua
function genNumber(n)
  n = n or 0

  while n < 100 do
    n = n + 1
    coroutine.yield(n)
  end
end

t1 = coroutine.create(genNumber)
for i=1,3,1  do
  print(coroutine.resume(t1))
end
--[[
Output:
  true 1
  true 2
  true 3
--]]
```

```lua
function genNumber(n)
  n = n or 0

  while n < 100 do
    n = coroutine.yield() or n
    n = n + 1
    coroutine.yield(n)
  end

  return 'finish'
end

t1 = coroutine.create(genNumber)
for i=1,3,1  do
  coroutine.resume(t1)
  print(coroutine.resume(t1)) --> 1, 2, 3
end

coroutine.resume(t1)
print(coroutine.resume(t1,96)) -- 97
for i=1,3,1  do
  print(coroutine.resume(t1)) -->  98, 99, 100
end

print(coroutine.resume(t1)) --> finish
print(coroutine.status(t1)) --> dead

--[[
Output:
true 1
true 2
true 3
true 97
true 98
true 99
true 100
true finish
dead
--]]
```

4. Wrap thread as function

```lua
t1 = coroutine.wrap(genNumber)
print(type(t1)) --> function


for i=1,3,1  do
  t1()
  print(t1()) --> 1, 2, 3
end

t1()
print(t1(96)) -- 97
for i=1,3,1  do
  t1()
  print(t1()) -->  98, 99, 100
end

print(t1()) --> finish
```
