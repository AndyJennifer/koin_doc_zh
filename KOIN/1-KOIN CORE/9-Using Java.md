---
title: KOIN系列之在Java中使用Koin(九）
tags:
  - koin
categories:
  - koin
---

Koin module 必须通过 Kotlin 来编写，可以使用 `@JvmField` 来增加在 Java 中使用的友好性：

```kotlin
@JvmField
val koinModule = module {

    single { ComponentA() }
}
```

现在只需在一个 Java 类中调用 `KoinJavaStarter.startKoin` 函数来启动你的模块:

```kotlin
KoinJavaStarter.startKoin(singletonList(koinModule));
```

### 注入到 Java 类中

`koin-java` 项目中提供了一个静态 Java helper: `KoinJavaComponent` 来帮助你:

- 使用 `get(Class)` 直接注入对象

```kotlin
ComponentA a = get(ComponentA.class);
```

- 使用 `inject(Class)` 延迟注入对象

```kotlin
Lazy<ComponentA> lazy_a = inject(ComponentA.class);
```

>确保 `KoinJavaComponent` 类的静态导入。
