---
title: KOIN系列之Properties(十一）
tags:
  - koin
categories:
  - koin
---

Koin 可以处理环境或外部属性文件的参数配置，并可以帮助你将值注入到你自己的定义中。

### 开始加载属性

你可以加载几种类型的参数配置:

- 环境属性配置 - 加载系统参数
- koin.peroperties - 从 `/src/main/resources/koin.perperties` 文件中加载参数配置
- “额外的”启动参数配置 - 在 `startKoin` 函数中传递的 map 键值对。

### 从 module 中读取配置

在 Koin 的 module 中，你可以通过配置参数的 key 值来获取属性值：

例如在 /src/main/resoucres/koin.properties 文件中

```java
// Key - value
server_url=http://service_url
```

只需要调用 `getProperty` 函数来加载它：

```kotlin
val myModule = module {

    // 通过使用 “server_url" key 来获取其相关值
    single { MyService(getProperty("server_url")) }
}

```
