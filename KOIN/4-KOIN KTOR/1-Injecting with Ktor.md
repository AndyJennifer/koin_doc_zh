---
title: KOIN系列之koin-ktor（二十）
tags:
  - koin
categories:
  - koin
---

`koin-ktor` 项目致力于为 [Ktor](https://github.com/ktorio/ktor) 带来依赖注入。

### 安装Koin与注入

要启动 Koin 容器，需要使用 `installKoin()` 启动函数:

```kotlin
fun Application.main() {
    // Install Ktor features
    install(DefaultHeaders)
    install(CallLogging)
    install(Koin) {
        slf4jLogger()
        modules(helloAppModule)
    }

    //...
}
```

>你也可以从 Ktor 外部启动它，但是你不能与  `autoreload` 功能兼容。

在 `Application` 类中可以使用 KoinCompoent 相关功能：

```kotlin
fun Application.main() {
    //...

    // Lazy inject HelloService
    val service by inject<HelloService>()

    // Routing section
    routing {
        get("/hello") {
            call.respondText(service.sayHello())
        }
    }
}
```

在 `Routing` 类中：

```kotlin
fun Application.main() {
    //...

    // Lazy inject HelloService
    val service by inject<HelloService>()

    // Routing section
    routing {
        v1()
    }
}

fun Routing.v1() {

    // Lazy inject HelloService from within a Ktor Routing Node
    val service by inject<HelloService>()

    get("/v1/hello") {
        call.respondText("[/v1/hello] " + service.sayHello())
    }
}
```
