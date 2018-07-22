---
title: Unity 接入 Android SDK，切换黑屏问题
categories: 技术分享
tags: [Unity、Android、SDK]
---
Unity 接入 Android SDK，切换黑屏问题
<!-- more -->
***<label style="color:red">问题描述***：在 Unity 接入Android SDK 以后，打开 SDK 界面（例如登陆或支付），切换到后台，回来之后游戏背景黑屏问题（SDK 的界面还在，游戏内背景黑了）。

***<label style="color:blue">原因***：有些 SDK 的界面是采用启动一个新的 activity 的方式。所以导致我们在调用 SDK 的时候，游戏本身会调用生命周期的 pause 。这时候按下设备的 home 键，切换到后台，SDK 应该也会调用 pause 。然后重新返回游戏，这时候只有 SDK 会调用 resume，但我们游戏自己的程序并没有调用 resume。

***<label style="color:green">解决方法***：在 onPause 和 onStart 里分别判断当前 mainactivity 是否是 SDK 的 activity，假如是，就手动调用游戏 activity 的 onResume。

另附上 Android Activity 生命周期图；
![](/img/UnityAndroidSDKQuestion1/AndroidActivity.png)  