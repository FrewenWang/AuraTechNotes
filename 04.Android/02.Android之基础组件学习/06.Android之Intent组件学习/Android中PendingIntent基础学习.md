  在Android中，我们常常使用PendingIntent来表达一种“留待日后处理”的意思。从这个角度来说，PendingIntent可以被理解为一种特殊的异步处理机制。不过，单就命名而言，PendingIntent其实具有一定误导性，因为它既不继承于Intent，也不包含Intent，它的核心可以粗略地汇总成四个字——“异步激发”。
  
很明显，这种异步激发常常是要跨进程执行的。比如说A进程作为发起端，它可以从系统“获取”一个PendingIntent，然后A进程可以将PendingIntent对象通过binder机制“传递”给B进程，再由B进程在未来某个合适时机，“回调”PendingIntent对象的send()动作，完成激发。

PendingIntent 是 Android 提供的一种用于外部程序调起自身程序的能力，生命周期不与主程序相关。外部程序通过 PendingIntent 只能调用起三种组件：Activity、Service、Broadcast。
       
PendingIntent 的使用场景有三个：使用 AlarmManager 设定闹钟、在系统状态栏显示 Notification、在桌面显示 Widget。

PendingIntent 也只能通过下列的静态方法获取：


```
// 获取 Broadcast 关联的 PendingIntent
PendingIntent.getBroadcast(Context context, int requestCode, Intent intent, int flags)

// 获取 Activity 关联的 PendingIntent
PendingIntent.getActivity(Context context, int requestCode, Intent intent, int flags)
PendingIntent.getActivity(Context context, int requestCode, Intent intent, int flags, Bundle options)

// 获取 Service 关联的 PendingIntent
PendingIntent.getService(Context context, int requestCode, Intent intent, int flags)
```

参数说明：
- context：上下文对象。
- requstCode：请求码，发件人的私人请求代码（当前未使用）。
- intent：请求意图。用于要指明要启动的类以及数据的传递；
- flags：这是一个关键的标志位：

我们简单解释一下。上面的getActivity()的意思其实是，获取一个PendingIntent对象，而且该对象日后激发时所做的事情是启动一个新activity。也就是说，当它异步激发时，会执行类似Context.startActivity()那样的动作。相应地，getBroadcast()和getService()所获取的PendingIntent对象在激发时，会分别执行类似Context.sendBroadcast()和Context.startService()这样的动作。

PendingIntent 是系统对于待处理数据的一个引用，称之为：token；当主程序被 Killed 时，token 还是会继续存在的，可以继续供其他进程使用。如果要取消 PendingIntent，需要调用PendingIntent 的 cancel 方法。

对于 PendingIntent 容易误解的一点是：

如果创建了很多 PendingIntent，只要 extra 中的数据不同的话，以为就是两个不同的 PendingIntent 这种理解是错误的！！Extras不参与Intent的匹配过程。

正确区分不同 PendingIntent 有两种方法：
1. PendingIntent.getXXX(…) 方法中的 requestCode 不同
1. 通过 Intent.filterEquals 测试时不相等

关于 PendingIntent.getXXX(…) 方法中第四个参数 flags，在 PendingIntent 定义了四个比较常用的 FLAG：

```
//如果当前系统中已经存在一个相同的PendingIntent对象，那么就将先将已有的PendingIntent取消，然后重新生成一个PendingIntent对象。
int FLAG_CANCEL_CURRENT

//如果当前系统中不存在相同的PendingIntent对象，系统将不会创建该PendingIntent对象而是直接返回null。
int FLAG_NO_CREATE

//表示 PendingIntent 只能使用一次，如果已使用过，那么 getXXX(...) 将会返回 NULL 
//也就是说同类的通知只能使用一次，后续的通知单击后无法打开。
int FLAG_ONE_SHOT

//如果系统中有一个和你描述的PendingIntent对等的PendingInent，那么系统将使用该PendingIntent对象，但是会使用新的Intent来更新之前PendingIntent中的Intent对象数据，例如更新Intent中的Extras。
int FLAG_UPDATE_CURRENT
```

注意：两个PendingIntent对等是指它们的operation一样, 且其它们的Intent的action, data, categories, components和flags都一样。但是它们的Intent的Extra可以不一样。