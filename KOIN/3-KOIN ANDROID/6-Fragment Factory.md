---
title: KOIN系列之Fragment Factory（十九）
tags:
  - koin
categories:
  - koin
---

因为 `AndroidX` 已经发布了 `androidx.Fragment` 包来扩展 `Android Fragment` 的功能

- [https://developer.android.google.cn/jetpack/androidx/releases/fragment](https://developer.android.google.cn/jetpack/androidx/releases/fragment)

### Fragment Factory

从 `1.1.0-*` 版本开始，已经引入了 `FragmentFactory`，一个专门负责 `Fragment` 类实例创建的类:

- [https://developer.android.google.cn/jetpack/androidx/releases/fragment#1.1.0](https://developer.android.google.cn/jetpack/androidx/releases/fragment#1.1.0)

在 Koin 中可以接直接使用 `KoinFragmentFactory` 来帮助您直接注入 `Fragment` 实例。

### 配置 Fragment Factory

首先，在你的 `KoinApplication` 声明中，使用 `fragmentFactory()` 关键字来设置一个默认的 `KoinFragmentFactory` 实例:

```kotlin
 startKoin {
    androidLogger(Level.DEBUG)
    androidContext(this@MainApplication)
    androidFileProperties()
    // setup a KoinFragmentFactory instance
    fragmentFactory()

    modules(...)
}
```

### 声明或注入你的 Fragment

要声明一个 `Fragment` 实例，只需在 `Koin` 模块中将其声明为 `Fragment` 并使用构造函数注入。

给定的一个 `fragment`:

```kotlin
class MyFragment(val myService: MyService) : Fragment(){


}
```

```kotlin
val appModule = module {
    single { MyService() }
    fragment { MyFragment(get()) }
}
```

### 获取你的Fragment

在你的主 Activity 类中，通过使用 `setupKoinFragmentFactory()` 初始化你的 fragment factory:

```kotlin
class MyActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        // Koin Fragment Factory
        setupKoinFragmentFactory()

        super.onCreate(savedInstanceState)
        //...
    }
}
```

接下来，从你的 `suppoortFragmentMnager` 中获取你的 `Fragment`

```kotlin
supportFragmentManager.beginTransaction()
            .replace(R.id.mvvm_frame, MyFragment::class.java, null, null)
            .commit()

```

### Fragment Factory 与 Koin Scopes

如果你想使用 Koin 下 Activity 中的 Scope，你必须在 `scope` 中的 `scoped` 下声明你的 `fragment`

```kotlin
val appModule = module {
    scope<MyActivity> {
        scoped { MyFragment(get()) }
    }
}
```

在你的 scope 中设置你的 Koin Fragment factory:`setupKoinFragmentFactory(currentScope)`

```kotlin
class MyActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        // Koin Fragment Factory
        setupKoinFragmentFactory(currentScope)

        super.onCreate(savedInstanceState)
        //...
    }
}
```
