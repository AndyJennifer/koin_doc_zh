---
title: KOIN系列之使用日志(十）
tags:
  - koin
categories:
  - koin
---

Koin 有一个简单的日志 API 来记录任何 Koin 活动(分配、查询……)。日志 API 由下面的类表示:

```kotlin
abstract class Logger(var level: Level = Level.INFO) {
  
    abstract fun log(level: Level, msg: MESSAGE)

    fun debug(msg: MESSAGE) {
        log(Level.DEBUG, msg)
    }

    fun info(msg: MESSAGE) {
        log(Level.INFO, msg)
    }

    fun error(msg: MESSAGE) {
        log(Level.ERROR, msg)
    }
}

```

Koin 针对于不同的目标平台，提出了以下的日志实现:

- `PrintLogger` —记录在控制台中(包含在 `koin-core` 项目中)
- `EmptyLogger` —什么都不记录(包含在 `koin-core` 项目中)
- `SLF4JLogger` —使用 SLF4J 进行日志记录。在 ktor 和 spark 中使用( `koin-logger-slf4j` 项目)
- `AndroidLogger` —使用 Android Logger记录(包含在 `koin-android` 中)

### 开启日志

默认情况下，Koin 使用 `EmptyLogger` 。你可以直接使用 `PrintLogger`, 如下所示:

```kotlin
startKoin{
    logger(LEVEL.INFO)
}
```
