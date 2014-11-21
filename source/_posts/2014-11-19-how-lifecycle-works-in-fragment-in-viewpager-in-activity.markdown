---
layout: post
title: "How Lifecycle Works in Fragment in ViewPager in Activity"
date: 2014-11-19 17:20:51 +0800
comments: true
categories:
---
## Activity和Fragment各自理论上的生命周期

- Activity的生命周期是较为经典也最清晰的，在此不表；
- Fragment从出现到广泛运用也有一段时间了，其标准生命周期也仅比Activity多出一些流程，如onCreateView();
***
- Activity和Fragment在实际编码中必定是结合出现的，表现为Activity作为容器，装载有一个或若干个Fragment；
- 装载多个Fragment时，经常使用TabHost和Viewpager作为载体；
- 在实际编码中发现，Activity和Fragment的混合情况里，其生命周期的交叉可能与预想中有差别；
***
- 使用如下方式加载Fragment时：

      getSupportFragmentManager()
			.beginTransaction()
			.add(R.id.fragment_container, mFragment, SHARE_PUBLIC_LIST_FRAGMENT_TAG)
			.commitAllowingStateLoss();
其onResume和onPause执行过程为：
  - Activity - onResume
  - Fragment - onResume
  - Activity - onPause
  - Fragment - onPause

## 友盟-页面统计要求

- Activity和Fragment共同体页面统计中，需要保证线性不交叉，每个onPageStart都有一个onPageEnd配对，
- 如：onPageStart ->onPageEnd-> onPageStart -> onPageEnd -> onPageStart ->onPageEnd
- 这样才能保证每个页面统计的正确。

## Viewpager中Fragment的生命周期

- ViewPager装载Fragment一般使用FragmentPagerAdapter或FragmentStatePagerAdapter，同样借助FragmentManager，在adapter的getItem方法中根据position制定显示的fragment
- 由于Viewpager的缓存特点，Viewpager启动时其第一个Fragment页面及待缓存的页面都将按顺序呢开始他们的正常生命周期，走向onResume，即：
  - Viewpager所在Activity - onResume
  - Fragment1 - onResume
  - Fragment2 - onResume
  - Fragment3 - onResume
- 由于这若干个页面的生命周期被同时催化了，影响了我们的单一判断，即无法判断“真正”显示和消失在使用者眼前的页面。

## 解决方案

- 方案1：设置Viewpager的缓存机制，不缓存除当前页以外的页面数据，所见即所得，离开即销毁；
- 此方案对需求改动较大，且较影响用户体验;  
***
- 方案2：重载Fragment.onHiddenChanged(boolean hidden)方法，其参数hidden代表当前fragment显隐状态改变时，是否为隐藏状态，可通过check此参数作处理；
- 此方案局限在于本方法的系统调用时间发生在显隐状态改变时，但第一次显示时此方法并不调用；  
***
- 方案3：重载Fragment.setUserVisibleHint(boolean isVisibleToUser)方法，其参数isVisibleToUser顾名思义最接近我们的需求，代表页面是否“真正”对使用者显示；
- 此方案局限在于此方法的第一次系统调用甚至早于Fragment的onCreate方法，故其第一次调用时isVisibleToUser值总为false，影响我们对生命周期顺序的判定；
  - Fragment1 - isVisibleToUser - false (多余)
  - Fragment1 - isVisibleToUser - true
  - Fragment1 - isVisibleToUser - false
  - Fragment2 - isVisibleToUser - false (多余)
  - Fragment2 - isVisibleToUser - true
  - Fragment2 - isVisibleToUser - false

## 实际采用的解决方案

- 根据对产品需求的理解和用户体验的统一，选择在方案3基础上加以改进；
- setUserVisibleHint()方法本身很接近我们的需求，它的局限点我采取了一个侵入式的解决方式：
    	protected boolean isCreated = false;

    	@Override
    	public void onCreate(Bundle savedInstanceState) {
    		super.onCreate(savedInstanceState);

    		// ...
    		isCreated = true;
    	}

    	/**
    	 * 此方法目前仅适用于标示ViewPager中的Fragment是否真实可见
    	 * For 友盟统计的页面线性不交叉统计需求
    	 */
    	@Override
    	public void setUserVisibleHint(boolean isVisibleToUser) {
    		super.setUserVisibleHint(isVisibleToUser);

    		if (!isCreated) {
    			return;
    		}

    		if (isVisibleToUser) {
    			umengPageStart();
    		}else {
    			umengPageEnd();
    		}

    	}

- 对onCreate方法结束的一个标记即可解决问题；

## One more thing

- Viewpager装载Fragment时还有另一个坑，Viewpager的父容器（Activity或Fragment）在显隐状态改变时，
如在Activity的onResume和onPause调用时，并不会主动通知Viewpager内的Fragment执行其应用的生命周期，故我们需要再增加手动强制调用两次
- 在Activity中查询到当前的Fragment，并执行其内方法，需要借助Viewpager，并改写其PagerAdapter；
- 我采用了此文提到的Second Solution：[如何获得ViewPager当前显示的Fragment？](http://blog.csdn.net/piglovesula/article/details/12916279)
