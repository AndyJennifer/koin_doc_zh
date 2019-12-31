---
title: KOIN系列之实验性功能(十二）
tags:
  - koin
categories:
  - koin
---

`koin-core-ext` 项目为 Koin 带来了扩展和实验性功能。

### 更好的定义声明

Koin DSL 可以看作是 “手动的”，你必须使用 “get()” 函数解析所需的实例并填入构造函数中。如果您的定义不需要构造函数集成特殊的定义(列如，注入参数或特殊的 scope Id)，我们可以使用下面的 API 实现更紧凑的编写风格。

>使用反射并不是没有代价的。但使用反射代码可以代替你不想写的东西(寻找主构造函数，注入参数……)。如果您使用的是对性能有一定要求的平台(例如 Android)，那么在使用之前，你一定要注意这一点。

### 使用 create() 构建任意对象

第一个需要介绍的函数是 create() 函数

与之前直接使用 `get()` 函数来解析定义中的依赖参数，并实例化对象不同。

```kotlin
module {
    single { ComponentA(get() ...) }
}
```

你可以使用 `create()` 函数从其主构造函数构建实例，并填充所需的依赖。

```kotlin
module {
    single { create<ComponentA>() }
}
```

### 更简单的 DSL 声明

如果你只需要单一的的功能，没有任何表达式。那么你可以使用 `create()` 函数来更“紧凑”的表示法：

```kotlin
module {
    single<ComponentA>()
}
```

如果你有一个实现类型，并希望用一个目标类型来解决，你可以使用以下 `singleBy` 函数:

```kotlin
module {
    singleBy<Target,Implementation>()
}
```

>适用于 single, factory & scope

注意：如果您使用自定义构造函数表达式，如注入参数或其他，不要使用反射 API 。
