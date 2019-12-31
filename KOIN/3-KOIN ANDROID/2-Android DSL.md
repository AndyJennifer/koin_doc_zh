---
title: KOIN系列之从 module 中获取 Android context（十五）
tags:
  - koin
categories:
  - koin
---

在 koin DSL 中增加了如下关键字：

`androidContext()` 和 `androidApplication()` 函数允许你在一个 Koin module 中获取 `Context` 对象，并可以帮助你简单地编写需要 `Application` 对象的表达式。

```kotlin
val appModule = module {

    //从Android资源文件中获取 R.string.mystring 资源，并创建 Presenter 实例
    factory {
        MyPresenter(androidContext().resources.getString(R.string.mystring))
    }
}
```
