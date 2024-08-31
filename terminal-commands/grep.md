# grep

TBD

## Syntax

```bash
grep [options] [pattern] [file]
```

## Options

1. 不帶任何 options

```bash
# 找出 file 中 hello 字串
grep 'hello'
```

1. -v: 排除匹配的行

```bash
# 排除 file 中包含 test 的字串
grep -v '\.test'
```

2. -E: 允許後面 pattern 使用 regex

```bash
# 排除 file 中開頭為 test 的字串
grep -v -E '^test'
```

## Pattern

grep 的 pattern 有三種:

1. Fixed Strings: 不使用任何特殊符號

2. Regular Expressions: 使用 regex 並且需要 -E 選項
