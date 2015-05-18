---
layout: post
title: "Handle onContextItemSelected in Fragment in Viewpager"
date: 2014-12-29 11:09:54 +0800
comments: true
categories:
---

## ContextMenu
上下文菜单ContextMenu由系统支持，常用于通过长按控件弹出列表形菜单，实现步骤如下
 - onCreateContextMenu方法 创建菜单
 - registerForContextMenu方法，为需响应的控件注册
 - onContextItemSelected方法，响应点击

## 在Viewpager中的Fragment使用ContextMenu发生的问题
在我的具体项目环境中，Viewpager中存在3个Fragment（0/1/2），并继承自一个父类BaseFragment。在父类中完成了上下文菜单的绝大部分工作，即上文提到的创建/注册/响应点击；
子类中仅重写onCreateContextMenu方法，用于区分修改菜单显示；那么问题来了：
 - 当点击Fragment0的上下文菜单的某项时，Fragment1和2的点击响应事件也同时执行了；
 - 当分别点击Fragment1和2的上下文菜单时，也仍然按照Fragment0-1-2的顺序将响应事件依次执行一遍；

## 解决方法
问题的症结在于Viewpager改变了其中的Fragment显式的生命周期和关联关系，在前文《关于Activity中的Viewpager中的Fragment的生命周期》我也提到过这一问题，并尝试采用setUserVisibleHint方法解决了问题；
在本文中同样，可以采用getUserVisibleHint()方法判断当前Fragment是否真实可见，如可见则响应，不可见则截止并向下分发，直到有可见页面响应为止。

 - 在3个Fragment子类中，均需重写
     @Override
    	public boolean onContextItemSelected(MenuItem item) {
    		if (getUserVisibleHint()) {
    			return super.onContextItemSelected(item);
    		}
    		return false;
    	}

 - return true  代表截断
 - return false 代表继续分发

## 另一种解决方案
- StackOverFlow提供了另一个解决方案，为每一个Fragment分别创建菜单，在创建时设置Group组号，并在分别响应点击时判断组号来完成截止和分发；
- 本文方法针对我的具体代码而言改动较小，更为简便清晰，故未采用上述方式。
