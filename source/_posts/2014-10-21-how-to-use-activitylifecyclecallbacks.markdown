---
layout: post
title: "How To Use ActivityLifecycleCallbacks"
date: 2014-10-21 15:08:47 +0800
comments: true
categories:
---
# Android开发 - ActivityLifecycleCallbacks使用方法初探

---

## ActivityLifecycleCallbacks是什么？

- Application通过此接口提供了一套回调方法，用于让开发者对Activity的生命周期事件进行集中处理。

## 为什么用ActivityLifecycleCallbacks？

- 以往若需监测Activity的生命周期事件代码，你可能是这样做的，重写每一个Acivity的onResume()，然后作统计和处理：
      @Override
      protected void onResume() {
        super.onResume();
        //TODO 处理和统计代码
        Log.v(TAG, "onResume");
        Logger.v(TAG, "onResume");
        Logging.v(TAG, "onResume");
        ...
      }
- ActivityLifecycleCallbacks接口回调可以简化这一繁琐过程，在一个类中作统一处理

## ActivityLifecycleCallbacks怎么用？

- android.app.Application.ActivityLifecycleCallbacks
- 要求API 14+ （Android 4.0+）
- 继承Application
      public class BaseApplication extends Application
- 在AndroidManifest里起用自定义Application
      <application android:name=".global.BaseApplication"
- 重写Application的onCreate()方法，调用Application.registerActivityLifecycleCallbacks()方法，并实现ActivityLifecycleCallbacks接口
      public void onCreate() {
        super.onCreate();
        this.registerActivityLifecycleCallbacks(new ActivityLifecycleCallbacks() {

          @Override
          public void onActivityStopped(Activity activity) {
            Logger.v(activity, "onActivityStopped");
          }

          @Override
          public void onActivityStarted(Activity activity) {
            Logger.v(activity, "onActivityStarted");
          }

          @Override
          public void onActivitySaveInstanceState(Activity activity, Bundle outState) {
            Logger.v(activity, "onActivitySaveInstanceState");
          }

          @Override
          public void onActivityResumed(Activity activity) {
            Logger.v(activity, "onActivityResumed");
          }

          @Override
          public void onActivityPaused(Activity activity) {
            Logger.v(activity, "onActivityPaused");
          }

          @Override
          public void onActivityDestroyed(Activity activity) {
            Logger.v(activity, "onActivityDestroyed");
          }

          @Override
          public void onActivityCreated(Activity activity, Bundle savedInstanceState) {
            Logger.v(activity, "onActivityCreated");
          }
        });
      };

- 运行结果(Logcat日志)
      10-21 14:32:57.722: V/WelcomeActivity(8085): onActivityCreated
      10-21 14:32:57.762: V/WelcomeActivity(8085): onActivityStarted
      10-21 14:32:57.762: V/WelcomeActivity(8085): onActivityResumed
      10-21 14:32:59.164: V/WelcomeActivity(8085): onActivityPaused
      10-21 14:32:59.194: V/MainActivity(8085): onActivityCreated
      10-21 14:32:59.224: V/MainActivity(8085): onActivityStarted
      10-21 14:32:59.224: V/MainActivity(8085): onActivityResumed
      10-21 14:32:59.735: V/WelcomeActivity(8085): onActivityStopped
      10-21 14:32:59.735: V/WelcomeActivity(8085): onActivityDestroyed
      10-21 14:33:06.502: V/MainActivity(8085): onActivityPaused
      10-21 14:33:06.612: V/MainActivity(8085): onActivityStopped
      10-21 14:33:06.612: V/MainActivity(8085): onActivityDestroyed


## ActivityLifecycleCallbacks的拓展用法
- 本次初探仅尝试使用Log日志工具作简要测试，如需满足较复杂的统计或调试需求时，此法可能会大大减少插入代码量，提高效率

- API仅在14+版本上提供此接口回调，[Android 4.0以下系统如何使用？](http://codego.net/456648/)
- API仅针对上述几个Activity的生命周期事件留出了接口回调，可能已无法满足日益过渡为使用Fragment的今日需求， [何在更大范围内应用LifecycleCallbacks？](https://github.com/soarcn/AndroidLifecyle)
