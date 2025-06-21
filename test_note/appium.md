## 一、安装

[appium](https://www.kancloud.cn/testerhome/appium_docs_cn)

pycharm

`pip install Appium-Python-Client==2.11.1`

## 二、ADB

adb命令原理：

* 电脑appium客户端会创建一个服务器（ADB服务器）
* 手机连接上电脑之后，会自动检测服务进行连接
* 需要操作手机，使用appium接受脚本代码，然后转化为adb命令，最后通过命令执行手机对应的操作
* 脚本代码（python）——>将脚本代码编译成adb命令，发送给服务器(appium) ——>真机/模拟机

**定义：**

* ADB是**安卓调试桥**`(Android-Debug-Bridge)`

**作用：**

* 通过编译脚本代码生成`adb`命令操作控制手机

### **常用命令：**

1. 开启服务器：`adb start-server` ==>  进启动ADB服务后台进程`(Android-Debug-Bridge)`，此时，ADB服务处于运行状态，**不会主动扫描或者连接设备**
2. 关闭服务器：`adb kill-server`
3. 查看连接设备：`adb devices`
4. 进入手机终端：`adb shell`
5. 获取手机版本号：`adb shell getprop ro.build.version.release`
6. 获取手机当前启动的app和界面：`adb shell dumpsys window windows | findstr mFocusedApp`
   * **注：**对于较新版本（10.0以上）通过`adb shell dumpsys window | findstr mCurrentFocus`
     * eg：` mCurrentFocus=Window{f4c93cb u0 com.oplus.wirelesssettings/com.android.settings.SettingsActivity}`
     * `mCurrentFocus=null
       mCurrentFocus=Window{10ef860 u0 com.ziqing.medical/com.ziqing.medical.activitys.SearchDevicesActivity type=1}`
       * 包名(package name)：`com.ziqing.medical `   应用程序的唯一标识符
       * 活动名(activity name)：`com.ziqing.medical.activitys.SearchDevicesActivity`
       * 窗口表示：`10ef860`
       * 用户id：`u0 `    设备主用户（系统用户标识）
       * 窗口类型：`type=1`   应用程序窗口（基础类型）

     * app名字：`com.android.settings`
     * app界面名字：`.Settings`
7. 安装`apk`文件到设备

* ```cmd
  # 1. 开启手机USB调试（开发者选项中）
  # 2. 连接USB并选择"文件传输"模式
  # 3. 电脑端执行：
  adb install -r path/to/your/app.apk # -r 表示覆盖安装
  ```

  * `adb install -r E:\apkPackage\app-release-ziqing-1.2.3.apk`

  * 结果

    ```cmd
    Performing Streamed Install
    Success
    ```

  * 安装失败，先把手机上的原app卸载掉

    `adb: failed to install E:\apkPackage\app-release-ziqing-1.2.3.apk: Failure [INSTALL_FAILED_UPDATE_INCOMPATIBLE: Existing package com.ziqing.medical signatures do not match newer version; ignoring!]`

    手机上是1.2.2版本

8. 卸载指定包名

   * `adb uninstall <package>`

9. 获取设备截图

   * `adb shell screencap <file>`
   * ①截图后保存在设备上：`adb shell screencap /sdcard/shootpic.png`
   * ②将图片拉取到电脑上：`adb pull /sdcard/screenshot.png C:\Users\Administrator\Desktop\shootpic`
   * 注：`/sdcard` 为内部存储

10. 将设备文件复制到电脑

   * `adb pull <from> <to>`

11. 将电脑文件推送到设备

    * `adb push <from> <to>`
    * `adb push C:\Users\Administrator\Desktop\shootpic\pic1.png /sdcard/`

12. 通过管道获取设备上的截图

    * `adb exec-out screencap -p > <PC>`
    * `adb exec-out screencap -p > "C:\Users\Administrator\Desktop\screenshot.png"`

13. 删除设备上的文件

    * `adb shell rm <file>`

    * `eg: adb shell rm /sdcard/screenshot.png`

14. 创建文件夹

    * 创建单个文件夹：`adb shell mkdir <file>`
    * 递归创建多级文件夹：`adb shell mkdir -p <file1/file2>`

15. 查看文件

    * 查看直接子目录：`adb shell ls <file>`
    * 递归查看目录下所有内容：`adb shell ls -R <file>`
    * 在`path`中查找`file2`：`adb shell find <path> -type d -name 'file2_name'`
      * `-type d`：只查找**目录（directory）**类型的文件系统项
    * 在`path`中查找直接子目录：`adb shell ls <path> | findstr <file_name>`
      * 查找的是 `/path/` 目录下的直接子项（文件和目录名）中包含 "file_name" 的项

    ```cmd
    C:\Users\Administrator>adb shell ls /sdcard/Pictures
    Pic
    ScreenShot
    
    C:\Users\Administrator>adb shell ls -R /sdcard/Pictures
    /sdcard/Pictures:
    Pic
    ScreenShot
    
    /sdcard/Pictures/Pic:
    
    /sdcard/Pictures/ScreenShot:
    pic1.png
    
    C:\Users\Administrator>adb shell find /sdcard/Pictures -type d -name 'Pic1'
    /sdcard/Pictures/Pic/Pic1
    
    C:\Users\Administrator>adb shell ls /sdcard/Pictures | findstr Pi
    Pic
    ```

16. 重启设备

    * `adb reboot`

17. 屏幕分辨率

    * `adb shell wm size`
    * `wm：window manager`

18. 屏幕密度

    * `adb shell wm density`

19. 显示设备信息

    * `adb shell cat /proc/meminfo`

20. 应用内存使用情况（总内存、空闲内存、已用内存）

    * `adb shell dumpsys memoinfo <package>`
    * `<package>`：应用程序包名
    * `eg：adb shell dmupsys meminfo io.appium.android.apis`

21. 详细内存信息

    * `adb shell dumpsys meminfo`
    * 提供详细的内存信息，包括应用程序、系统进程和缓存的内存使用情况。输出会包括各个应用程序的内存使用统计，缓存和系统进程的内存信息等

22. 查看内存信息

    * `adb shell free`
    * 显示内存使用情况。包括物理内存和交换空间的总量、已使用和空闲量。

23. 查看电池信息

    * `adb shell dumpsys battery`

24. 查看CPU信息

    * `adb shell cat /proc/cpuinfo`

25. 单独查看属性

    * `adb shell getprop <属性>`
    * 查看设备信号：`adb shell getprop ro.product.model`
    * 查看`Android`版本：`adb shell getprop ro.build.version.release`

26. 查看包名

    * 查看第三方包名：`adb shell pm list packages -3`
    * 查看所有包名：`adb shell pm list packages`
    * `pm：package manager`

27. 查看将要启动后者关闭的包名

    * `adb shell am monitor`
    * 输入`q`退出
    * `am`：`activity manager`

28. 关闭软件（根据包名）

    * `adb shell am force-stop <package_name>`

29. 导出设备内的`apk`文件

    * ①使用`adb shell pm list packages [-3]`查看包名

    * ②找到`apk`文件在设备上的路径：`adb shell pm path <package>`

    * ③将`apk`文件导出：`adb pull <path> <PC>`

    * ```cmd
      C:\Users\Administrator>adb shell pm list packages -3
      package:io.appium.settings
      package:com.ziqing.medical
      package:io.appium.uiautomator2.server
      package:io.appium.android.apis
      package:io.appium.uiautomator2.server.test
      
      C:\Users\Administrator>adb shell pm path io.appium.android.apis
      package:/data/app/~~7o2goKAu7WJ4cqCJ0v6yEA==/io.appium.android.apis-oABXWk4y1lXUckBUcWX9Ag==/base.apk
      
      C:\Users\Administrator>adb pull /data/app/~~7o2goKAu7WJ4cqCJ0v6yEA==/io.appium.android.apis-oABXWk4y1lXUckBUcWX9Ag==/base.apk C:\Users\Administrator\Desktop\shootpic
      
      /data/app/~~7o2goKAu7WJ4cqCJ0v6yEA==/io.appium.android.apis-oABXWk4y...e.apk: 1 file pulled, 0 skipped. 52.1 MB/s (6419067 bytes in 0.117s)
      ```

30. 查看占用内存最高的3个app

    * `adb shell top -m 3`

31. 刷新两次，返回所有程序所占内存

    * `adb shell top -n 2`

32. 获取正在运行应用的`activity`

    * `adb shell dumpsys package <package>`

33. 

**移动测试的连接操作**

通过配置连接参数，连接手机进行自动化测试

```py
# 导包
import time
from appium import webdriver
# 配置连接手机的参数信息
# 所有的参数都是字典的格式：键值对
desired_caps = {}
# 设备名字
desired_caps['deviceName'] = "127.0.0.1:62001"
# 系统名字
desired_caps['platformName'] = 'Android'
# app名字
desired_caps['appPackage'] = 'com.android.settings'
# app界面名字
desired_caps['appActivity'] = '.Settings'
# 创建连接
driver = webdriver.Remote('http://127.0.0.1:4723/wd/hub', desired_capabilities=desired_caps)
time.sleep(5)
driver.quit()

```

## 四、App自动化测试流程

1.核心业务流程梳理

* 确定自动化测试的目标以及范围
* 针对自动化的范围及需求进行分析
* 编写自动化测试的脚本
  * 编写冒烟测试的脚本进行第一轮线性脚本执行
  * 执行完之后将线性脚本用例进行深度封装以及引入测试框架
    * 选择设计模式：
      * POM设计模式
      * KDT设计模式
    * 使用测试框架`pytest`结合设计模式进行用例管理和深度封装
    * 用例自动化执行过程中集合日志信息`log`进行记录
    * 用例执行完之后自行话生成测试报告，通过`allure`第三方库实现
    * 将整个项目进行持续集成使用`jenkins`完成
  * 后期脚本需要进行定期维护以及迭代和更新

app自动化测试过程中核心技能点：

`Python + Appium + Pytest + Parametrize + CSV + POM + Log + Allure + Jenkins`

2.项目流程及描述

**站在项目已经完成雏形的基础上，进行项目梳理和总结**

* 整个项目的文件解析

  * 每个文件包和模块具体的使用规范

* 整个项目中的模块与文件之间的联系

  * 各个模块几包之间的导入作用

* 整体从项目完成之后的角度出发去讲项目搭建流程以及使用规范

  * **搭建app自动化测试环境**

    * 安卓的sdk
    * java的sdk
    * 安装appium
    * 使用真机或者安装手机模拟器
    * 安装第三方库： `Appium-Python-Client`

  * **编写自动化的用例线性脚本**

    * 新建一个项目
    * 创建一个script脚本包
      * 梳理核心业务模块和功能
        * 登录
        * 注册
        * 点赞评论收藏
        * 发布文章
        * 浏览文章
        * ...
        * 创建py模块进行线性脚本编写
          * 通过核心业务流程模块梳理出线性脚本的执行步骤
          * 然后通过编写自动化用例脚本执行完成第一轮正反例的冒烟测试
            * 梳理被操作的元素以及具体操作方法
            * 执行正反例之后进行断言
            * 保证自动化用例的准确性

  * **选择设计模式pom**

    * 封装

      * 一个类就是一个流程用例或者一个界面
      * 将界面中需要被操作的每一个元素封装成类属性
      * 将界面中需要被执行的用例步骤定义成一个实例方法

    * 使用

      * 创建页面类对象
      * 通过对象调用方法执行用例步骤脚本
      * 获取实际结果进行断言

      

  * **引入测试框架结合用例执行以及管理**

    * 用例进行综合管理
    * 使用参数化进行数据驱动测试
    * `@pytest.mark.parametrize`
    * 一般情况再用例执行之前将参数化使用的实例进行读取使用
      * 数据量较多的情况下会使用不同的文件格式存储数据
        * CSV
        * text
        * excel
        * yaml
        * JSON
        * ...
    * 数据驱动测试主要核心就是在用例设计的过程中把所有用例需要的正反例数据进行保存，然后在使用的使用自动读取自动化操作用例完成脚本内容
    * **日志信息的记录**
    * **缺陷报告的生成**
    * **持续集成的处理**

    

    

## 五、测试要点

### 1.功能性测试

#### 1.1安装与卸载

* **软件安装前：**
  * 空间不足时是否有响应提示
* **软件安装中：**
  * 安装过程中是否可以取消  -- 强调杀掉进程
  * **安装是否可以正常运行**
  * 空间不足时是否有相应提示
* **软件安装后：**
  * 是否可以卸载应用：**1）通过桌面卸载  2）通过软件设置卸载**
  * 常见bug：发现在ios手机上有个应用安装时未完全安装，终止安装后，未完成安装的应用图标一直显示在手机上，并且无法成功删除
  * 卸载是否支持取消功能，单击取消后软件卸载功能是否正常
  * 安装完是否自动删除安装包
* 重复安装：1）提示版本已存在 2）直接覆盖安装，但是能正常使用   V1.0-->V1.0
* **架构**
  * b/s架构：通过浏览器可以访问的项目
  * c/s架构：通过客户端才可以访问的项目 --- app应用程序



#### 1.2.app升级测试

* **升级之前：**
  * 当客户端有新版本时，是否有更新提示；
* **非强制升级版** -- 需要跟开发沟通确认
  * 用户可以取消更新，老版本能正常使用，用户在下次启动app时，仍能出现更新提示；
  * 立即更新之后，正常升级，升级之后，新版本正常使用
  * 选择升级到主流版本
  * 能否升级成功，升级新功能是否正常，老功能是否正常，数据是否无异
* **强制升级版**
  * 用户没有做更新时，退出客户端，下次启动app时，仍出现强制升级
  * 强制升级完毕后，新版本正常使用
* **在线跨版本升级**
  * 升级后正常使用
  * 选择的版本 --- 通过运营数据，用户使用量最多的版本；没有运营数据，边界值
  * 需要跟开发确认，版本太多维护比较高
* **问题：**版本升级测试的覆盖范围有哪些？ --- 新功能、就功能 ---新功能详细测试，旧功能流程基本跑一遍



#### 1.3.app登录测试

* **登录方式**
  * **用户名**、**短信验证码**、指纹、**二维码**、手势、语音、头像识别、**第三方登录（微信、QQ、微博）** -- 需求有无实现
  * 一些页面的操作，是否做了控制 -- 天猫淘宝 -- 加了购物车
  * 点击某些资源，提示登录或者跳转登录页面
  * 用户主动退出登录后，下次启动APP时，应该进入登录界面 -- QQ、微信
* 切换账号登录：检验登录的信息是否做到及时更新 -- QQ
* 单个端登录：单点登录--无论任何一个端登录，数据都同步；单点登录--只允许登录一个端，IT圈
  * 不允许多个端登录时，是否将原用户踢下线，且能够给出提示信息
  * 允许多个端登录时，提示信息；且确保数据库操作无误，每个端可以及时看到数据的更新
* 用户登录持续时间太久，账号信息会过期
  * 出现”虽然是登录状态，系统会提示用户没有登录“
  * 强制退出，提示:账号信息过期，请重新登录“或者 跳转登录界面



#### 1.4.离线测试

* **App离线测试**
  * **应用程序在本地客户端会缓存一部分数据以供程序下次调用，对于一些程序，离线状态下可以**
    **览本地数据。---微信/QQ**
    * 对于离线(无网络)时，刷新获取新数据时，不能获取数据时能给出友好提示
    * 离线下，退出APP再开启APP时能正常浏览本地缓存数据
    * 离线下，切换到主屏幕再切回APP应用时可以正常浏览
    * 离线下，锁屏后再解锁回到应用前台可以正常浏览
  * **对于界面的数据不提供离线查看，需要给出相应提示且界面更新后无任何数据**



#### 1.5.触屏及操作测试

* 触屏快捷手势:两指、三指滑动
* 长按、短按屏幕
* 手机横屏、竖屏测试----经常出现闪退
* 同时触摸不同的位置，同时进行不同操作

查看客户端的处理情况，是否会**crash**（崩溃、闪退）、**ANR**（application not response 无响应）
**注意:需要跟开发和产品确认是否支持!**



#### 1.6.app消息推送测试

* **开关设置**:
  * 默认状态全部打开状态，客户可以接受到推送消息
  * 设置开关可以打开、关闭；APP设置开关关闭时，客户端接收不到消息推送
* **手机客户端未锁屏时**:
  * 应用后台运行，消息推送是否可正常接收，且可以点击查看   ---屏幕上方，点击跳转对应应用程序
  * APP应用前台使用，可以收到消息提醒，且点击可查看
* **手机客户端锁屏时**：消息推送是否正常接收  ---高亮显示消息，点击屏幕，消息列表；点击其中一条，解锁屏幕，转对应页面
* **登录状态**:
  * 退出登录后，是否接收push推送(根据需求来)
  * 未登录用户再去登录：批量接收多条消息推送
  * 当推消息是针对登录用户的时候，需要检查收到的push与用户身份是否相符，没有错误地将其它人的消息推送过来
* 消息栏(通知中心)是否可以接收到消息提醒，且点击可查看，点击后消息栏中消失



### 2.UI界面测试

* 确保产品UI符合产品经理制定的原型图与ui设计效果图/切图一致
* 依据经验、用户使用习惯、参考其他成熟的产品，界面可优化的bug
  * 一般涉及界面(如菜单、对话框、窗口和其他可视控件)布局、风格，文字是否正确，
    页面是否美观，操作是否友好。
    如：安装应用程序后的加载页/动态视频显示，分享页面的产品logo显示

**注意:UI界面测试和web思路基本一致**



### 3.兼容性测试-适配/手机适配

* 应用是否可以在不同操作系统正常使用(Android和IOS)，那么在这两个平台都要做兼容性测试;
  每个平台的**不同系统版本**
  * 安卓版本：6，7，8以及各个小版本
  * 10S版本：9，10、11、12、13，及其各个小版本
* 能否适配各种**屏幕尺寸**
  * Android系列：4.5英寸，5.0英寸等(phone，pad)
  * IOS系列:3.5英寸、4英寸、4.8英寸、5.5英寸(iPhone&iPad)
* 分辨率适配:分辨率影响界面图标，文字大小，保证主流分辨率下页面显示完整，文字不被遮挡

总结

* 兼容性测试一般覆盖:安卓系统手机，造取市面上主流手机就行，例如:小米，华为，vo、oppd等；ios系统手机就是6s、6plus、7、8plus，x，xs，11，11plus
  市场占有率最高:https://tongji.baidu.com/research/app?source=index
* 公司里有哪些测试手机，我就去测试哪些测试手机的兼容性测试，界面测试：在测试报告中体现

了解：云测平台，免费50款机型；**安装**、**打开app**、**monkey**、**关闭应用**、**卸载**  30分钟  测试报告一辅助测试

testion、腾讯、百度、阿里



### 4.app中断测试

* APP被手机行为打扰的情况：APP能否正常处理，保证数据正确性
* 主要对于**核心功能** **存在实时数据交换**的页面去进行中断测试，除了确保中断过程中有合理处理；还需确保中断过后，恢复正常
  * 来电、来短信、锁屏解锁、断网重连、断电、低电量、前后台切换、app切换
  * 手机端硬件上，如：待机，插拔数据线、耳机、闹铃弹出框提示等操作
* 常见bug场景:
  * 爱奇艺视频播放过程中，微信通话/电话中断:
    * 正常:app暂停状态:接听完电话之后，恢复直播正常
    * 异常:APP卡死、音视频不同步
  * 微信视频聊天，低电量提醒中断:(电话中断?)
    * 正常：聊天不中断，关掉提示信息后，正常通讯
    * 异常：APP卡死/期溃，微信聊天被强行断开

### 5.网络测试

* 测试2G/3 G/**4 G/5G/wifi4** /wifi6网络的切换

  例如从wifi环境切换到4G环境提示是否启用4G网络，会产生扣费，是否有提醒

* 测试有网/无网切换下应用的运行
  * 有网到无网再到有网环境时，数据是否可以自动恢复，正常加载(网络中断重连)
  * 无网络时，各种提示信息是否友好，数据本地化是否正确(比如提示当前已断开网络，请
    检查网络设置)
* **弱网测试(延时+丢包)**，关注弱网杨最下超时是否有合理提示，且是否有重发机制
  * 提交数据是否一直处理提交中，是否会有延迟(3分钟)，数据交换失败是否会有提醒;
  * 3条--2条：1条重发。你能收到-微信。重发！/自动重发
    * 数据多次提交(支付类app)，是否只能被执行一次
    * 最大尝试次数，APP是否正常工作





### 6.安全测试

* 扣费风险:包括发送短信、拨打电话、连接网络，没有网络时能否提醒===友好提示

* 隐私泄露风险:包括访问手机信息、访问联系人信息等

* 是否允许访问相册、拍照  开放权限+不权限权限

* 是否允许录音

* 是否允许定位

* 是否允许接收通知推送

  **注意:默认情况下是开放的，需要提示用户选择!**

  

* 一般对于大多数非支付类App来说，安全并不是一个特别大的问题，只需要保证**登录鉴权**的安全性即可。包括登录的sql注入测试(安全测试);敏感数据 加密!
* 没有安全性测试要求，可以不用考虑(sql注入，安全扫描等)

### 7.性能测试









### 8.应用程序测试与网络测试的区别?--理解记忆，面试概率比较高!!!

* **相同点:**
  * 同样的测试用例设计方法;-功能测试
  * 同样的测试方法;都会依据原型图或者效果图检查UI；--界面UI测试
  * 测试页面载入和翻页的速度、登录时长、内存是否溢出等; --性能和易用性
  * 测试应用系统的稳定性 --稳定性(性能)
* 不同点:
  * app的中断测试:来电中断、短信中断、蓝牙、闹钟、插拔数据线、手机锁定、手机断电、手机问题(系统死机重启)
  * app的安装卸载升级:全新安装、升级安装、第三方工具安装/卸载、直接制除卸载
  * 消息推送测试、手机授权测试、前后台切换、网络环境(wifi/2G/3G/4G/5G/无网络)
  * 兼容性测试:web项目考虑不同浏觉器的兼容;app需要考虑手机不同操作系统、不同机型、不同屏幕等的兼容

app云测试平台:百度云测、测试云测等
众测?-兼职:
http://www.open-open.com/lib/view/open1463526042631.html

不强制作业:免费兼容性测试
测试云测，免费50款机型;安装、打开app、monkey、关闭app、卸载     测试报告



## 六、h5测试

### 1.小程序是什么

### 2.小程序的架构

### 3.小程序的测试要点

### 4.h5是什么

### 5.h5的测试要点



## 七、模拟机

### 1.nox

1.1 下载其他版本

模拟器助手 --> 左侧的 多开管理 --> 右下角的 添加模拟器 按钮

## 八、Appium连接模拟机

#### 1.获取模拟机当前页面UI

①`Appium Server GUI --> Advanced --> Allow Cors`

`在Appium Inspecto`r中：

- `Remote Host: 127.0.0.1`
- `Remote Port: 4723`
- `Remote Path: /wd/hub`

②`cmd->appium --allow-cors`

`在Appium Inspecto`r中：

- `Remote Host: 127.0.0.1`
- `Remote Port: 4723`

```json
{
  "platformName": "Android",
  "appium:platformVersion": "12",
  "appium:deviceName": "SM-G988N",
  "appium:appPackage": "com.android.settings",
  "appium:appActivity": "com.android.settings.Settings",
  "appium:automationName": "UiAutomator2",
  "appium:udid": "127.0.0.1:62025"
}
```

点击`Start Session`

#### 2.`Appium Inspector`介绍

2.1 布局

* 左侧是应用程序的屏幕截图（快照视图）
* 中间是应用程序的层次结构，表示为XML
* 右侧是元素信息视图

2.2 功能说明

* `< Press Back Button` ：操作回退。Inspector窗口和手机都会同时回到上一个操作的界面
* `○ Press Home Button`：返回主屏幕。
* `□ Press App Switch Button`：应用切换。快速切换最近打开使用的应用程序  ？？？不成功
* `Native App Mode`：切换为原生APP模式
* `Web/HyBird App Mode`：切换为混合APP模式

#### 3.知识点

##### 2.1 元素句柄（Element Handles）

1. **本质**：Appium 为页面上的每个可识别元素分配的 **唯一内部标识符**，类似于 Web 开发中的 DOM 节点引用。这个ID在当前会话中是稳定的，但每次会话会变化。
2. 生命周期：
   - 创建：页面加载时自动生成
   - 销毁：元素不再可见/页面刷新时失效
3. 特性：
   - 仅在当前会话中有效
   - 不可跨页面重用
   - 不保证连续执行中的不变性

**注：**句柄是动态生成的，每次启动App时都可能变化，所以**不要硬编码句柄到测试脚本中**

4. 主要作用和应用场景

   4.1. 调试辅助工具

   - **问题定位**：快速识别哪个元素导致测试失败
   - **对比分析**：比较相同UI在不同状态下的句柄差异



Python + Appium命令行(v2.18.0)

```py
from appium import webdriver
from appium.options.android import UiAutomator2Options

def test_001():
  options = UiAutomator2Options()
  options.platform_name = 'Android'
  options.platform_version = '12'
  options.device_name = 'SM-G988N'
  options.app_package = 'io.appium.android.apis'
  options.app_activity = 'io.appium.android.apis.ApiDemos'
  options.automation_name = 'UiAutomator2'

  print("连接Appium服务器...")
  driver = webdriver.Remote("http://127.0.0.1:4723", options=options)
  driver.implicitly_wait(10)
  print("驱动初始化完成")


  # 退出程序
  driver.quit()

if __name__ == '__main__':
  test_001()

```



九、`app-debug.apk`

[app-debug.apk](https://github.com/appium/android-apidemos)

1. **准备环境**
   - 确保已安装Android Studio和SDK
   - 确保已配置环境变量（ANDROID_HOME，PATH中加入platform-tools和tools目录）
   - 安装最新版Node.js（用于构建脚本，但非必须，因为也可以直接用Android Studio构建）
2. **项目结构**
   - 下载的项目应该包含以下关键文件：
     - `settings.gradle` 和 `build.gradle` (项目级)
     - 各个模块的`build.gradle` (例如app模块)
     - `gradlew` 和 `gradlew.bat` (Gradle包装器)
     - 可能包含一个`scripts`目录，里面有构建脚本
3. **构建APK的方法**：使用Gradle命令行

​	`gradlew.bat assembleDebug`

4. 构建卡住了 `ctrl+c`

**原因分析：**

1. **构建进程卡住**：可能是Gradle守护进程挂起
2. **资源不足**：内存或CPU资源耗尽
3. **网络阻塞**：依赖下载卡住
4. **死锁问题**：并发构建冲突

`gradlew.bat --stop`

`gradlew.bat assembleDebug --no-daemon`

构建成功后，在`app/build/outputs/apk/debug/`目录下会生成`app-debug.apk`

## 九、操作

1. 页面

* 获取页面标题`activity`：`driver.current_activity()`

* ```py
  elem = self.driver_.find_element(AppiumBy.XPATH, '//android.widget.TextView[@content-desc="Animation"]')
  # 元素的类型（Android/iOS控件的类名）
  print(elem.tag_name)
  # 元素的可视文本内容
  print(elem.text)
  # 元素是否可见 bool
  print(elem.is_displayed()) 
  # 元素是否可用（可点击/可输入） bool
  print(elem.is_enabled() )
  # 元素是否被选中（复选框/单选框） bool
  print(elem.is_selected())
  # 获取元素的位置（坐标）
  element.location        # 返回: {'x': 100, 'y': 200}
  
  # 获取元素的尺寸
  element.size            # 返回: {'width': 300, 'height': 50}
  
  # 获取元素的边界矩形
  element.rect            # 返回: {'x': 100, 'y': 200, 'width': 300, 'height': 50}
  
  
  # 获取元素的resource-id（Android特有）
  element.get_attribute("resource-id")  # 返回: 'com.example:id/title'
  
  # 获取元素的content-desc（等同于Accessibility ID）
  element.get_attribute("content-desc")  # 返回: 'welcome_message'
  
  # 获取checked状态（复选框/开关）
  element.get_attribute("checked")       # 返回: 'true' 或 'false'
  
  # 获取元素类的完整信息
  element.get_attribute("class")         # 返回: 'android.widget.TextView'
  
  # 横竖屏
  driver_.orientation  # 返回：PORTRAIT：竖屏；LANDSCAPE：横屏
  driver_.orientation = "LANDSCAPE" #设置为横屏
  ```

* 启动多窗口支持

  * ```json
    {
      "appium:ignoreHiddenApiPolicyError": true,
      "appium:enableMultiWindows": true
    }
    ```

  * `appiun inspector`可以打开`element handles`捕捉多窗口
