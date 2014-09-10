---
layout: post
title: "PullToRefresh-PinnedSection-ListView"
date: 2014-08-21 16:13:33 +0800
comments: true
categories:
---
PullToRefresh-PinnedSection-ListView
===========
前段时间因为项目需求，需要在Android中对ListView同时增加下拉刷新和分段头悬停的效果，受到[dkmeteor](https://github.com/dkmeteor)的启发，Merge了两个Github上的开源项目：

 * [Android-PullToRefresh](https://github.com/chrisbanes/Android-PullToRefresh)(handmark版,目前已不再更新)

 * [StickyListHeaders](https://github.com/emilsjolander/StickyListHeaders)(目前版本为2.x)

 由于既有项目里的StickyListHeaders代码为1.x版本，StickyListHeadersListView继承自ListView，故与handmark版的PullToRefreshListView做merge时很顺畅；

 但2.x版的StickyListHeadersListView继承自FrameLayout，与PullToRefresh的融合并不顺利，若要整理拆分出一个独立的lib时遇到很多的问题，
 故在分断头悬停需求上采用了另一个类似的开源项目：

 * [pinned-section-listview](https://github.com/beworker/pinned-section-listview()

## 原理
