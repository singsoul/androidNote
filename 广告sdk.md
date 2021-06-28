# 1.sdk导入

### 1.1导入sdk依赖库

```java

 //版本号 1.2.0
    implementation 'com.boniu.ad:gdtsdk:4.370.1240@aar'
    implementation 'com.boniu.ad:csjsdk:3.7.0.2@aar'
    implementation 'com.boniu.ad:kssdk:3.3.13@aar'
    implementation 'com.boniu.ad:adsdk:1.2.0@aar'
//自3400版本开始，穿山甲支持了Android R，引入了Android R的 <queries> 标签,需要对gradle版本进行限制，限制范围为：3.3.3、 3.4.3、 3.5.4、3.6.4、4.0.1 ，开发者根据自身情况酌情升级
  
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
   <external-path name="tt_external_root" path="." />
   <external-path name="tt_external_download" path="Download" />
   <external-files-path name="tt_external_files_download" path="Download" />
   <files-path name="tt_internal_file_download" path="Download" />
   <cache-path name="tt_internal_cache_download" path="Download" />


    <external-cache-path
        name="gdt_sdk_download_path1"
        path="com_qq_e_download" />
    <cache-path
        name="gdt_sdk_download_path2"
        path="com_qq_e_download" />
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


</paths>

```

### 1.3代码混淆

```java
#穿山甲
-keep class com.bytedance.sdk.openadsdk.** { *; }
-keep public interface com.bytedance.sdk.openadsdk.downloadnew.** {*;}
-keep class com.pgl.sys.ces.** {*;}
-keep class com.bytedance.embed_dr.** {*;}
-keep class com.bytedance.embedapplog.** {*;}
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

