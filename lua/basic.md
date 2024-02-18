# Default is global variable

```lua
globalVariable = 'global'

print('glocal variable: ', globalVariable)

function print_local () {
  local localVariable = 'local'
  local localVariable2 <const> = 'local' -- const variable (local only)
  local localVariable3 <close> = 'local' -- close variable (local only)

  print('local variable: ', localVariable)
}

print_local()
```

## Data Type (default variable value is nil)

- nil
- boolean
- number
- string
- function
- userdata
- thread
- table
