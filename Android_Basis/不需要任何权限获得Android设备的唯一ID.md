首先先来看看我们为什么不选用其他的方法：
**User Email**
用户可以更改他们的email （非常不可靠）
API5+需要权限

`<uses-permission android:name="android.permission.GET_ACCOUNTS" />`

 API14+需要权限
 
`<uses-permission android:name="android.permission.READ_CONTACTS" />`

(How to get the Android device’s primary e-mail address)

**用户手机号**

用户可以更改他们的手机号 （非常不可靠）

需要权限`<uses-permission android:name="android.permission.READ_PHONE_STATE" />`


**IMEI**

只有Android手机才有， IMEI号是一串15位的号码，比如像这样 359881030314356

```java
TelephonyManager TelephonyMgr = (TelephonyManager)getSystemService(TELEPHONY_SERVICE);
String szImei = TelephonyMgr.getDeviceId();
``` 

`需要权限<uses-permission android:name="android.permission.READ_PHONE_STATE" />`

通常用户会因为你向他们要了这个权限而给你一个差评，因为他们觉得你就是在窃取他们的隐私，很明显，你就是在收集一些数据


**Android ID**

这个是不靠谱的，因为有时候它是null的，文档中明确说明，如果你恢复了出厂设置，那他就会改变的。而且如果你root了手机，你也可以改变这个ID

`String m_szAndroidID = Secure.getString(getContentResolver(), Secure.ANDROID_ID);` 

它返回的是9774d56d682e549c这样一串东西，倒是不需要什么权限

**WLAN MAC地址**

这也可以得到一个独一无二的ID号，返回的是 00:11:22:33:44:55 。但是当没有wifi的时候，我们是无法获得数据的。

```java
WifiManager wm = (WifiManager)getSystemService(Context.WIFI_SERVICE);
String m_szWLANMAC = wm.getConnectionInfo().getMacAddress();
```

需要权限`android.permission.ACCESS_WIFI_STATE`

**蓝牙MAC地址 **

 市面上大部分的应用不使用蓝牙，如果你的应用根本没用蓝牙，而你却和用户要了蓝牙权限的，那你很可疑。

```java
BluetoothAdapter m_BluetoothAdapter = null; 
m_BluetoothAdapter = BluetoothAdapter.getDefaultAdapter();
String m_szBTMAC = m_BluetoothAdapter.getAddress();
```

需要权限`<uses-permission android:name="android.permission.BLUETOOTH "/>`

最合适的办法：Pseudo-Unique ID

- API >=9:通过“Build.SERIAL”这个属性来保证ID的独一无二。API 9 以上的Android设备目前市场占有率在99.5%
记住：你只在技术上忽略了0.5%的用户，你可以专注于99.5%的用户

- API < 9:我们可以通过读取设备的ROM版本号、厂商名、CPU型号和其他硬件信息来组合出一串15位的号码，这15位号码有可能重复，但是几率太小了，小到可以忽略，况且就算重复了，我们损失的用户最多也只不过是0.5%而已。


```java
String m_szDevIDShort = "35" + 
Build.BOARD.length()%10+ Build.BRAND.length()%10 + 

Build.CPU_ABI.length()%10 + Build.DEVICE.length()%10 + 

Build.DISPLAY.length()%10 + Build.HOST.length()%10 + 

Build.ID.length()%10 + Build.MANUFACTURER.length()%10 + 

Build.MODEL.length()%10 + Build.PRODUCT.length()%10 + 

Build.TAGS.length()%10 + Build.TYPE.length()%10 + 

Build.USER.length()%10 ; //13 位
```

“35”加上后面的13位一共15位，我们可以得到355715565309247这样一串号码，不需要任何的权限，非常方便。
//获得独一无二的

```java
Psuedo IDpublic static String getUniquePsuedoID() {
String serial = null;

String m_szDevIDShort = "35" + 
 Build.BOARD.length()%10+ Build.BRAND.length()%10 + 

 Build.CPU_ABI.length()%10 + Build.DEVICE.length()%10 + 

 Build.DISPLAY.length()%10 + Build.HOST.length()%10 + 

 Build.ID.length()%10 + Build.MANUFACTURER.length()%10 + 

 Build.MODEL.length()%10 + Build.PRODUCT.length()%10 + 

 Build.TAGS.length()%10 + Build.TYPE.length()%10 + 

 Build.USER.length()%10 ; //13 位

try {
serial = android.os.Build.class.getField("SERIAL").get(null).toString();
//API>=9 使用serial号
return new UUID(m_szDevIDShort.hashCode(), serial.hashCode()).toString();
} catch (Exception exception) {
 //serial需要一个初始化
serial = "serial"; // 随便一个初始化
}
//使用硬件信息拼凑出来的15位号码
return new UUID(m_szDevIDShort.hashCode(), serial.hashCode()).toString();
```

最终会得到这样的一串ID：00000000-28ee-3eab-ffff-ffffe9374e72
