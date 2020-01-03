---
title: KOIN系列之在Android中使用Koin（十四）
tags:
  - koin
categories:
  - koin
---

`Koin-Android` 项目致力于为 Android 领域提供 Koin 支持。

### 在你的 Application 调用 startKoin()

在你的 `Application` 中，你可以使用 `startKoin` 函数将 Android context 通过 `androidContext` 注入 ，如下所示:

```kotlin
class MainApplication : Application() {

    override fun onCreate() {
        super.onCreate()

        startKoin {
            // Koin中的 Android 日志记录
            androidLogger()
            //注入 Android context
            androidContext(this@MainApplication)
            //使用的 module
            modules(myAppModules)
        }

    }
}
```

### 在其他地方使用 Android context 并启动 Koin

如果你需要从另一个 Android 类中启动 Koin，你可以使用 `startKoin` 函数，并提供你的Android `Context` 实例，如下所示：

```kotlin
startKoin {
    //注入 Android context
    androidContext(/* 你自己的 android context */)
    //使用的 module
    modules(myAppModules)
}
```

### Koin 日志记录

在你的 `KoinApplication` 实例中，我们有一个扩展 `androidLogger`，其内部使用的是 `AndroidLogger` 对象，（这个logger 是 Koin logger 的一个 Android 实现）。

如果这个 logger 不适合你的需求，你可以按需修改。

```kotlin
class MainApplication : Application() {

    override fun onCreate() {
        super.onCreate()

        startKoin {
            //注入 Android context
            androidContext(/* 你自己的 android context */)
            //使用 Android 下的日志 默认是 INFO 级别
            androidLogger()
            //使用的 module
            modules(myAppModules)
        }
    }
}
```

### 属性

你可以在`assets/koin.properties`文件中使用 Koin 属性，并在该文件中存储键值对。

使用 Koin 额外的属性。

```kotlin
// Shut off Koin Logger
class MainApplication : Application() {

    override fun onCreate() {
        super.onCreate()

        startKoin {
            //注入 Android context
            androidContext(/* 你自己的 android context */)
            //使用 Android 下的日志 默认是 INFO 级别
            androidLogger()
            //使用从 assets/koin.proerties中获取的属性。
            androidFileProperties()
            //使用的 module
            modules(myAppModules)
        }
    }
}

```
