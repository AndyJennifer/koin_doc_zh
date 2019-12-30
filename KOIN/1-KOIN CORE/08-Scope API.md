---
title: KOIN系列之 Scope API(八）
tags:
  - koin
categories:
  - koin
---

Koin 提供了一个简单的 API ，来帮助你定义那些具有生命周期的实例。

### Scope 是什么

Scope 是对象存在的固定时间或方法调用。另一种理解是将 scope 看作是对象状态持续的时间。当 scope 上下文结束时，在该 scope 下绑定的任何对象都不能被再次注入(它们将会从容器中移除)。

### Scope 声明

在 Koin 中，我们默认有 3 种 scope:

- `single` 声明，创建一个对象，该对象在整个容器生命周期中都持续存在(不能被删除)。
- `factory` 声明，每次创建一个新对象。短生命周期。不会在容器中持续存在(不能共享)。
- `scoped` 声明，创建一个持续并绑定到 scope 声明周期下的对象。

要声明一个 scoped ，可以使用如下所示的 `scoped` 函数。scope 将收集那些 scoped 声明，并同时作为其逻辑时间单位:

```kotlin
module {
    scope(named("A Scope Name")){
        scoped { Presenter() }
        // ...
    }
}
```

> scope 需要限定符(qualifier)来帮助命名。它可以是一个字符串限定符(String Qualifier)，也可以是一个类型限定符(TypeQualifier)

声明给定类型的 scope ，如下所示

```kotlin
module {
    scope(named<MyType>()){
        scoped { Presenter() }
        // ...
    }
}
```

或者可以像这样直接声明:

```kotlin
module {
    scope<MyType>{
        scoped { Presenter() }
        // ...
    }
}
```

### 使用一个 Scope

我们拥有如下类：

```kotlin
class A
class B
class C
```

#### 声明一个 Scoped 对象

让我们通过 A scope 包裹 B 和 C 对象，B 与 C对象都绑定到 A 对象下:

```kotlin
module {
    single { A() }
    scope<A> {
        scoped { B() }
        scoped { C() }
    }
}
```

#### 创建一个 scope 并获取依赖

下面是创建一个 scope 并获取依赖的例子：

```kotlin
// Get A from Koin's main scope
val a : A = koin.get<A>()

// Get/Create Scope for `a`
val scopeForA = a.getOrCreateScope()

// Get scoped instances from `a`
val b = scopeForA.get<B>()
val c = scopeForA.get<C>()
```

我们使用 `getOrCreateScope` 函数来创建由类型定义的 scope。

>注意 `scopeForA` 已绑定到 `a` 对象上

#### 使用 `scope` 属性

```kotlin
// Get A from Koin's main scope
val a : A = koin.get<A>()

// Get scoped instances from `a`
val b = a.scope.get<B>()
val c = a.scope.get<C>()
```

>注意，当你想为一个特殊的组件使用 `scope` 时 ，千万不要创建一个与 Android 生命周期绑定的 scope--例如`lifecycleScope` 。

### 销毁 scope 及其链接的对象

```kotlin
// Destroy `a` scope & drop `b` & `c`
a.closeScope()
```

我们使用 `closeScope` 函数来删除当前 scope 及关联的 scoped 下的对象。

### 更多关于 Scope 的 API

#### 使用 scope

scope 对象可以按照如下方式创建:
`val scope = koin.createScope(id,qualifier)` 。`id` 是 scope 的id 同时也是其标识符。

要解析 scope 中的依赖，我们可以这样做：

- `val presenter = scope.get<Presenter>()` - 直接使用 scope 对象中的 get/inject 函数

#### 创建与获取一个 scope

在 Koin 实例中，你可以访问：

- `createScope(id : ScopeID, scopeName : Qualifier)` - 使用给定的 id 与 scopeName 创建一个封闭的 scope 对象。
- `getScope(id : ScopeID)` - 通过给定的 id 获取已经创建的 scope
- `getOrCreateScope(id : ScopeID, scopeName : Qualifier)` - 创建或获取已经创建的 scope ，该封闭的 scope 对象需要指定的 id 和 scopeName

>与一个 scope 只有一个 id 的区别是，id 只会帮助你在你所有声明的 scope 找到指定的 scope, 而 scopeName 则是对绑定的 scope 组名称的引用。

#### 在 scope 中解析依赖

scope 最有趣的一点就是，为其下的scoped 声明,定义了一个通用的逻辑时间单位。它还允许在给定的 scope 内中解析这些声明

```kotlin
// given the classes
class ComponentA
class ComponentB(val a : ComponentA)

// module with scope
module {

    scope(named("A_SCOPE_NAME")){
        scoped { ComponentA() }
        // will resolve from current scope instance
        scoped { ComponentB(get()) }
    }
}
```

依赖关系的解析是直接了当的:

```kotlin
// create an closed scope instance "myScope1" for scope "A_SCOPE_NAME"
val myScope1 = koin.createScope("myScope1",named("A_SCOPE_NAME"))

// from the same scope
val componentA = myScope1.get<ComponentA>()
val componentB = myScope1.get<ComponentB>()
```

>默认情况下，如果在当前 scope 内没有找到相关声明，则会退回到主 scope 内解析

#### 关闭 scope

一旦你的 scope 对象已经结束，只需要调用 `close` 函数来关闭它。

```kotlin
// from a KoinComponent
val session = getKoin().createScope("session")

// use it ...

// close it
session.close()
```

注意，您不能再从已经关闭的 scope 中注入实例。
