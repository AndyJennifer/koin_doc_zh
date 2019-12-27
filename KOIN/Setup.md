# 为你的项目设置 Koin

## 当前版本

Koin 当前版本如下：

```groovy

//当前稳定版本
koin_version= "2.0.1"

//最新的不稳定版本
koin_version= "2.1.0-alpha-8"
```

## Gradle 依赖

添加以下 Gradle 依赖添加 Koin 到你的项目:

>Koin 包发布在 JCenter

```groovy
// 如果你需要添加Jcenter仓库
repositories {
    jcenter()
}
```

### Core features

```groovy
// Koin for Kotlin
compile "org.koin:koin-core:$koin_version"

// Koin Extended & experimental features
compile "org.koin:koin-core-ext:$koin_version"

// Koin for Unit tests
testCompile "org.koin:koin-test:$koin_version"

// Koin for Java developers
implementation "org.koin:koin-java:$koin_version"
```

### Android

```groovy

// Koin for Android
implementation "org.koin:koin-android:$koin_version"
// Koin Android Scope features
implementation "org.koin:koin-android-scope:$koin_version"
// Koin Android ViewModel features
implementation "org.koin:koin-android-viewmodel:$koin_version"
// Koin Android Experimental features
implementation "org.koin:koin-android-ext:$koin_version"
```

### AndroidX

```groovy
// Koin AndroidX Scope features
implementation "org.koin:koin-androidx-scope:$koin_version"
// Koin AndroidX ViewModel features
implementation "org.koin:koin-androidx-viewmodel:$koin_version"
// Koin AndroidX Fragment features
implementation "org.koin:koin-androidx-fragment:$koin_version"
// Koin AndroidX Experimental features
implementation "org.koin:koin-androidx-ext:$koin_version"
```

### Ktor

```groovy
// Koin AndroidX Scope features
implementation "org.koin:koin-androidx-scope:$koin_version"
// Koin AndroidX ViewModel features
implementation "org.koin:koin-androidx-viewmodel:$koin_version"
// Koin AndroidX Fragment features
implementation "org.koin:koin-androidx-fragment:$koin_version"
// Koin AndroidX Experimental features
implementation "org.koin:koin-androidx-ext:$koin_version"
```
