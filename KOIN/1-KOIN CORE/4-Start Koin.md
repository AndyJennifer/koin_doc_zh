---
title: KOIN系列之启动Koin（四）
tags:
  - koin
categories:
  - koin
---

### Koin是什么

Koin 是面向 Kotlin 开发人员的实用的轻量级依赖注入框架。用纯 Kotlin 编写，使用函数式解决方案。Koin 是一个DSL、一个轻量级容器和一个实用的API。

### 启动容器

Koin 是一个DSL、一个轻量级容器和一个实用的API。一旦你在 Koin 模块中描述了相关声明，那就可以启动Koin容器了。

### startKoin 函数

`startKoin` 函数是启动 Koin 容器的主要入口点。它需要运行 Koin 模块的列表。当模块被加载时，其内部定义的声明将会被 Koin 容器解析。

开启 koin

```kotlin
// 在 Global context 中启动一个 KoinApplication
startKoin {
    // 声明使用的 module
    modules(coffeeAppModule)
}
```

一旦 `startKoin` 被调用，Koin 将读取你所有声明的模块和声明。当你执行任意 `get()` 或`inject()` 方法时，Koin 将会获取你所需的实例对象。

你的 Koin 容器可以有几个选项:

- `logger`- 允许日志可用
- `properties()` 、 `fileProperties()` 或 `environmentProperties()` - 从环境、koin.perperite 文件，或其他配置中加载相关属性。

`startKoin 不能被多次调用，如果需要额外加载模块，请使用 loadKoinModules 函数。`

### 启动背后：KoinApplication 和 Koin 实例

当我们启动 Koin 时，我们会创建一个 Koin 容器配置对象--`KoinApplication` 。该对象一旦被加载，它将生成一个由你的模块和选项配置的 `Koin` 实例，这个 `Koin` 实例接着会在`GlobalContext` 中被注册，以便供任何`KoinComponen` 类使用。

### 在调用 startKoin 后加载模块

虽然我们不能多次调用 `startKoin` 函数。但是我们可以直接使用 `loadKoinModules()` 函数。

这对于希望使用 Koin 的 SDK 开发人员来说，这个函数很有趣，因为他们不需要使用 starKoin() 函数，只需在库的开头使用 `loadKoinModules` 即可。

```kotlin
loadKoinModules(module1,module2 ...)
```

### 卸载模块

我们可以卸载多个Modlue，使用给定函数来释放他们的实例对象:

```kotlin
unloadKoinModules(module1,module2 ...)
```

### Koin上下文隔离

对于 SDK 开发人员，你还可以以一种非全局的方式使用 Koin :将 Koin 用于 library 的 DI（依赖注入），并通过隔离上下文的方式，来避免同时使用 library 与 Koin 之间的冲突。

在标准模式下，我们可以这样启动 Koin:

```kotlin
//启动一个 KoinApplication 并在 Global context 中注册它
startKoin {
    //声明使用的 module
    modules(coffeeAppModule)
}
```

在这里，我们可以使用 `KoinComponent` :它将使用 `GlobalContext` 中的 Koin 实例对象。

但如果我们想使用一个被隔离的 Koin 实例，我们可以像下面这样声明:

```kotlin
// 创建一个 KoinApplication
val myApp = koinApplication {
    // 声明使用的 module
    modules(coffeeAppModule)
}
```

你必须保持你的 `myApp` 实例在你的库中可用，并将其传递给你的自定义 `KoinComponent` 实现:

```kotlin
// 从你的 Koin 对象中获取一个 context
object MyKoinContext {
    var koinApp : KoinApplication? = null
}

//注册 Koin context
MyKoinContext.koinApp = KoinApp

```kotlin
abstract class CustomKoinComponent : KoinComponent {
    // 覆盖默认的 Koin 实例, 在 GlobalContext 中实例化你的 koin 对象。
    override fun getKoin(): Koin = MyKoinContext?.koinApp.koin
}

现在，注册你自己的 context 并运行被隔离的 Koin 组件:

```kotlin
// Register the Koin context
MyKoinContext.koinApp = myApp

class ACustomKoinComponent : CustomKoinComponent(){
    // inject & get 将会指向 MyKoinContext
}

```

### 停止Koin -关闭所有资源

你可以关闭所有 Koin 资源以及删除实例对象和声明。为此，你可以在任何地方使用 `stopKoin()` 函数来停止 Koin `GlobalContext` 。或者在 `KoinApplication` 实例中调用 `close()`
