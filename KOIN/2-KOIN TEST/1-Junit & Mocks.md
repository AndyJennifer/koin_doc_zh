---
title: KOIN系列之使用 Koin 测试 KoinComponent(十三）
tags:
  - koin
categories:
  - koin
---

通过使用 `KoinTest` 标记你的类，你的类变就成了一个`KoinComponent`，并给你带来:

- `by inject()` & `get()` - 从 Koin 中获取实例
- `check` & `dryRun` - 帮助你检查你的配置
- `declareMock` & `declare` - 在当前上下文中声明一个 mock 或一个新定义

```kotlin
class ComponentA
class ComponentB(val a: ComponentA)

class MyTest : KoinTest {

    // Lazy inject property
    val componentB : ComponentB by inject()

    @Test
    fun `should inject my components`() {
        startKoin{
            modules(
                module {
                    single { ComponentA() }
                    single { ComponentB(get()) }
                })
        }

        // directly request an instance
        val componentA = get<ComponentA>()

        assertNotNull(a)
        assertEquals(componentA, componentB.a)
    }

```

>当你需要部分构建程序时，不要犹豫去重载 Koin 中的 module 配置，

### 在容器外进行 Mock

与其每次都 mock 一个新模块，不如使用 `declareMock` 动态声明一个 mock:

```kotlin
class MyTest : KoinTest {

    class ComponentA
    class ComponentB(val a: ComponentA)

    @Test
    fun `should inject my components`() {
        startKoin{
            modules(
                module {
                    single { ComponentA() }
                    single { ComponentB(get()) }
                })
        }
        declareMock<ComponentA>()

        // retrieve mock
        assertNotNull(get<ComponentA>())

        // is built with mocked ComponentA
        assertNotNull(get<ComponentB>())
    }

```

> declareMock 可以特别指明在 module 中的 single or factory 定义。

### 动态声明一个组件

当你需要 mock 多个对象，但是又不想为此创建一个模块（module），那么你可以使用 `declare`

```kotlin
    @Test
    fun `successful declare an expression mock`() {
        startKoin { }

        declare {
            factory { ComponentA("Test Params") }
        }

        Assert.assertNotEquals(get<ComponentA>(), get<ComponentA>())
    }
```

### 检查你的配置

Koin 提供了一种测试 Koin 模块是否良好的方法: `checkModules` - 遍历定义树并检查每个定义是否被绑定

```kotlin
    @Test
    fun `check MVP hierarchy`() {
        startKoin{
            modules(
                module {
                    single { ComponentA() }
                    single { ComponentB(get()) }
                })
        }.checkModules()

        // or

        koinApplication{
            modules(
                module {
                    single { ComponentA() }
                    single { ComponentB(get()) }
                })
        }.checkModules()
    }
```

### 在 Koin 中结束胡开始测试

注意，在每次测试后需停止 Koin 实例(如果你在你的测试项目中使用了 `startKoin` )。请确保在本地 Koin 实例或 `koinApplication` 中调用 `stopKoin()` 来结束当前全局实例。
