文章参考：https://developer.android.com/training/dependency-injection?hl=zh-cn



### 概述

我们学习一个框架，最好的学习方法就是学习官网教程。上面就是Android的依赖注入框架的Hilt的学习的官网地址

#### 什么是依赖注入

依赖项注入 (DI) 是一种广泛用于编程的技术，非常适用于 Android 开发。遵循 DI 的原则可以为良好的应用架构奠定基础。其实，说白了。就是一个类里面有一个变量，这个变量就是这个类的依赖。然后通过外部注入对这个变量进行赋值，这种就叫做依赖注入。

官网中详细讲解了依赖注入的含义。大家具体可以参见：https://developer.android.com/training/dependency-injection?hl=zh-cn

#### 依赖注入的优点

实现依赖项注入可为您带来以下优势：

- 重用代码

- 易于重构

- 易于测试

说白了，依赖注入可以更好的帮你减少创建依赖对象的模板代码。更大范围的让这些模板代码通过注解的形式完成。

### 什么是Hilt

[Hilt](https://developer.android.com/training/dependency-injection/hilt-android?hl=zh-cn) 是推荐用于在 Android 中实现依赖项注入的 Jetpack 库。Hilt 通过为项目中的每个 Android 类提供容器并自动为您管理其生命周期，定义了一种在应用中执行 DI 的标准方法。

Hilt 在热门 DI 库 [Dagger](https://developer.android.com/training/dependency-injection/dagger-basics?hl=zh-cn) 的基础上构建而成，因而能够受益于 Dagger 提供的编译时正确性、运行时性能、可伸缩性和 Android Studio 支持。Hilt 是 Android 的依赖注入库，其实是基于 Dagger 。但是Dagger主要是基于Java的，而Hilt是应用于Android，可以说 Hilt 是专门为 Andorid 打造的。

至于Dagger2是什么？这篇文章我们暂时先不讲解。



### Hilt的依赖添加

添加依赖项。首先，将 `hilt-android-gradle-plugin` 插件添加到项目的根级 `build.gradle` 文件中：

```
buildscript {
    ...
    dependencies {
        ...
        classpath 'com.google.dagger:hilt-android-gradle-plugin:2.28-alpha'
    }
}
```

然后，应用 Gradle 插件并在 app/build.gradle 文件中添加以下依赖项：

```groovy
...
apply plugin: 'kotlin-kapt'
apply plugin: 'dagger.hilt.android.plugin'

android {
    ...
}

dependencies {
    implementation "com.google.dagger:hilt-android:2.28-alpha"
    kapt "com.google.dagger:hilt-android-compiler:2.28-alpha"
}
```

Hilt 使用 [Java 8 功能](https://developer.android.com/studio/write/java8-support?hl=zh-cn)。如需在项目中启用 Java 8，请将以下代码添加到 `app/build.gradle` 文件中：

```groovy
android {
  ...
  compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
  }
}
```

就是这么简单。

文章参考：https://developer.android.com/training/dependency-injection/hilt-android?hl=zh-cn#java

文章参考：https://juejin.im/post/6845166891325997069



### Hilt的概念讲解

#### @HiltAndroidApp

@HiltAndroidApp将会触发Hilt的代码生成，作为程序依赖项容器的基类，生成的 Hilt 依附于 Application 的生命周期，他是 App 的父组件，提供访问其他组件的依赖。在 Application 中配置好后，就可以使用 Hilt 提供的组件了；组件包含 Application，Activity，Fragment，View，Service 等。

创建一个依赖容器，该容器遵循 Android 的生命周期类，目前支持的类型是: Activity, Fragment, View, Service, BroadcastReceiver.

#### @Inject

使用@Inject来告诉Hilt如何提供该类的实例,常用于构造方法，非私有字段，方法中。Hilt 有关如何提供不同类型的实例信息也称之为绑定。如果你用过Dagger2应该是对这个注解并不陌生。

#### @Module

Module是用来提供一些无法用构造@Inject 的依赖，如第三方库，接口，build 模式的构造等。使用 @Module 注解的类，需要使用@InstallIn注解指定module的范围。增加了@Module 注解的类，其实代表的就是一个模块，并通过指定的组件来告诉在那个容器中可以使用绑定安装。

#### @InstallIn

使用 @Module 注入的类，需要使用 @InstallIn 注解指定 module 的范围。

例如使用 @InstallIn(ActivityComponent::class) 注解的 module 会绑定到 activity 的生命周期上。

#### @Provides

常用于被 @Module 注解标记类的内部方法上。并提供依赖项对象。

#### @EntryPoint


Hilt支持最常见的 Android 类 Application、Activity、Fragment、View、Service、BroadcastReceiver 等等，但是您可能需要在Hilt 不支持的类中执行依赖注入，在这种情况下可以使用 @EntryPoint 注解进行创建，Hilt 会提供相应的依赖。


### Hilt 中的组件(Compenent)

使用 @Module 注解的类，需要使用 @Installin 注解来指定 module 的范围。例如 @InstallIn(ApplicationComponent::class) 注解的 Module 就会绑定到 Application 的生命周期上。



### Hilt的示例

#### Hilt 应用类

所有使用 Hilt 的应用都必须包含一个带有 `@HiltAndroidApp` 注释的 `Application` 类。`@HiltAndroidApp` 会触发 Hilt 的代码生成操作，生成的代码包括应用的一个基类，该基类充当应用级依赖项容器。

```
@HiltAndroidApp
class NyxSunflowerApp : Application() {
   
    override fun onCreate() {
        super.onCreate()
      
    }
}
```

生成的这一 Hilt 组件会附加到 `Application` 对象的生命周期，并为其提供依赖项。此外，它也是应用的父组件，这意味着，其他组件可以访问它提供的依赖项。



#### 将依赖项注入 Android 类

在 `Application` 类中设置了 Hilt 且有了应用级组件后，Hilt 可以为带有 `@AndroidEntryPoint` 注释的其他 Android 类提供依赖项：

```
@AndroidEntryPoint
class GardenActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView<ActivityGardenBinding>(this, R.layout.activity_garden)
    }
}
```

Hilt 目前支持以下 Android 类：

- `Application`（通过使用 `@HiltAndroidApp`）

- `Activity`

- `Fragment`

- `View`

- `Service`

- `BroadcastReceiver`

如果您使用 `@AndroidEntryPoint` 为某个 Android 类添加注释，则还必须为依赖于该类的 Android 类添加注释。例如，如果您为某个 Fragment 添加注释，则还必须为使用该 Fragment 的所有 Activity 添加注释。

在 Hilt 对 Android 类的支持方面还要注意以下几点：

- Hilt 仅支持扩展 [`ComponentActivity`](https://developer.android.com/reference/kotlin/androidx/activity/ComponentActivity?hl=zh-cn) 的 Activity，如 [`AppCompatActivity`](https://developer.android.com/reference/kotlin/androidx/appcompat/app/AppCompatActivity?hl=zh-cn)。
- Hilt 仅支持扩展 `androidx.Fragment` 的 Fragment。
- Hilt 不支持保留的 Fragment。









