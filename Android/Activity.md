### Activity

***

#### 1.Activity的生命周期

首先给出Activity的完整生命周期，如下图所示：

![Android生命周期图](https://github.com/mrduank/Mr.Duan-Android-Blogs/blob/master/Android/activity生命周期图.png)

##### 生命周期几种常见情况分析：

###### 1.a页面跳转到b页面

|         情况         |                           生命周期                           |
| :------------------: | :----------------------------------------------------------: |
|        启动a         |              a_onCreate 、a_onStart、a_onResume              |
|  从a跳转到不透明的b  |    a_onPause、b_onCreate、b_onStart、b_onResume、a_onStop    |
|   从a跳转到透明的b   |         a_onPause、b_onCreate、b_onStart、b_onResume         |
| 从不透明的b再次回到a | b_onPause、a_onRestart、a_onStart、a_onResume、b_onStop、b_onDestory |
|  从透明的b再次回到a  |         b_onPause、a_onResume、b_onStop、b_onDestory         |
|      按 home 键      |                     a_onPause、a_onStop                      |
|    从后台返回到a     |                    a_onRestart、a_onStart                    |
|     按back退出a      |               a_onPause、a_onStop、a_onDestory               |

**注** ：透明 Activity 和 DialogActivity 类似，在跳转到 DialogActivity 时不会回调前一个 Activity 的 onStop 方法。

###### 2.单页面屏幕旋转生命周期变化

运行Activity：onCreate()->onStart()->onResume()

1）.不设置Activity的android:configChanges时

- 横竖屏切换的生命周期：onPause()->onSaveInstanceState()-> onStop()->onDestroy()->onCreate()->onStart()->onRestoreInstanceState->onResume()
- 结论：切屏会重新调用各个生命周期，切横屏时会执行1次，切竖屏时会执行1次

2）.设置Activity的android:configChanges="orientation"时

* 横竖屏切换的生命周期：onPause()->onSaveInstanceState()-> onStop()->onDestroy()->onCreate()->onStart()->onRestoreInstanceState->onResume()
* 结论：`android:configChanges="orientation"`对于4.4.0以上版本不生效

3）.设置Activity的android:configChanges="orientation|keyboardHidden|screenSize"时

*  横竖屏切换的生命周期 : onConfigChanged()->
* 结论：切屏不会重新调用各个生命周期，只会执行onConfigurationChanged()方法

#### 2.Activity的启动模式

##### 标准模式（standard）:

* 标准模式也是系统的默认模式。每次启动一个 Activity 都会重新创建一个新的实例，不管这个实例是否已经存在。这种模式下，谁启动了这个 Activity，那么这个 Activity 就运行在启动它的那个 Activity 所在的栈中。如 Activity A 启动了 Activity B（B 是标准模式），那么 B 就会进入到 A 所在的栈中。

##### 栈顶复用模式（singleTop）:

* 如果新 Activity 已经位于任务栈的栈顶，那么此 Activity 不会被重新创建，同时它的 onNewIntent 方法会被调用，通过此方法的参数可以取出当前请求的信息。需要注意的是，这个 Activity 的 onCreate、onStart 不会被系统重新调用，因为它并没有发生改变。如果新 Activity 的实例已经存在但不是位于栈顶，那么新 Activity 仍然会重建。
*  应用场景：适合接收通知启动的内容显示页面，当收到多条新闻推送时，用于展示新闻的 Activity 设置成此模式，根据传来的 Intent 数据显示不同的新闻信息，不会启动多个 Activity。

##### 栈内复用模式（singleTask）:

* 这是一种单实例模式，在这种模式下，只要 Activity 在一个栈中存在，那么多次启动此 Activity 都不会重新创建实例，复用时会将它上面的 Activity 全部出栈，同时它的 onNewIntent 方法会被调用。这个过程存在一个任务栈匹配，因为这个模式启动时会在自己需要的任务栈中寻找实例，这个任务栈通过 taskAffinity 属性指定，如果这个任务栈不存在，则会创建这个任务栈。
* taskAffinity 标识了一个 Activity 所需的任务栈的名字，默认情况下，所有 Activity 所需的任务栈的名字为应用的包名。我们可以为每个 Activity 都单独指定 TaskAffinity 属性，这个属性必须不能和包名相同，否则就相当于没有指定。TaskAffinity 属性主要和 singleTask 启动模式或者 allowTaskReparenting 属性配对使用。另外，任务栈分为前台任务栈和后台任务栈，后台任务栈中的 Activity 处于暂停状态，用户可以通过切换将后台任务栈再次调到前台。
* 应用场景：适合作为程序入口点，例如浏览器的主界面，不管从多少个应用启动浏览器，只会启动主界面一次，其余情况都会走 onNewIntent，并且会清空主界面上的其它页面。

##### 单实例模式（singleInstance）:

* 该模式除了具备 singleTask 模式的所有特性外，该模式的 Activity 只能单独的位于一个任务栈中，具有全局唯一性，即整个系统中只有这一个实例，由于栈内复用的特性，后续的请求均不会创建新的Activity实例，除非这个特殊的任务栈被销毁了。以singleInstance模式启动的Activity在整个系统中是单例的，如果在启动这样的Activiyt时，已经存在了一个实例，那么会把它所在的任务调度到前台，重用这个实例。
* 应用场景：呼叫来电界面。这种模式的使用情况比较罕见，在Launcher中可能使用。或者你确定你需要使Activity只有一个实例。请谨慎使用。

##### 如何指定 Activity 的启动模式？

（1）通过 AndroidMenifest 为 Activity 指定启动模式，如下所示：

```
<activity
     android:name=".activity.MainActivity"
     android:launchMode="singleTask"/>
```

（2）通过 Intent 中设置标志位来为 Activity 指定启动模式，如下所示：

```
Intent intent = new Intent();
intent.setClass(MainActivity.this, SecondActivity.class);
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
startActivity(intent);
```

优先级上第二种优先级高于第一种，当两种同时存在时，以第二种方式为准；这两种方式的限定方式不同，第一种无法直接为 Activity 设定 FLAG_ACTIVITY_CLEAR_TOP 标识，第二种无法为 Activity 指定 singleInstance 模式。

##### Activity 常用 Flags

* FLAG_ACTIVITY_NEW_TASK:  为 Activity 指定 singleTask 启动模式，效果和在 XML 中指定该模式相同。
* FLAG_ACTIVITY_SINGLE_TOP:  为 Activity 指定 singleTop 启动模式，效果和在 XML 中指定该模式相同。
* FLAG_ACTIVITY_CLEAR_TOP:  具有此标记的 Activity，当它启动时，在同一个任务栈中所有位于它上面的 Activity 都要出栈。
* FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS :具有这个标记的 Activity  不会出现在历史 Activity 的列表中，在某些情况下我们不希望用户通过历史列表回到我们的 Activity 的时候这个标记比较有用。它等同于在 XML 中指定 Activity 的属性 android:excludeFromRecents="true"。

#### 3.Intent用法和总结

显示Intent:

1）构造方法传入Component，最常用的方式：

```
Intent intent = new Intent(this, SecondActivity.class);  
startActivity(intent);  
```

2）setComponent方法

```
Intent intent = new Intent();    
intent.setClass(this, SecondActivity.class);  
//或者intent.setClassName(this, "com.example.app.SecondActivity");  
//或者intent.setClassName(this.getPackageName(),"com.example.app.SecondActivity");            
startActivity(intent);  
```

隐示Intent :  隐式，不明确指定启动哪个Activity，而是设置Action、Data、Category，让系统来筛选出合适的Activity。筛选是根据所有的`<intent-filter>`来筛选。

例如在`AndroidManifest.xml`文件中，首先被调用的Activity要有一个带有`<intent-filter>`并且包含`<action>`的Activity，设定它能处理的Intent，并且`category`设为`"android.intent.category.DEFAULT"`。action的name是一个字符串，可以自定义，例如这里设成为`"test"`：

```
<activity  
    android:name="com.example.app.SecondActivity">  
    <intent-filter>  
        <action android:name="test"/>  
        <category android:name="android.intent.category.DEFAULT"/>  
    </intent-filter>  
</activity>  
```

然后，在MainActivity，才可以通过这个action name找到上面的Activity。下面两种方式分别通过setAction和构造方法方法设置Action，两种方式效果相同。
 1）setAction方法

```
Intent intent = new Intent();  
intent.setAction("test");  
startActivity(intent);  
```

2）构造方法直接设置Action

```
Intent intent = new Intent("test");  
startActivity(intent);  
```

为了防止应用程序之间互相影响，一般命名方式是**包名+Action名**，例如这里命名`"test"`就很不合理了，就应该改成`"com.example.app.Test"`。