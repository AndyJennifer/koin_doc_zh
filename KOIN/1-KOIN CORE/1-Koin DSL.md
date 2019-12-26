---
title: KOIN系列之Koin是什么（一）
tags:
  - koin
categories:
  - koin
---

### KOIN 是什么

KOIN 是一个面向 Kotlin 开发人员的实用的轻量级依赖注入框架。用纯 Kotlin 编写，只使用函数解析:没有代理，没有代码生成，没有反射。Koin 是一个 DSL、一个轻量级容器和一个实用的API

### Koin DSL

由于 Kotlin 语言的强大功能，Koin 提供了一种 DSL 来帮助你描述应用程序，而不是对其进行使用注解或生成代码。通过使用 Kotlin DSL，Koin 提供了一个智能的函数式 API 来解决你的依赖注入。

#### Application & Module DSL

Koin 提供了几个关键字来描述 Koin Application 中的元素

- Application DSL :描述 Koin 容器配置
- Module  DSL :用来描述必须要注入的组件

#### Application DSL

一个 `KoinApplication` 是 Koin 容器配置对象。这将允许您配置日志记录、属性加载和模块。

要构建一个新的 `KoinApplication` ，可以使用以下函数:

- `koinApplication { }` 创建一个 `koinApplication` 容器配置对象
- `startKoin { }` 创建一个 `KoinApplication` 容器配置对象，并在  `GlobalContext` 中注册它，以便使用 GlobalContext 类中相关 API

要配置 `KoinApplication` 对象，可以使用以下任何函数:

- `logger()` 声明使用的日志实现(默认使用 EmptyLogger )及日志使用的级别
- `modules()` 设置要加载到容器中的 Koin 模块列表(list 或 vararg list)
- `properties()`  将 HashMap 属性(键值对）加载到 Koin 容器中
- `fileProperties()` 从给定文件中获取属性并加载到 Koin 容器中
- `environmentProperties()` 从操作系统环境加载属性并加载到 Koin 容器中

### KoinApplication 对象: 全局 vs 本地

综上所述：我们可以用两种方式描述 Koin 容器配置: `koinApplication` 或 `startKoin` 函数。

- `koinApplication` 描述了一个 Koin 容器对象
- `startKoin` 描述了一个 Koin 容器对象，并在 Koin 中的 `GlobalContext` 中注册
  
通过将容器配置对象（koinApplication）注册到 `GlobalContext` 中，这样我们就能直接使用它的全局 API 。任何 `KoinComponent（koin 组件）` 都将引用 `Koin` 对象。默认情况下，我们使用来自 `GlobalContext` 的 `Koin` 对象。

有关自定义 Koin 实例的更多信息可以查看相应章节。

### 开启 Koin

启动 Koin 意味着在 `GlobalContext` 中运行一个 `KoinApplication` 实例对象。

要在开启 Koin 容器时加载相关模块，我们可以像这样使用 `startKoin` 函数:

```kotlin
// 在 GlobalContext 中运行一个 KoinApplication 实例。
startKoin {
    // 声明使用的日志
    logger()
    // 声明使用的模块
    modules(coffeeAppModule)
}
```

### Module DSL

一个 Koin 模块将收集您为应用程序注入或合并的声明。要创建一个新模块，只需使用以下函数:

- `module { // module content }` - 创建一个 Koin 模块

要描述模块中的内容，可以使用以下函数:

- `factory { //definition }` - 提供一个工厂对象声明
- `single { //definition }` - 提供一个单例对象声明(也作为对象别名)
- `get()` - 解析组件依赖项(也可以使用name、scope 或者 parmeters)
- `bind()` - 为给定的对象声明添加要绑定的类型
- `binds()` - 为给定的对象声明添加要绑定的类型数组
- `scope { // scope group }` - 为 `scoped` 定义一个逻辑组
- `scoped { //definition }` - 提供仅存在于 scope 中的对象声明

注意:`named()`函数允许你给定一个字符串或其他类型限定符。该函数用于命名你的声明。

### 书写一个模块（module)

Koin 模块是用来声明所有组件的区域。使用 `module` 函数声明一个 Koin 模块:

```kotlin
val myModule = module {
   // 在这里声明你自己的依赖项
}
```

在这个 module 函数中，您可以声明相关组件。
