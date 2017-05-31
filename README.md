# NKBaseSDK（Unity版）接入文档(ver1.0.0_20170106)
NKBaseSDKForUnity集成了Android和iOS端的资源，可直接接入Unity

------
## 1、SDK说明
当前SDK是Unity版本，NKBaseSDK集成了Android端的微信、支付宝、银联支付，目前包含Android和iOS资源，导入Assets下接入即可。
## 2、接入准备
### （1）Android端配置
请务必确认以下参数配置，否则可能导致登录或支付不能正常使用。
#### <1>AndroidManifest设置
在标签中添加权限声明：
```
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.SET_DEBUG_APP" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>

    <!-- 定位 -->
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

    <!-- SDK2.1新增获取用户位置信息 -->
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_LOCATION_EXTRA_COMMANDS" />
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.GET_TASKS"/>

    <!-- 支付宝权限 -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```
在Application节点下增加以下Activity:
```
      <activity
            android:name="com.nkgame.SDKActivity"
            android:windowSoftInputMode="adjustPan|stateHidden"
            android:configChanges="orientation|screenSize|keyboardHidden"
            android:theme="@android:style/Theme.Translucent.NoTitleBar"
            >
        </activity>
        <activity
            android:name="com.nkgame.SDKPayActivity"
            android:windowSoftInputMode="adjustPan|stateHidden"
            android:configChanges="orientation|screenSize|keyboardHidden"
            android:screenOrientation="portrait"
            android:theme="@android:style/Theme.Translucent.NoTitleBar"
            >
        </activity>
        <!-- alipay sdk begin -->
        <activity
            android:name="com.alipay.sdk.app.H5PayActivity"
            android:configChanges="orientation|keyboardHidden|navigation|screenSize"
            android:exported="false"
            android:screenOrientation="behind"
            android:windowSoftInputMode="adjustResize|stateHidden" >
        </activity>
        <activity
            android:name="com.alipay.sdk.app.H5AuthActivity"
            android:configChanges="orientation|keyboardHidden|navigation"
            android:exported="false"
            android:screenOrientation="behind"
            android:windowSoftInputMode="adjustResize|stateHidden" >
        </activity>
```
##### ③Activity
修改启动的Activity为游戏的Activity,其他属性按游戏需求修改：
```
<activity android:configChanges="locale|fontScale|keyboard|keyboardHidden|mcc|mnc|navigation|orientation|screenLayout|screenSize|smallestScreenSize|touchscreen|uiMode" android:label="@string/app_name" android:launchMode="singleTask" android:name="com.unity3d.player.UnityPlayerActivity" android:screenOrientation="fullSensor">
<intent-filter>
<action android:name="android.intent.action.MAIN"/>
<category android:name="android.intent.category.LAUNCHER"/>
<category android:name="android.intent.category.LEANBACK_LAUNCHER"/>
</intent-filter>
<meta-data android:name="unityplayer.UnityActivity" android:value="true"/>
</activity>
```
微信相关
修改src下的WXEntryActivity的包名为游戏的包名：
```
<activity
android:name="YourAppName.wxapi.WXEntryActivity"
android:exported="true"
android:label="@string/app_name"
android:launchMode="singleTop"
android:theme="@android:style/Theme.Translucent" />
<receiver
android:name="YourAppName.AppRegister"
android:permission="com.tencent.mm.plugin.permission.SEND" >
<intent-filter>
<action android:name="com.tencent.mm.plugin.openapi.Intent.ACTION_REFRESH_WXAPP" />
</intent-filter>
</receiver>
```
### （2）iOS端配置
##### <1>.导入
CFNetwork.framework, 
SystemConfiguration.framework,
libstd++.6.0.9.tbd, 
CoreTelephony.framework,
libz.dylib,
libsqlite3.0.dylib,
StoreKit.framework

##### <2>.加入nksdkres.bundle,nkconfig.plist

