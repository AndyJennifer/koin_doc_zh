---
title: KOIN系列之Koin组件（五）
tags:
  - koin
categories:
  - koin
---

### 前言

Koin 是一个用于帮助描述模块和声明的 DSL，也是解析声明的一个容器。我们现在需要的是一个 API 来获取容器外部的实例。这就是 Koin 组件的目的。

### 创建一个 Koin 组件

为了给类赋予使用 Koin 特性的能力，我们需要用 `KoinComponent` 接口标记它。举个例子。

一个声明了 MyService 实例的 module

```kotlin
class MyService

val myModule = module {
    // 声明一个 MyService 单例对象
    single { MyService() }
}
```

在使用定义之前，启动 Koin

装载 myModule 并启动 Koin

```kotlin
fun main(vararg args : String){
    // 开启 Koin
    startKoin {
        modules(myModule)
    }

    // 创建一个 MyComponent 对象 并从 Koin 容器中注入
    MyComponent()
}
```

如何编写 `MyComponent` 并从 Koin 容器中获取实例？我们看下面的例子。

使用 `get()` 和 `by inject()` 注入 MyService 实例。

```kotlin
class MyComponent : KoinComponent {

    // lazy inject Koin instance
    val myService : MyService by inject()

    // or
    // eager inject Koin instance
    val myService : MyService = get()
}
```

### 使用 KoinComponents 解锁 Koin API

一旦你将你的类标记为 `KoinComponent` ，你就有权访问:

- `by inject()` - 从 Koin 容器中获取延迟计算的实例
- `get()` — 从 Koin 容器中立即获取实例
- getProperty()/setProperty() - 获取/设置属性

### 使用 get 和 inject 获取定义

Koin 提供了两种从 Koin 容器中获取实例的方法:

- `val t: t by inject()` - 延迟计算（其值只在首次访问时计算）
- `val t: t = get()`  - 立刻获取实例

```kotlin
// 延迟计算
val myService : MyService by inject()

// 直接获取实例对象
val myService : MyService get()
```

`延迟注入更适合那些需要延迟求值的属性。`

### 通过名称解析实例对象

如果需要，可以使用 `get()` 或 `by inject()`指定以下参数

- `qualifier` — 定义的名称(当在声明中需要指定名称参数时)

在模块中使用声明名称的例子:

```kotlin
val module = module {
    single(named("A")) { ComponentA() }
    single(named("B")) { ComponentB(get()) }
}

class ComponentA
class ComponentB(val componentA: ComponentA)
```

通过名称解析定义：

```kotiln
// 从给定的module中获取
val a = get<ComponentA>(named("A"))
```

### 在你的 API 中没有 inject() 或 get()

如果你正在使用一个API，并且希望在其中使用 Koin ，那么只需使用 `KoinComponent` 接口标记所需的类。
