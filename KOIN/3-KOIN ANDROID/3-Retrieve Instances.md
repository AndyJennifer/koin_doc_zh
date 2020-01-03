---
title: KOIN系列之在Android中获取对象（十六）
tags:
  - koin
categories:
  - koin
---

一旦你声明了一些模块并且你已经启动了 Koin ，那如何在你的 Android 下的 Activity、Fragment 或 Service 中获取你的实例?

### 将 Activity Fragment 及 Service 转换为 KoinComponent

Activity Fragment 及 Service 在 KoinComponent 扩展的基础上进行了延伸。我们有权使用：

- `by inject()` - 从 Koin 容器中获取延迟计算的实例
- `get()` - 从 Koin 容器中立即获取实例
- `release()` - 从它的路径中释放模块的实例
- `getProperty()` / `setProperty()` - 获取/设置属性

一个模块声明了一个 'presenter '组件:

```kotlin
val androidModule = module {
    // 一个 Presenter 工厂对象
    factory { Presenter() }
}
```

我们可以声明一个延迟属性注入:

```kotlin
class DetailActivity : AppCompatActivity() {

    //延迟注入一个 Presenter 实例对象
    override val presenter : Presenter by inject()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // ...
    }
```

或者我们可以直接获取一个实例对象：

```kotlin
class DetailActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // 获取Presenter对象
        val presenter : Presenter = get()
    }
```

### 在你的 API 中没有 inject() 或 get()

如果你希望在其他类中通过 `injcet()` 或 `get()` 方法获取实例对象，那么只需使用 `KoinComponent` 接口标记所需的类。
