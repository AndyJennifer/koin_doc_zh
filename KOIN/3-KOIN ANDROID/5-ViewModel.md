---
title: KOIN系列之Android中使用ViewModel（十八）
tags:
  - koin
categories:
  - koin
---


`koin-android-viewmodel` 项目致力于引入 Android Architecture 中的 ViewModel 特性。

### ViewModel DSL

`koin-android-viewmodel` 引入了一个新的 `viewModel` DSL 关键字，作为 `single` 和 `factory` 的补充，来帮助声明一个 ViewModel 组件，并将其绑定到 Android 组件的生命周期中。

```kotlin
val appModule = module {

    //  DetailView 的 ViewModel
    viewModel { DetailViewModel(get(), get()) }

}
```

您声明的组件必须至少继承 `android.arch.lifecycle.ViewModel` 类。你可以对如何注入类的构造函数，如何使用 `get()` 函数注入依赖项，进行详细的说明。

> `viewModel` 关键字可以帮助声明 `ViewModel` 的工厂实例。该实例将由内部的 `ViewModelFactory` 处理，并在需要时重新附加 `ViewModel` 实例。

> `viewModel` 关键字也允许你使用注入参数。

### 注入你的 ViewModel

为了在 Activity, Fragment 或者 Service 中注入 ViewModel，我们可以使用

- `by viewModel()` - 延迟委托属性会把 ViewModel 注入到属性中
- `getViewModel()` - 直接获取 ViewModel 实例

```kotlin
class DetailActivity : AppCompatActivity() {

    // 延迟注入 ViewModel
    val detailViewModel: DetailViewModel by viewModel()
}
```

同理，如果你使用 Java编写代码，你也可以注入 ViewModel 实例，但需要使用 `viewModel()` 或来自 `ViewModelCompat` 的 `getViewModel` 静态函数:

```kotlin
// import viewModel
import static org.koin.android.viewmodel.compat.ViewModelCompat.viewModel;

// import getViewModel
import static org.koin.android.viewmodel.compat.ViewModelCompat.getViewModel;

public class JavaActivity extends AppCompatActivity {

    // lazy ViewModel
    private Lazy<DetailViewModel> viewModelLazy = viewModel(this, DetailViewModel.class);

    // 直接获取 ViewModel 实例
    private DetailViewModel viewModel = getViewModel(this, DetailViewModel.class);

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_simple);

        //...
    }
}
```

### 共享的 ViewModel

一个 ViewModel 实例可以在多个 fragment 及其宿主 Activity 之间共享。

为了在 Fragment 中注入一个可共享的 ViewModel,我们可以使用：

- `by sharedViewModel()` - 延迟属性委托将可共享的 ViewModel 注入到属性中
- `getSharedViewModel()` - 直接获取可共享的 ViewModel 实例

只需声明 ViewModel 一次:

```kotlin
val weatherAppModule = module {

    // 天气视图组件的 ViewModel 声明
    viewModel { WeatherViewModel(get(), get()) }
}
```

Note: a qualifier for a ViewModel will be handled as a ViewModel’s Tag

And reuse it in Activity and Fragments:

注意:一个 ViewModel 的限定符被作为一个 ViewModel 进行处理，并且可以在 Activity 和 Fragment 中重用:

```kotlin
class WeatherActivity : AppCompatActivity() {

    /*
     * 使用 Koin 声明一个 WeatherViewModel 并允许其构造函数的依赖注入
     */
    private val weatherViewModel by viewModel<WeatherViewModel>()
}
```

```kotlin
class WeatherHeaderFragment : Fragment() {

    /*
     * 使用 WeatherActivity 声明共享的 WeatherViewModel
     */
    private val weatherViewModel by sharedViewModel<WeatherViewModel>()
}

class WeatherListFragment : Fragment() {

    /*
     * 使用 WeatherActivity 声明共享的 WeatherViewModel
     */
    private val weatherViewModel by sharedViewModel<WeatherViewModel>()
}
```

> Activity 通过使用 `by viewModel()` 或 `getViewModel()` 注入并共享 ViewModel。在Fragment 中也可以通过 `by sharedViewModel()` 重用共享的 ViewModel 。

如果你是用 Java 编写的 Fragment，那么就必须使用 `sharedViewModel` 或`SharedViewModelCompat` 包中的 `getSharedViewModel`。

```kotlin
// 导入 sharedViewModel 静态函数
import static org.koin.android.viewmodel.compat.SharedViewModelCompat.sharedViewModel;

public class JavaFragment extends Fragment {

    private Lazy<WeatherViewModel> viewModel = sharedViewModel(this, WeatherViewModel.class);

    @Override
    public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        //...
    }
}
```

### ViewModel 与参数的注入

`viewModel` 关键字及其注入 API 是与参数的注入是兼容的

以该 module 为例：

```kotlin
val appModule = module {

    //使用 id 作为参数注入的 DetailViewModel
    viewModel { (id : String) -> DetailViewModel(id, get(), get()) }
}
```

调用注入的代码：

```kotlin
class DetailActivity : AppCompatActivity() {

    val id : String //视图的 id

    //延迟注入带id参数的ViewModel
    val detailViewModel: DetailViewModel by viewModel{ parametersOf(id)}
}
```

### ViewModel and State Bundle

在 `koin-androidx-viewmodel:2.1.0-alpha-3`中，我们提了一种更简洁的方法来处理带有 state bundle 的 ViewModel。

只需传递一个 `Bundle` 对象作为 `ViewModel` 初始化状态的注入参数:

在构造函数中添加一个新的属性类型 `SavedStateHandle`:

```kotlin
class MyStateVM(val handle: SavedStateHandle, val myService : MyService) : ViewModel()
```

在 Koin 模块中，使用参数注入:

```kotlin
viewModel { (handle: SavedStateHandle) -> MyStateVM(handle, get()) }
```

在你的 Activity/Fragment 中,将默认状态作为参数传递

```kotlin
val myStateVM: MyStateVM by viewModel { parametersOf(Bundle(), "vm1") }
```
