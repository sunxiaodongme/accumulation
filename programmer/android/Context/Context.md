# Context

## 各种类型Context的能力

|                     | **Application** | **Activity** | **Service** | **ContentProvider** | **BroadcastReceiver** |
| ------------------- | --------------- | ------------ | ----------- | ------------------- | --------------------- |
| 显示Dialog            | NO              | YES          | NO          | NO                  | NO                    |
| 启动Activity          | NO1             | YES          | NO1         | NO1                 | NO1                   |
| Layout Inflation    | NO2             | YES          | NO2         | NO2                 | NO2                   |
| 启动Service           | YES             | YES          | YES         | YES                 | YES                   |
| 绑定到Service          | YES             | YES          | YES         | YES                 | NO                    |
| 发送Broadcast         | YES             | YES          | YES         | YES                 | YES                   |
| 注册BroadcastReceiver | YES             | YES          | YES         | YES                 | NO3                   |
| 加载Resource          | YES             | YES          | YES         | YES                 | YES                   |

注：

1. NO1 表示Application context的确可以开始一个Activity，但是它需要创建一个新的task。这可能会满足一些特定的需求，但是在你的应用中会创建一个不标准的回退栈（back stack），这通常是不推荐的或者不是是好的实践。
2. NO2 表示这是非法的，但是这个填充（inflation）的确可以完成，但是是使用所运行的系统默认的主题（theme），而不是你app定义的主题。
3. NO3 在Android4.2以上，如果Receiver是null的话（这是用来获取一个sticky broadcast的当前 值的），这是允许的。

Application context有很多功能并不是合适去做，而这些功能都与UI相关。实际上，只有Activity能够处理所有与UI相关的任务。其他类别的context实例功能都差不多。

尝试显示一个使用Aplication context创建的Dialog，或者使用Application context开始一个Activity，系统会抛出一个异常。

一个并不明显的问题是填充布局（inflating layout）。使用正确的context关系到其中的一个行为。当你使用Application context来inflate一个布局的时候，框架并不会报错，并返回一个使用系统默认的**主题**创建一个完美的view给你，而没有考虑你的applicaiton自定义的theme和style。**这是因为Acitivity是唯一的绑定了在manifast文件种定义主题的Context**。其他的Context实例将会使用系统默认的主题来inflater你的view。导致显示的结果并不是你所希望的。