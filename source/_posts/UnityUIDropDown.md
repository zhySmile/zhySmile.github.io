---
title: Unity UGUI - Dropdown
categories: 技术分享
tags: [Unity、UI]
---
UGUI 组件之 Dropdown 下拉框
<!-- more -->

前些日子在工作中遇到一个 Bug，是一个下拉框，第一次打开界面，点击下拉框，会触发正常的下拉框点击事件，不会触发到下拉框展开之后层级底下的按钮点击事件。但是当切换别的界面之后，再点击下拉框，下拉框点击事件就不会再触发，相反，下层按钮点击事件会触发。

看到这个 bug，首先想到的就是有可能是层级问题导致。然后去查代码，发现这个下拉框的实现是通过 UGUI 的 Dropdown 组件。之前并没有用过这个组件，对它的实现原理不了解。去复现问题发现原本下拉列表上没有 Canvas 组件，但是在下拉框展开之后，会给下拉列表添上一个 Canvas 组件，并且其 sortingOrder 很大，为30000。当打开别的界面之后，再点击下拉列表，其 sortingOrder 变小了，查找发现等于其父物体上 Canvas sortingOrder 的值。如此一来，就导致了层级错误，触发事件出问题。

接着我去查看 Dropdown 的源码，在第一次 Dropdown Show 的时候会去初始化下拉列表，添加 Canvas ，设置 sortingOrder = 30000，并且生成一个 Blocker 在根 Canvas 下，设置其 sortingOrder = (Dropdown Canvas).sortingOrder - 1，即 29999。我猜想这么设值的原因是，3000 足够大，能够保证下拉框是最上层，添加 Blocker 是为了防止点击到下层物体。

但是问题出在，我们的 UI 会根据界面展示时机去设置其 Canvas 的 sortingOrder，当打开别的界面返回的时候，会给这个下拉框的 sortingOrder 设置为和根 Canvas 相同的值，打开下拉框的时候，再次生成 Blocker 并设置 sortingOrder，就会造成遮挡功能失效，Bug 出现。

最终的解决方案是必要的时候手动再去设置下拉列表的 sortingOrder。