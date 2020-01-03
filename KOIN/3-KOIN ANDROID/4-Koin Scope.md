---
title: KOIN系列之在Android中使用scope（十七）
tags:
  - koin
categories:
  - koin
---


`koin-android-scope` 项目致力于将 Android scope 特性引入到现有的 Scope API 中。

### 驯服 Android 声明周期

Android 组件主要由它们的生命周期管理:我们不能直接实例化一个 Activity 或一个 Fragment 。系统已经为我们进行了所有的创建和管理，并会回调方法:onCreate, onStart…

这就是为什么我们不能在 Koin 模块中描述我们的 Activity/Fragment/Service 的原因。我们需要做的是将依赖注入到属性时，遵从其生命周期:当我们不再需要与 UI 部分相关的组件时，我们必须尽快释放他们。

根据组件生命周期的长短，我们可以将组件划分为如下几种类型：

- 长生命周期组件(Service、Data Repository……) - 被多个屏幕使用，从未被删除
- 中等生命周期组件(User Sessions…) - 被多个屏幕使用，必须在一段时间后删除
- 短生命周期组件(Views) - 只有一个屏幕使用且必须在屏幕的结束时被删除

长生命周期组件可以简单地描述为 `single` 声明。而对于中等期和短期生命周期活动组件，我们可以多种声明方式。

在 MVP 架构风格的例子中，`Presenter` 是一个帮助/支持 UI 的短生命周期组件。presenter 必须在每次屏幕显示时创建，并在屏幕消失后删除。

每次创建一个Presenter

```kotlin
class DetailActivity : AppCompatActivity() {

    // 注入 Presenter
    override val presenter : Presenter by inject()
```

我们可以在 module 中这样描述它：

- 使用 `factory` - 当 by inject() 或者 get() 被调用时，每次创建一个新的实例。

```kotlin
val androidModule = module {

    // Presenter 的工厂实例
    factory { Presenter() }
}
```

- 使用 `scope` - 创建一个绑定到 scope 的实例

```kotlin
val androidModule = module {

    scope(named("scope_id")) {
        scoped { Presenter() }
    }
}
```

大多数 Android 内存泄漏都是因为非 Android 组件引用了 UI/Android 组件，系统中始终保留了对UI/Android 组件的引用，导致垃圾回收器不能回收它。

### CurrentScope: 一个绑定到你的生命周期 scope

Koin 提供的 `currentScope` 属性已经绑定到你的 Android 组件生命周期中。在生命周期结束时，它将自动关闭。

为了从 `currentScope` 中获益，你必须为你的 Activity 声明一个 scope (看看我们如何在 Activity 类型中使用 `named()` 限定符):

```kotlin
val androidModule = module {

    scope(named<MyActivity>()) {
        scoped { Presenter() }
    }
}
```

```kotlin
class MyActivity : AppCompatActivity() {

    //在当前范围中注入 Presenter 实例
    val presenter : Presenter by currentScope.inject()
```

### 通过 scope 在不同的组件中共享实例

在扩展的用法中，我们可以跨组件使用 `Scope` 实例。例如，如果我们需要共享一个 `UserSession` 实例。

首先声明一个 scope 定义:

First declare a scope definition:

```kotlin
module {
    // 共享用户会话数据
    scope(named("session")) {
        scoped { UserSession() }
    }
}
```

当需要开始使用 UserSession 实例时，为它创建一个 scope:

```kotlin
val ourSession = getKoin().createScope("ourSession",named("session"))
```

你可以在任何你需要的地方使用它：

```kotlin
class MyActivity1 : AppCompatActivity() {

    val userSession : UserSession by ourSession.inject()
}

class MyActivity2 : AppCompatActivity() {

    val userSession : UserSession by ourSession.inject()
}
```

或者你可以可以使用 Koin 的 DSL 来注入它，比如 一个 presenter 也需要该 UserSession。

```kotlin
class Presenter(val userSession : UserSession)
```

只需要将它注入到构造函数中，并使用正确的 scope id：

```kotlin
module {
    // 共享 UserSession 数据
    scope(named("session")) {
        scoped { UserSession() }
    }

    //从 "session" Scope 中注入  UserSession 实例
    factory { (scopeId : ScopeID) -> Presenter(getScope(scopeId).get())}
}
```

当需要结束掉你的 scope 时，只需要关闭它：

```kotlin
val ourSession = getKoin().getScope("ourSession")
ourSession.close()
```
