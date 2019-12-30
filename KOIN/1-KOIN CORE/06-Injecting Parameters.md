---
title: KOIN系列之声明一个注入参数（六）
tags:
  - koin
categories:
  - koin
---

在任意声明中，你可以注入参数:

### 声明一个注入参数

下面是一个注入参数的例子。我们需要一个 `View` 参数来构造 `Presenter` 类:

```kotlin
class Presenter(val view : View)

val myModule = module {
    single{ (view : View) -> Presenter(view) }
}
```

### 传值注入

与解析依赖(使用 `get()` 解析)相反，注入参数是通过解析 API 传递的参数。这意味着这些参数是通过 `get()` 或 `by inject()` 使用 `parametersOf()` 函数传递的值:

```kotlin
class MyComponent : View, KoinComponent {

    // inject this as View value
    val presenter : Presenter by inject { parametersOf(this) }
}
```

### 多参数

如果我们想要声明多个参数，我们可以使用构造函数来列出我们的参数:

```kotlin
class Presenter(val view : View, id : String)

val myModule = module {
    single{ (view : View, id : String) -> Presenter(view,id) }
}
```

在 `KoinComponent` 中，只需将 `parametersOf` 函数和你的参数一起使用，如下所示:

```kotlin
class MyComponent : View, KoinComponent {

    val id : String ...

    // 注入 view 与 id
    val presenter : Presenter by inject { parametersOf(this,id) }
}
```
