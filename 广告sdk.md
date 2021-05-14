# 1.sdk导入

### 1.1导入sdk依赖库

```java
//版本号 1.1.7

//导入我们广告库的sdk
    implementation 'com.boniu.ad:gdtsdk:4.351.1221@aar'
    implementation 'com.boniu.ad:csjsdk:3.6.0.0@aar'
    //快手
      implementation 'com.boniu.ad:kssdk:3.3.14@aar'
    implementation 'com.boniu.ad:adsdk:1.1.7@aar'

 //版本号 1.1.8
    implementation 'com.boniu.ad:gdtsdk:4.351.1221@aar'
    implementation 'com.boniu.ad:csjsdk:3.2.5.1@aar'
    implementation 'com.boniu.ad:kssdk:3.3.13@aar'
    implementation 'com.boniu.ad:adsdk:1.1.8@aar'
  
implementation 'com.squareup.okhttp3:okhttp:4.0.1'
implementation 'com.squareup.retrofit2:retrofit:2.5.0'
implementation("com.squareup.retrofit2:adapter-rxjava2:2.2.0")
            { exclude group: 'com.squareup.okhttp3' }
implementation 'com.google.code.gson:gson:2.8.6'
implementation 'com.github.bumptech.glide:glide:4.10.0'


//maven
maven { url 'http://nexus.rhinox.cn/repository/maven-public/'}
```

### 1.2AndroidManifest配置

#### 1.2.1 添加权限

```java
<!--必要权限-->
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES"/>
<uses-permission android:name="android.permission.GET_TASKS"/>

<!--可选，穿山甲提供“获取地理位置权限”和“不给予地理位置权限，开发者传入地理位置参数”两种方式上报用户位置，两种方式均可不选，添加位置权限或参数将帮助投放定位广告-->
<!--请注意：无论通过何种方式提供给穿山甲用户地理位置，均需向用户声明地理位置权限将应用于穿山甲广告投放，穿山甲不强制获取地理位置信息-->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

<!-- 如果有视频相关的广告且使用textureView播放，请务必添加，否则黑屏 -->
<uses-permission android:name="android.permission.WAKE_LOCK" />
  
  <!--SDK内⾃定义的权限，与下载相关，aar中已经申请-->
<permission
android:name="${applicationId}.permission.KW_SDK_BROADCAST"
android:protectionLevel="signature" />
<uses-permission android:name="${applicationId}.permission.KW_SDK_BROADCAST"
/>
```

#### 1.2.2 下载配置

```java
//直客广告
<service android:name="com.boniu.ad.download.UpdateService"
    android:exported="false"
    />
//广点通配置

<!-- 声明SDK所需要的组件 -->
<service
    android:name="com.qq.e.comm.DownloadService"
    android:exported="false" />
<!-- 请开发者注意字母的大小写，ADActivity，而不是AdActivity -->
<activity
    android:name="com.qq.e.ads.ADActivity"
    android:screenOrientation="portrait"
    android:configChanges="keyboard|keyboardHidden|orientation|screenSize"
    android:exported="false"  />

//穿山甲配置
<provider
    android:name="com.bytedance.sdk.openadsdk.TTFileProvider"
    android:authorities="${applicationId}.TTFileProvider"
    android:exported="false"
    android:grantUriPermissions="true">
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/file_paths" />
</provider>
<provider
    android:name="com.bytedance.sdk.openadsdk.multipro.TTMultiProvider"
    android:authorities="${applicationId}.TTMultiProvider"
    android:exported="false" />
```

```java
//file_paths
<?xml version="1.0" encoding="utf-8"?>
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <root-path
        name="root"
        path="" />

    <external-path
        name="external"
        path="." />

    <external-path
        name="root_path"
        path="Android/data/{applicationId}" />//你的包名

    <external-files-path
        name="external_file_path"
        path="." />
    <external-cache-path
        name="external_cache_path"
        path="." />


    <external-cache-path
        name="gdt_sdk_download_path1"
        path="com_qq_e_download" />
    <cache-path
        name="gdt_sdk_download_path2"
        path="com_qq_e_download" />
</paths>

```

### 1.3代码混淆

```java
#穿山甲
-keep class com.bytedance.sdk.openadsdk.** { *; }
-keep public interface com.bytedance.sdk.openadsdk.downloadnew.** {*;}
-keep class com.pgl.sys.ces.* {*;}
#直客
-keep class com.boniu.ad.bean.** { *; }
# 嵌入广点通sdk时必须添加
-keep class com.qq.e.** {
        public protected *;
    }
    -keep class android.support.v4.**{
        public *;
    }
    -keep class android.support.v7.**{
        public *;
    }
-keep class MTT.ThirdAppInfoNew {
        *;
    }
    -keep class com.tencent.** {
        *;
    }

#快手
-keep class org.chromium.** {*;}
-keep class org.chromium.** { *; }
-keep class aegon.chrome.** { *; }
-keep class com.kwai.**{ *; }
-keep class com.kwad.**{ *; }
-keepclasseswithmembernames class * {
native <methods>; }-dontwarn com.kwai.**
-dontwarn com.kwad.**
-dontwarn com.ksad.**
-dontwarn aegon.chrome.**
```

### 1.4初始化sdk

```java
/**
 * 
 * @param context
 * @param packageNmae 包名
 * @param appName appname
 */

SplashConfig.init(app,"com.jisuanqi.xiaodong","计算器");
```

## 2.加载广告

### 2.1加载开屏广告

```java
//frameLayout显示布局
//
new SplashAdManager().ShowSplashAd(this,frameLayout,iStartNext,”广告位”);
private SplashAdManager.IStartNext iStartNext = new SplashAdManager.IStartNext() {
	
	//跳过返回
    @Override
    public void startNext() {
        gotoNext();
    }

//失败返回
    @Override
    public void onerror(String msg) {
        Log.e("MainActivity", "onerror: " + msg );
        gotoNext();
    }
  //广告展示放回 - 一般这个时候显示 底部那个跳转第三方的弹窗
  @Override
        public void onAdShow() {
            llDisanfang.setVisibility(View.VISIBLE);
        }

};
```

### 2.2加载激励视频

```java
new RewardAdManager().ShowSplashAd(MainActivity.this,new RewardAdManager.RewardAdManagerListener() {
    @Override
    public void rewardVideAdComplete() {
        //成功
        Log.e(TAG, "rewardVideAdComplete: " );
    }

    @Override
    public void rewardVideAdClose() {

        Log.e(TAG, "rewardVideAdClose: "  );
        //关闭
    }

    @Override
    public void rewardError(String msg) {
        //错误
        Log.e(TAG, "rewardError: " + msg);
    }
},”广告位”);
```

