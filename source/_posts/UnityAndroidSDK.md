---
title: Unity 接入 Android SDK
categories: 技术分享
tags: [Unity、Android、SDK]
---
Unity 接入 Android SDK
<!-- more -->
SDK：软件开发工具包（Software Development Kit），软件开发工具包广义上指辅助开发某一类软件的相关文档、范例和工具的集合

需要的开发环境：Unity、安卓开发工具（Eclipse 或 Android Studio）
一、首先阅读文档，有些文档说的不清楚，所以要多读，注意要反复阅读，不要遗漏任何细节
二、将示例 demo 看一遍，查看其调用方式，回调等等
三、接下来步入正轨，开始着手接入

Tips：为了调试的方便，可以先用一个测试小项目接入 SDK，节省调试成本和其余部分的影响，单纯的去测试 SDK 接入，待测试项目通过，再去接入项目内。

接入的SDK 一般分为两种：一种是封装好的适用于 Unity 的。这种就很简单了，只需要去看一下有哪些接口，直接去调用相应的接口就可以了。 但是更多的是另一种，纯 Android 的，这就需要我们去写一些 Android 部分，主要处理 Unity 与 Android 通信的内容。

对于接入纯 Android 的 SDK，我接过的流程如下：
1.创建 Android 工程，写 java 脚本，处理 Unity 与 Android 通信。
2.将 java 脚本打成 jar 包，放入 Unity 工程。
3.Unity 工程里去调用对应方法。

### 关于 Unity 与 Android 交互：
1.处理 Unity 接收 Android 回调：
UnityPlayer.UnitySendMessage(_unityCallBackObjectName, "LoginCancelCallBack", "");
参数1:GameObject名称，参数2：函数名，参数3:字符串参数。
注意：物体上不存在名称为参数2的函数，不会报错。参数3只能传一个字符串参数，不能传多个，如果需要传多个参数，只能分几个函数调用，或者字符串用特殊字符合并传递再拆分接收。但是参数3不能为null，否则会强制退出。

2.处理 Unity 调用 Android 函数：
AndroidJavaClass jc = new AndroidJavaClass("com.unity3d.player.UnityPlayer");
AndroidJavaObject jo = jc.GetStatic<AndroidJavaObject>("currentActivity");
jo.Call("Test");//调用 jar 包的 currentActivity 里的 Test 方法

以上就是 Unity 接入 Android SDK 的一个大致流程，实际接入的时候可能还会碰到一些奇奇怪怪的其他问题，这就需要和相应的 SDK 客服人员去沟通，接入 SDK 麻烦的地方就在于文档不清楚，沟通不便捷，调试不方便。只能说一句 Good Luck.