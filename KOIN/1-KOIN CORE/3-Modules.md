---
title: KOIN系列之Module是什么（三）
tags:
  - koin
categories:
  - koin
---

通过使用Koin，你可以在模块（module) 中描述相关声明。在本节中，我们将学习如何声明、组织和链接你的模块。

### 模块 是什么

Koin 模块（module) 是用来收集 Koin 声明的 “区域”。使用 `module` 函数描述相关模块

```kotlin
val myModule = module {
    // Your definitions ...
}
```

### 使用多个模块

组件不一定要在同一个模块中。模块只是帮助你组织定义与声明的一块逻辑空间，它也可以依赖于其它模块的定义。这种定义是延迟计算的，只有在组件请求它时才会解析。

让我们举个在不同的模块中链接组件例子:

```kotlin
// ComponentB <- ComponentA
class ComponentA()
class ComponentB(val componentA : ComponentA)

val moduleA = module {
    // Singleton ComponentA
    single { ComponentA() }
}

val moduleB = module {
    // Singleton ComponentB with linked instance ComponentA
    single { ComponentB(get()) }
}

```

`Koin 中最重要的概念就是：Koin 中的声明是延迟的。一个 Koin 声明是随着 Koin 容器启动的，但是该声明并没有实例化。只有完成了对其类型的请求时，才会被创建`

当我们启动 Koin 容器时，我们只需要声明需要使用的模块列表:

```kotlin
// Start Koin with moduleA & moduleB
startKoin{
    modules(moduleA,moduleB)
}
```

Koin 将解析所有给定模块的依赖关系。

### 链接 module 的策略

由于模块之间的声明是延迟的，我们可以使用模块实现不同的策略实现:每个模块中声明一个组件的实现。

让我们以 Repository 和 Datasource 为例。Repository 需要 Datasource，Datasource 可以通过两种方式实现:本地或远程。

```kotlin
class Repository(val datasource : Datasource)
interface Datasource
class LocalDatasource() : Datasource
class RemoteDatasource() : Datasource
```

我们可以在3个模块中分别声明这些组件: Repository 与每个 DataSource 中各实现一个:

```kotlin
class Repository(val datasource : Datasource)
interface Datasource
class LocalDatasource() : Datasource
class RemoteDatasource() : Datasource
```

然后我们只需要将正确的模块组合，并启动 Koin。

```kotlin
// 加载 Repository + Local Datasource definitions
startKoin {
    modules(repositoryModule,localDatasourceModule)
}

// 加载 Repository + Remote Datasource definitions
startKoin {
    modules(repositoryModule,remoteDatasourceModule)
}
```

### 覆盖声明或者 module

Koin 不允许你重复声明已经存在的声明(类型、名称、路径……)。如果你尝试这样做，那么就会得到一个错误：

```kotlin
val myModuleA = module {

    single<Service> { ServiceImp() }
}

val myModuleB = module {

    single<Service> { TestServiceImp() }
}

// 将会抛出一个 BeanOverrideException 异常
startKoin {
    modules(myModuleA,myModuleB)
}
```

为了覆盖声明，你可以使用 `override` 参数：

```kotlin
val myModuleA = module {

    single<Service> { ServiceImp() }
}

val myModuleB = module {

    //覆盖声明
    single<Service>(override#true) { TestServiceImp() }
}
```

```kotlin
val myModuleA = module {

    single<Service> { ServiceImp() }
}

// 允许覆盖模块中的所有声明
val myModuleB = module(override#true) {

    single<Service> { TestServiceImp() }
}
```

`在列出模块和覆盖声明时，顺序非常重要。你必须在模块列表的最后一个module中包含相关覆盖声明。`
