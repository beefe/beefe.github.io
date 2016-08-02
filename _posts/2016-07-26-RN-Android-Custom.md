---
layout: post
title: React Native的Android Native定制开发
author: shexiaoheng
categories: tech_note
tag: Android
---

使用RN开发APP，可能要做的某个功能只能用Native来实现，比如调用摄像头、打电话等，但是RN又没有提供相应的组件或模块，
这就需要我们去定制一个Native Module或Native Components。定制完成后可以放在git上，这样再遇到相同功能可以直接引用，
或者发布一个npm模块共享出来，也能帮助下别人。

<!-- more -->

开发工具采用Android Studio，首先创建一个[Android Library](#android-library)，然后添加[RN Dependency](#rn)

不能同时拥有Module和Component两个身份，定义为Module的不能包含组件，反之同理。

* [开发Native Module](#module)
* [开发Native Components](#components)

### 定义Module类

```java
public class ModuleName extends ReactContextBaseJavaModule {  // 根据开发的功能来定义ModuleName

    /** 
     * 根据开发的功能来定义JSModuleName
     * JS端通过此名称来使用本Module
     * 若前面有RCT,RN会自动忽略，如:RCTCardHelp，在JS端实际为CardHelp，建议一律不加RCT
     * */
    private final static String REACT_CLASS = "JSModuleName";

    public ModuleName(ReactApplicationContext reactContext) {
        super(reactContext);
    }

    @Override
    public String getName() {
        return REACT_CLASS;
    }

}
```

在Module类里定义一个方法供JS调用

```java
/**
 * 根据方法的功能来定义methodName
 * 类型必须为public void
 * */
@ReactMethod
public void methodName(){
    
}

/**
 * Callback通知JS结果
 *
 * result类型与JS类型的对应关系：
 *  Boolean -> Bool
 *  Integer -> Number
 *  Double -> Number
 *  Float -> Number
 *  String -> String
 *  Callback -> function
 *  ReadableMap -> Object
 *  ReadableArray -> Array
 * */
@ReactMethod
public void methodName(Callback callback){
    try{
        callback.invoke(null,result);
    } catch (Exception e){
        callback.invoke(e);
    }
}

/**
 * Promise通知JS结果
 * result同上
 * */
@ReactMethod
public void methodName(Promise promise){
    try{
        promise.resolve(result);
    } catch (Exception e) {
        promise.reject("err",e);   
    }
}
```

如果程序需要调用第三方SDK，比如使用微信SDK的分享功能，就需要一个Events来通知分享的结果

```java
/**
 * reactContext为Module构造函数传入的参数
 * eventName对应JS获取事件的名称
 * params为返回结果,类型为object
 * */
private void sendEvent(ReactContext reactContext,
                       String eventName,
                       @Nullable WritableMap params) {
  reactContext
      .getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class)
      .emit(eventName, params);
}
```

有时候需要启动一个Native界面，比如调用相机拍照

```java
public class ModuleName extends ReactContextBaseJavaModule implements ActivityEventListener {

    private final static int REQUEST_CODE = 0x1001;
    private Callback mCallback;
    private Promise mPromise;

    public ModuleName(ReactApplicationContext reactContext) {
        super(reactContext);
        reactContext.addActivityEventListener(this);
    }

    /**
     * 以Callback为例，Promise同理
     * */
    @ReactMethod
    public void methodName(Callback callback){
        Activity currentActivity = getCurrentActivity();
        if (currentActivity == null) {
            callback.invoke("Activity doesn't exist");
            return;
        }
        this.mCallback = callback;
        try {
            currentActivity.startActivityForResult(
                    new Intent(
                            getReactApplicationContext().getBaseContext()
                            , TargetActivity.class), REQUEST_CODE);
        } catch (Exception e) {
            mCallback.invoke(e);
            mCallback = null;
        }
    }

    @Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        if (resultCode == Activity.RESULT_OK && requestCode == REQUEST_CODE) {
            if (mCallback != null) {
                if (data != null) {
                    String result = data.getStringExtra("result");
                    mCallback.invoke(result);
                } else {
                    mCallback.invoke("no data found");
                }
                mCallback = null;
            }
        }
    }
}
```

如果需要监听程序的生命周期，RN提供了如下方式

```java
public class ModuleName extends ReactContextBaseJavaModule implements LifecycleEventListener {

    public ModuleName(ReactApplicationContext reactContext) {
        super(reactContext);
        reactContext.addLifecycleEventListener(this);
    }

    @Override
    public void onHostResume() {
        // Actvity onResume
    }

    @Override
    public void onHostPause() {
        // Actvity onPause
    }

    @Override
    public void onHostDestroy() {
        // Actvity onDestroy
    }

}
```

### 创建Package

```java
public class PackageName implements ReactPackage{   // 根据开发的功能来定义PackageName

    @Override
    public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
        return Arrays.<NativeModule>asList(new ModuleName(reactContext));   //在此处把定义的Module加进来
    }

    @Override
    public List<Class<? extends JavaScriptModule>> createJSModules() {
        return Collections.emptyList();     
    }

    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
        return Collections.emptyList();
    }
}
```

### 创建index.js

```javascript
var React = require('react-native');

var { NativeModules } = React;

module.exports = NativeModules.JSModuleName;   // 对应定义在Module里的REACT_CLASS
```

### 创建package.json
推荐采用命令行来创建package.json

```shell
$ npm init
```


### 定义Components类

```java
/**
 * 根据开发的组件来定义ViewNameManager
 * YouView为你开发的组件，可以是Android提供的View，也可以是自定义的View
 * */
public class ViewNameManager extends SimpleViewManager<YouView> {

     /** 
      * 根据开发的组件来定义RCTViewName
      * JS端通过此名称来使用本组件
      * RCT不会被忽略，名称与JS端一致
      * */
    private static final String REACT_CLASS = "RCTViewName";

    private static final int COMMAND_METHOD_ONE = 1;
    private static final int COMMAND_METHOD_TWO = 2;

    @Override
    protected YouView createViewInstance(ThemedReactContext reactContext) {
        return new YouView(reactContext);
    }

    @Override
    public String getName() {
        return REACT_CLASS;
    }

    /**
     * @ReactProp用来定义组件的属性
     * 支持下列属性类型：
     *  String
     *  boolean
     *  float
     *  int
     *  ReadableArray
     *  ReadableMap(应该也支持，未验证)
     * */
    @ReactProp(name = "isLoop",defaultBoolean = true)
    public void isLoop(YouView view, boolean isLoop){
        view.isLoop(isLoop);
    }


    /**
     * 给JS提供methodOne()和methodTwo()两个方法
     * 可以是一个或更多个方法，具体方法名和数量根据需要定义
     * */
    @Override
    public Map<String,Integer> getCommandsMap() {
        return MapBuilder.of(
                "methodOne",
                COMMAND_METHOD_ONE,
                "methodTwo",
                COMMAND_METHOD_TWO);
    }

    /**
     * 当JS调用methodOne()或methodTwo()时，本地方法的相应实现
     * */
    @Override
    public void receiveCommand(YouView view, int commandId, ReadableArray args) {
        switch (commandId) {
            case COMMAND_METHOD_ONE: {
                view.previous();
                return;
            }
            case COMMAND_METHOD_TWO: {
                view.next();
                return;
            }
            default:
                throw new IllegalArgumentException(String.format(
                        "Unsupported command %d received by %s.",
                        commandId,
                        getClass().getSimpleName()));
        }
    }

}
```

View的Event

```java
class YouView extends View {
   ...
   public void onReceiveNativeEvent() {
      WritableMap event = Arguments.createMap();
      event.putString("message", "MyMessage");
      ReactContext reactContext = (ReactContext)getContext();
      reactContext.getJSModule(RCTEventEmitter.class).receiveEvent(
          getId(),
          "topChange",
          event);
    }
}
```

### 创建Package

```java
public class PackageName implements ReactPackage{   // 根据开发的功能来定义PackageName

    @Override
    public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
        return Collections.emptyList();
    }

    @Override
    public List<Class<? extends JavaScriptModule>> createJSModules() {
        return Collections.emptyList();     
    }

    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
        return Arrays.<ViewManager>asList(new ViewNameManager());   //在此处把定义的ViewManager加进来
    }
}
```

### 创建index.js

```javascript
import React,{
    Component,
    PropTypes,
    View
} from 'react';

import ReactNative,{
    requireNativeComponent,
    NativeModules
} from 'react-native';

let UIManager = NativeModules.UIManager;

let NativeYouView = requireNativeComponent('RCTViewName',YouView);


export default class YouView extends React.Component{
    static propTypes = {
        isLoop: PropTypes.bool,
    };
    methodOne() {
        UIManager.dispatchViewManagerCommand(
            ReactNative.findNodeHandle(this),
            UIManager.RCTViewName.Commands.methodOne,       // 调用定义在ViewManager里的methodOne
            null,
        );
    };

    methodTwo(){
        UIManager.dispatchViewManagerCommand(
            ReactNative.findNodeHandle(this),
            UIManager.RCTViewName.Commands.methodTwo,       // 调用定义在ViewManager里的methodTwo
            null,
        );
    }

    render(){
        return <NativeYouView {...this.props} />;
    }
};
```

### 创建package.json
推荐采用命令行来创建package.json

```shell
$ npm init
```

### 创建Android Library

File -> New -> New Module -> Android Library -> give name

![](/img/2016-07-26-RN-Android-Custom/1.png)
![](/img/2016-07-26-RN-Android-Custom/2.png)
![](/img/2016-07-26-RN-Android-Custom/3.png)

### 添加RN依赖

File -> Project Structure -> you module -> Dependencies -> add Library Dependency

![](/img/2016-07-26-RN-Android-Custom/4.png)
![](/img/2016-07-26-RN-Android-Custom/5.png)
![](/img/2016-07-26-RN-Android-Custom/6.png)
![](/img/2016-07-26-RN-Android-Custom/7.png)
![](/img/2016-07-26-RN-Android-Custom/8.png)