##### <3>.iOS 9系统策略更新，限制了http协议的访问，此外应用需要在“Info.plist”中将要使用的URL Schemes 列为白名单， 才可正常检查其他应用是否安装。
受此影响，当你的应用在iOS 9中需要使用微信SDK的相关能力（分享、收藏、支付、登录等）时，需要在“Info.plist”里增加如下代码：
```
<key>LSApplicationQueriesSchemes</key>
<array>
<string>weixin</string>
</array>
<key>NSAppTransportSecurity</key>
<dict>
<key>NSAllowsArbitraryLoads</key>
<true/>
</dict>
```
##### <4>.AppDelegate类中加上[WXApi handleOpenURL:url delegate:[WXApiManager sharedManager]];
示例如下：
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation {
return [WXApi handleOpenURL:url delegate:[WXApiManager sharedManager]];
}
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString*, id> *)options{
return [WXApi handleOpenURL:url delegate:[WXApiManager sharedManager]];
}

##### <5>.在nkconfig.plist文件总加上微信APPID。wxappid：

##### <6>.build settings -> linking -> Other Linker Flags 中增加： 
-ObjC -l"BoringSSL" -l"NKSDKProtos" -l"Protobuf" -l"gRPC" -l"gRPC-Core" -l"gRPC-ProtoRPC" -l"gRPC-RxLibrary" -l"z" -framework NKBase -framework SDKLibNK


## 3、SDK接口描述
所有的接口均集成在NKBaseSDK中，方法均为静态方法，可直接通过类名调用。
### （1） 初始化
```
public static void init(AndroidJavaObject activity, String gameID, String gameName, bool isLandscape, NKListener listener)
```
说明：

| 参数        | 类型   |  说明  |适用平台
| --------   | -----:  | :----:  |:----:  |
| activity     | AndroidJavaObject |   Android启动时的Activity    | Android
| gameID       |   String   |   游戏id   | Android/iOS
| gameName     | String |   游戏名称    |  Android/iOS
| isLandscape     | bool |   是否横屏    | Android/iOS
| listener     | NKListener |   全局监听器    |Android
使用示例：
```
NKBaseSDK.init(currentActivity, "NIK-ZT-0009", "Test", false, new NKListener());
```
### NKListener(Android 监听器）
NKListener对接Android NKBaseSDK中的NKListener，可以实现初始化、登录、登出、退出等操作时消息的相互传递，对于信息的处理，请直接在NKListener相应的方法内实现（切勿更改类名、方法名称）。
json object仅当errorcode 为0时才保证有效，如果errorcode不为0，请直接忽略json object
#### <1>onInit
调用初始化接口后收到此回调 参数说明： errorcode 为0表示成功，-1表示未知错误, json 格式如下
{"result":0}
#### <2>onLogin
调用登录接口后收到此回调 参数说明： errorcode 为0表示成功，-1表示未知错误,1表示用户取消（用户取消后请自行再次调用login或者等待用户再次点击登录按钮调用Login） uuid字段为拟酷平台转换后用来验证token的用户id，extra字段下的uuid字段为此渠道自身的用户id，如有疑问，请联系技术解释 extra字段下的loginType中的值表示此次应用宝登录的类型，分别为wechat和qq，全小写 json 格式如下
```
{"result":0,"uuid":"123456789","token":"asdfzxcvasdfqwer","extra":     {"uuid":"b95de17361f01369b2c3534176928b62","loginType":"wechat"}}
```
#### <3>onLogout
调用登出接口之后收到此回调 参数说明： errorcode 为0表示成功，-1表示未知错误 json 格式如下

{"result":0}
#### <4>onPay

调用pay之后收到此回调 参数说明： errorcode 为0表示成功，-1表示未知错误,1表示用户取消 json 格式如下

{"result":0,"orderID":"nk12345678"}
#### <5>onQuit

调用登出接口之后收到此回调 参数说明： errorcode 为0表示成功，-1表示未知错误 json 格式如下

{"result":0}
### （2） 登录
```
public static void login(String lineID, String lineName)
```
说明：

