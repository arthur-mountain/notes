# Dockerfile ARG

關於 Dockerfile 的 `ARG` 指令和用法

## 簡介

用來定義建置 Docker image 時的變數。

這些變數可以在 docker build 時由外部傳入，也可以在 Dockerfile 中設定一個預設值。

## Syntax

```dockerfile
FROM ubuntu

ARG MY_ARG # 定義一個 ARG 變數，但不賦予預設值

ARG MY_ARG2=default_value # 定義一個 ARG 變數，但賦予預設值

RUN echo ${MY_ARG} # 使用 ARG 變數

RUN echo ${MY_ARG2} # 使用 ARG 變數
```

在 docker build 的時候，透過 `--build-arg` 參數傳入變數值：

```bash
docker build --build-arg MY_ARG=value .  # 傳入 MY_ARG 變數值

docker build --build-arg MY_ARG2=value . # 雖然 MY_ARG2 有預設值，但可以透過 --build-arg override
```

## ARGs Scope

關於 ARG 變數的作用域

- 在 FROM 後的 ARG 變數僅在當前 FROM 之後，別的 FROM 之前的指令中有效.

  如果有使用 multi-stage build 的話，可能會有多個 FROM 指令

  ```dockerfile
    FROM ubuntu AS layer1

    ARG MY_ARG=my_arg

    RUN echo "result: ${MY_ARG}" # -> "result: my_arg"

    FROM ubuntu AS layer2

    # MY_ARG 僅在 layer1 中有效，所以 MY_ARG 在 layer2 中是空的
    RUN echo "result: ${MY_ARG}" # -> "result: "
  ```

- 定義在第一個 FROM 之前的 ARG 變數，僅在 FROM 中有效

  ```dockerfile
  ARG UNBUNTU_VERSION=latest

  # UNBUNTU_VERSION 在 FROM 中有效
  FROM ubuntu:${UNBUNTU_VERSION} AS layer1

  # 但 UNBUNTU_VERSION 在 layer1/layer2 內都會是空的
  RUN echo "result: ${UNBUNTU_VERSION}" # -> "result: "

  # UNBUNTU_VERSION 在 FROM 中有效
  FROM ubuntu:${UNBUNTU_VERSION} AS layer2

  RUN echo "result: ${UNBUNTU_VERSION}" # -> "result: "
  ```

## 補充: ARG vs ENV

- ARG 也會是個環境變數(env)，即用 printenv 是能看到 ARG 變數的

- 但 ARG 只存在於 build image 的過程中，當 image build 完成後，ARG 變數就不存在了，
  代表在 container 中是存取不到 ARG 變數的

- 如果需要在 container 中使用，則建議使用 ENV，ENV 會存在於 build image 和 container 中

  ```dockerfile
  # 這裡定義一個 ENV 變數
  ENV MY_ARG=${MY_ARG}
  ```

- 因為 ARG 變數只存在 build image 的過程，所以不能用在 CMD 指令中

  ```dockerfile
  FROM ubuntu:latest

  ARG MY_ARG

  # 因為 CMD 是會等到 docker run 的時候執行，而 ARG 只存在於 build image 的過程中
  CMD echo "${MY_ARG}" # 這樣是無法取到 MY_ARG 變數的

  # 如果要在 CMD 中使用，請使用 ENV，因為 ENV 會存在於 container 中
  ENV MY_ARG=${MY_ARG}
  CMD echo ${MY_ARG} # 這樣是可以取到 MY_ARG 變數的

  # 最後補充：
  # 1. CMD 會在 container start 的時候執行的一個指令
  # 2. 通常只會有一個 CMD，如果同時存在多個也 ok，但只有最後一個 CMD 會被執行
  # 3. CMD 通常最後會組成以 shell script 的方式執行，例如： CMD ["sh", "-c", "echo ${MY_ARG}"]
  ```
