---
title: 关于Android中设置闹钟的相对比较完善的解决方案
date: 2016-05-25 16:37:23
categories: 技术博客
tags: 
- 闹钟
- AlarmManager
---
>前些时候，有人在我「非著名程序员」公众号的后台问我有没有设置闹钟的demo，我当时说承诺为大家写一个，一直没空，直到最近又有人跟我要，我决定抽时间写一个吧。确实设置闹钟是一个比较麻烦的东西。我在这里写的这个demo抽出来了封装了一个类库，大家直接调用其中的设置闹钟和取消闹钟的方法即可。可以设置每天的闹钟，周一到星期天之间多选的闹钟，也可以设置选择一次性闹钟，跟系统设置闹钟的方式基本差不多吧。

### 效果图
来看分析和讲解之前，先看看效果吧，效果图如下：

**设置界面**
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/AlarmManager/ssdsfsdfs.gif)
**闹钟提醒界面**
![](https://raw.githubusercontent.com/loonggg/BlogImages/master/AlarmManager/Screenshot_2016-05-24-18-20-28-249_AlarmManagerCl.png)
<!--more-->
### AlarmManager
对于AlarmManager里的方法我就不逐一介绍了，如果都介绍讲完估计就天黑了。AlarmManager这个类提供对系统闹钟服务的访问接口。
在API 19 以前，AlarmManager的常用方法有三个：
* set(int type，long startTime，PendingIntent pi)；
该方法用于设置一次性闹钟，第一个参数表示闹钟类型，第二个参数表示闹钟执行时间，第三个参数表示闹钟响应动作。
* setRepeating(int type，long startTime，long intervalTime，PendingIntent pi)；
该方法用于设置重复闹钟，第一个参数表示闹钟类型，第二个参数表示闹钟首次执行时间，第三个参数表示闹钟两次执行的间隔时间，第三个参数表示闹钟响应动作。
* setInexactRepeating（int type，long startTime，long intervalTime，PendingIntent pi）；
该方法也用于设置重复闹钟，与第二个方法相似，不过其两个闹钟执行的间隔时间不是固定的而已。

从API 19开始，AlarmManager的机制都是非准确传递，操作系统将会转换闹钟，来最小化唤醒和电池使用。targetSdkVersion在API 19之前应用仍将继续使用以前的行为，所有的闹钟在要求准确传递的情况下都会准确传递。

从API 19以后，则采用了如下方法：
* setWindow(int, long, long, PendingIntent)
* setExact(int, long, PendingIntent)
从上面的两个方法我们可以看出，没有了repeat，就是设置了闹钟只能响一次了，而且这两种方法都可以设置精确的，第一个相对于第二种方法来说，应该是比较省电的。因为setWindow这个方法允许应用程序利用电池优化来自交货批处理即使它适度的及时性要求警报。

### 主要问题
1. API 19以后没有了重复设置，那如果设置一个闹钟每天都准确提醒呢？
2. 手机重启之后，设置的闹钟是否还有效？
3. 应用程序被杀死之后，闹钟是否还有效？

说实话，这些问题我相信大家肯定都遇到过，而且解决起来相当费劲，确实是。来看我们如何一一解决吧。

### 解决遇到的坑
#### API 19以后如何设置重复闹钟
我们知道，我们在使用AlarmManager设置了提醒之后，是通过广播接收的，设置的提醒时间一到，系统发送我们自定义的广播，我们接收到，应用程序提醒。那提醒的时候，我们可以再重新设置一次嘛，这就解决了API 19设置重复闹钟的问题。

```java
PendingIntent sender = PendingIntent.getBroadcast(context, id, intent, PendingIntent
                .FLAG_CANCEL_CURRENT);
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
            am.setWindow(AlarmManager.RTC_WAKEUP, calMethod(week, calendar.getTimeInMillis()),
                    intervalMillis, sender);
        } else {
            if (flag == 0) {
                am.set(AlarmManager.RTC_WAKEUP, calendar.getTimeInMillis(), sender);
            } else {
                am.setRepeating(AlarmManager.RTC_WAKEUP, calMethod(week, calendar.getTimeInMillis
                        ()), intervalMillis, sender);
            }
        }
```
根据判断系统版本，使用不同的设置闹钟的方法，进行设置。接下来我们通过广播接收系统发来的通知，进行闹钟提醒。

```java
public class LoongggAlarmReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        // TODO Auto-generated method stub
        String msg = intent.getStringExtra("msg");
        long intervalMillis = intent.getLongExtra("intervalMillis", 0);
        if (intervalMillis != 0) {
            AlarmManagerUtil.setAlarmTime(context, System.currentTimeMillis() + intervalMillis,
                    intent);
        }
        int flag = intent.getIntExtra("soundOrVibrator", 0);
        Intent clockIntent = new Intent(context, ClockAlarmActivity.class);
        clockIntent.putExtra("msg", msg);
        clockIntent.putExtra("flag", flag);
        clockIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        context.startActivity(clockIntent);
    }
}
```
通过上面的广播，我们可以看到，我是通过那个时间间隔是否为零来判断API 19之后是否是重复闹钟，不为0，就再重新设置一遍。我们来一起看看setAlarmTime()这个方法。如下：

```java
 public static final String ALARM_ACTION = "com.loonggg.alarm.clock";

    public static void setAlarmTime(Context context, long timeInMillis, Intent intent) {
        AlarmManager am = (AlarmManager) context.getSystemService(Context.ALARM_SERVICE);
        PendingIntent sender = PendingIntent.getBroadcast(context, intent.getIntExtra("id", 0),
                intent, PendingIntent.FLAG_CANCEL_CURRENT);
        int interval = (int) intent.getLongExtra("intervalMillis", 0);
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
            am.setWindow(AlarmManager.RTC_WAKEUP, timeInMillis, interval, sender);
        }
    }
```
就这样，重复的问题就解决了。

#### 手机重启之后，闹钟失效怎么解决
对，手机重启之后，闹钟确实是失效了，要想解决这个问题，那就再设置一个监听手机重启的广播，等手机重启的时候，再重新设置一遍，即可解决上面的问题。

看看我在手机重启广播里调用了我封装的重新设置闹钟的方法。这样就解决了手机重启之后，闹钟失效的问题。
**注意：广播需要在清单文件注册，小伙伴们别忘记了哈。我在这里就不贴出代码来了。**

#### 应用程序被杀死，闹钟失效
可以告诉你们的是，这个还真的没有什么好的解决方案，如果你们程序里写了服务，可以在服务重启的时候，判断重新再把闹钟注册一次，或者在打开应用的时候重新注册一次，反正就是能有利于闹钟注册的地方，进行重新设置。如果闹钟设置的id是一样的，后边设置的会自动覆盖先前设置的闹钟。如果谁有比较好的解决应用程序被杀死后，闹钟失效的问题，欢迎大家提供出来分享。

### 这个封装的类库的好处
好处就是我把方法都给你们封装好了，直接就可以调用。
* 直接传入时分的值就可以了。比如：直接传入某个时间点：12:30，然后传入是否是每天提醒，还是周几提醒等
* 闹钟提醒的界面我也已经封装到里面了，还算好看，懒的同学不需要再写了，不满意的同学可以直接下载类库修改。
* 取消闹钟的方法，我也已经进行了封装。

总之，就是非常方便，到底有多方便大家直接看demo就知道了，不满意的同学可以直接下载类库进行修改。

demo和类库地址：https://github.com/loonggg/Android-AlarmManagerClock


