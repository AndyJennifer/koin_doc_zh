---
title: KOIN系列之 set 方法注入(七）
tags:
  - koin
categories:
  - koin
---

除了使用 `by inject()` 惰性委托表达式来获取所需的依赖,你也可以直接注入属性。

>该功能是实验性的

### 注入一个属性

让我们创建一个需要一些属性注入的类:

```kotlin
class B
class C

class A {
    lateinit var b: B
    lateinit var c: C
}
```

你可以像这样通过 `inject` 扩展来注入属性：

```kotlin

val a : A = A()

// 注入属性
a::b.inject()
a::c.inject()
```

>通过 `inject()` 注入属性，没有使用任何反射 API

对于任何特殊的注入(如 Android 中的 ViewModel…)，需要像 `a.b = getViewModel (…)` 这样手动注入。

### 注入所有的属性（反射API)

另一种方法注入你所有的依赖，在你的实例对象中使用 `inject` :

```kotlin
val a : A = A()

// 注入所有的属性。
a.inject(a::b, a::c)
```
