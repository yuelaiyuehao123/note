# APP 性能指标

## 1、相应时间

| 指标项 | 标准（ms） |
| ------ | ---------- |
| 冷启动 | <= 1000    |
| 热启动 | <= 700     |

测试方法：

冷启动：第一次打开应用，或者杀死应用。 起始点：手指点击应用图标抬起第一帧    结束点：页面框架加载出来第一帧

热启动：应用启动后放置后台，再次打开该应用。起始点：手指点击应用图标抬起第一帧    结束点：页面框架加载出来第一帧



## 2、流畅度

### 2.1、上屏帧率

| 指标项                        | 标准            |
| ----------------------------- | --------------- |
| 前台运行页面的上屏帧率（FPS） | >=APP满帧 * 90% |

测试方法：

确保被测场景在前台运行

- 命令 dumpsys SurfaceFlinger  获得当前屏幕的监测项

- adb shell getfps -w "待测页面activity"     

### 2.2、延迟率、丢帧率

| 指标项                    | 标准  |
| ------------------------- | ----- |
| 滑动屏幕的延迟率（janky） | < 30% |
| 屏幕滑动的丢帧率（miss）  | < 5%  |

丢帧率（miss）：滑动过程中丢帧的次数 /（总绘制的帧数+丢帧数）

延迟率（janky):  每帧的处理时间超过16.3ms的次数 / 总绘制帧数

测试方法：

命令 dumpsys gfxinfo package reset  

在开始抓janky/miss时 先输入一遍dumpsys gfxinfo package reset ，目的是清除上次的结果。待用例操作完毕后，再输入dumpsys gfxinfo package reset抓取数据

## 3、资源

CPU

CPU 总体使用率

应用程序CPU占有率

我们可以通过下列命令**查看CPU资源使用情况：**

adb shell dumpsys cpuinfo | grep packagename

adb shell dumpsys top -n 10 -s cpu 显示占比CPU最高的钱10个程序

(-t 显示进程名称，-s按指定行排序,-n 在退出前刷新几次，-d 刷新间隔，-m显示最大数量)

CPU Usage: 传统CPU利用率，也称为未规范化CPU利用率

计算方法：当前时刻CPU频率下，CPU Usage = CPU执行时间/CPU总时间

CPU Usage（normalized）:规范化CPU利用率

计算方法：CPU usage（normalized）= (CPU执行时间/CPU总时间)*（当下时刻所有CPU频率之后/所有CPU频率最大值之后）