| 参数        | 类型   |  说明  |适用平台
| --------   | -----:  | :----:  |:----:  |
| lineID     | String |   服务器id    | Android/iOS
| lineName     | String |   服务器名称    | Android/iOS
此处传入的lineID、lineName可在selectLine中修改
使用示例：
```
NKBaseSDKForAndroid.login("Line1", "游戏1区");
```
### （3） 选择服务器
```
public static void selectLine(String lineID, String lineName)
```
说明：

| 参数        | 类型   |  说明  |适用平台
| --------   | -----:  | :----:  |:----:  |
| lineID     | String |   服务器id    | Android/iOS
| lineName     | String |   服务器名称    | Android/iOS
### （4） 创建角色
```
public static void createRole(String roleID, String roleName, String roleLevel, String lineID, String lineName, String guildName, long roleCT)
```
说明：

| 参数        | 类型   |  说明  |适用平台
| --------   | -----:  | :----:  |:----:  |
| roleID     | String |   角色id    | Android
| roleName     | String |   角色名称   | Android
| roleLevel     | String |   角色级别   | Android
| lineID     | String |   服务器id    | Android
| lineName     | String |   服务器名称    | Android
| guildName     | String |   公会名称/帮派    | Android
| long roleCT     | long |   角色创建时间    | Android

### （5） 角色登录
```
public static void roleLoggedIn(String roleID, String roleName, String roleLevel, String lineID, String lineName, String guildName, long roleCT)
```
说明：参数见创建角色
### （6） 角色升级
```
public static void roleLevelup(String roleID, String roleName, String roleLevel, String lineID, String lineName, String guildName, long roleCT)
```
说明：参数见创建角色
### （7）进入游戏
```
public static void enterGame()
```
### （8）支付/充值
```
public static void pay(int amount,int exchangeRatio,String moneyName,String extra,String productId,String productName)
```
说明：

| 参数        | 类型   |  说明  |适用平台
| --------   | -----:  | :----:  |:----:  |
| amount     | int |   商品价格，单位为分    | Android/iOS
| exchangeRatio     | int |   价格与游戏货币的兑换比例，1元对应多少游戏货币   | Android/iOS
| moneyName     | String |   游戏货币的名称   | Android/iOS
| extra     | String |   游戏cp设置的充值透传参数   | Android/iOS
| productId     | String |   购买的产品id（如果有的话，没有可以不用设置）    | Android/iOS
| productName     | String |   购买的产品名称    | Android/iOS
使用示例：
```
NKBaseSDK.pay(100, 100, "宝石", "123456", "1234566", "金币");
```
### （9）登出账号
```
public static void logout()
```
### （10）切换账号
```
public static void switchAccount(String lineID, String lineName)
```
说明：

| 参数        | 类型   |  说明  |适用平台
| --------   | -----:  | :----:  |:----:  |
| lineID     | String |   服务器id    | Android/iOS
| lineName     | String |   服务器名称    | Android/iOS
### （11）用户中心
```
public static void userCenter()
```
### （12）退出游戏
```
public static void quit()
```
## 4、其他接口
### （1）获取渠道版本号
```
public static String getVersion()
```
说明： 返回SDK的版本号（单独接入拟酷渠道时返回拟酷SDK版本号，接入拟酷平台打包全部渠道时返回各自渠道版本号）
### （2）获取渠道ID
```
public static int getPlatformID()
```
说明： 返回SDK的渠道标识ID（单独接入拟酷渠道时无需使用此方法，接入拟酷平台打包全部渠道时有用）
### （3）渠道是否有用户中心功能
```
public static bool hasUserCenter()
```
说明：返回渠道是否有用户中心功能（单独接入拟酷渠道时无需使用此方法，接入拟酷平台打包全部渠道时有用）
### （4）渠道是否有退出界面功能
```
public static bool hasQuitPanel()
```
说明: 返回渠道是否有退出界面功能（单独接入拟酷渠道时无需使用此方法，接入拟酷平台打包全部渠道时有用）

## 4、生命周期（Android）
```
public static void onCreate()

public static void onStart()

public static void onResume()

public static void onPause()

public static void onStop()

public static void onNewIntent()

public static void onActivityResult()
```
