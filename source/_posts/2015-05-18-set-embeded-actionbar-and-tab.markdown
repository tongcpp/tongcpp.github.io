---
layout: post
title: "强制Actionbar与Tab显示为一行或两行"
date: 2015-05-18 15:38:24 +0800
comments: true
categories:
---
## Actionbar使用Tab模式

- ActionBar.setNavigationMode(ActionBar.NAVIGATION_MODE_TABS)即可另Actionbra使用tab作为导航模式；
- ActionBar.addTab(Tab tab)使用该方法为你的导航添加具体的Tab；

## 显示为单行还是两行？
- 根据developer官方指导，当屏幕宽度足够时，Tab将嵌入Actionbar显示为一行，如大屏Pad、手机横屏时；
- ![Action bar tabs on a wide screen](http://developer.android.com/images/ui/actionbar-tabs@2x.png)
- 当屏幕宽度较窄时，Tab显示在Actionbar下一行，总共两行，常见于手机竖屏时；
- ![Tabs on a narrow screen](http://developer.android.com/images/ui/actionbar-tabs-stacked@2x.png)

## 强制在所有情况下显示为单行或两行
- 有时需求在pad上显示双行Tab，或是在手机竖屏时显示为单行Actionbar；
- 强制显示为单行
      try {
				Method setHasEmbeddedTabsMethod = mActionBar.getClass().getDeclaredMethod(
						"setHasEmbeddedTabs", boolean.class);
				setHasEmbeddedTabsMethod.setAccessible(true);
				setHasEmbeddedTabsMethod.invoke(mActionBar, true);
			} catch (Exception ignore) {
			}

- 强制显示为两行
      try {
				Method setHasEmbeddedTabsMethod = mActionBar.getClass().getDeclaredMethod(
						"setHasEmbeddedTabs", boolean.class);
				setHasEmbeddedTabsMethod.setAccessible(true);
				setHasEmbeddedTabsMethod.invoke(mActionBar, false);
			} catch (Exception ignore) {
			}

## 使用ActionbarSherlock
- ActionbarSherlock在3.0系统版本及以上直接调用Android原生的Actionbar，在2.3及以下使用内建的Actionbar来兼容；
- 原生类ActionBarWrapper中，通过上边的反射修改private final android.app.ActionBar mActionBar；
- 兼容类ActionBarImpl中，指定setHasEmbeddedTabs(boolean hasEmbeddedTabs)中参数即可；
