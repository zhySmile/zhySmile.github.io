---
title: Behaviour.enabled、Behaviour.isActiveAndEnabled、GameObject.activeInHierarchy 区别
categories: 技术分享
tags: [Unity、API]
---
Behaviour.enabled、Behaviour.isActiveAndEnabled、GameObject.activeInHierarchy 区别
<!-- more -->
最近在写代码的时候，遇到了三个不太清楚的 API（Behaviour.enabled、Behaviour.isActiveAndEnabled 和 GameObject.activeInHierarchy），去看了 unity 文档，理解的也不是很清楚，于是自己去研究了一下这三个 API 的差异。
- - -
# 一.关于 Behaviour.enabled

Unity 官方文档介绍：Enabled Behaviours are Updated, disabled Behaviours are not.

使用这个 API，分别查看脚本所绑定 GameObject 和脚本激活与失活状态下，变量的情况，结果如下：

    1.当 GameObject = Active，Script = Enabled 的时候，Behaviour.enabled = true
    2.当 GameObject = Active，Script = Disabled 的时候，Behaviour.enabled = false
    3.当 GameObject = Inactive，Script = Enabled 的时候，Behaviour.enabled = true
    4.当 GameObject = Inactive，Script = Disabled 的时候，Behaviour.enabled = false

**结论**：Behaviour.enabled 与 GameObject 的状态无关，只与 Script 的状态有关。
① 当 Script = Enabled，Behaviour.enabled = true。
② 当 Script = Disabled，Behaviour.enabled = false。

# 二.关于 Behaviour.isActiveAndEnabled

Unity 官方文档介绍：Has the Behaviour had enabled called.

使用这个 API，分别查看脚本所绑定 GameObject 和脚本激活与失活状态下，变量的情况，结果如下：

    1.当 GameObject = Active，Script = Enabled 的时候，Behaviour.isActiveAndEnabled = true
    2.当 GameObject = Active，Script = Disabled 的时候，Behaviour.isActiveAndEnabled = false
    3.当 GameObject = Inactive，Script = Enabled 的时候，Behaviour.isActiveAndEnabled = false
    4.当 GameObject = Inactive，Script = Disabled 的时候，Behaviour.isActiveAndEnabled = false

**结论**：Behaviour.isActiveAndEnabled 与 GameObject 和 Script 两者的状态有关。
① 只有当 GameObject = Active，Script = Enabled 的时候，Behaviour.isActiveAndEnabled = true。
② 其余情况，GameObject 或 Script 中任何一个处于失活状态，Behaviour.isActiveAndEnabled = false。

# 三.关于 GameObject.activeInHierarchy

Unity 官方文档介绍：Defines whether the GameObject is active in the Scene.

使用这个 API，分别查看 GameObject 和这个 GameObject 节点以上的所有父 GameObject 激活与失活状态下，变量的情况，结果如下：

    1.当 Parent GameObject = Active，GameObject = Active 的时候，GameObject.activeInHierarchy = true
    2.当 Parent GameObject = Active，GameObject = Inactive 的时候，GameObject.activeInHierarchy = false
    3.当 Parent GameObject = Inactive，GameObject = Active 的时候，GameObject.activeInHierarchy = false
    4.当 Parent GameObject = Inactive，GameObject = Inactive 的时候，GameObject.activeInHierarchy = false

**结论**：GameObject.activeInHierarchy 与父 GameObject 和当前 GameObject 两者的状态有关。
① 只有当 Parent GameObject = Active，GameObject = Active 的时候，GameObject.activeInHierarch = true。
② 其余情况，Parent GameObject 或 GameObject 中任何一个处于失活状态，GameObject.activeInHierarch = false。

以上是关于这三个 API 区别的一点研究，使用时注意按需去取。