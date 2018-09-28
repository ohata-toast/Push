## Notification > Push > Android SDK Guide
With TOAST Cloud Push SDK, mobile applications and TOAST Cloud Push can be easily integrated.

## Main Features
* Register notification tokens to OS
* Receive and display notification messages
* Receive messages and collect application execution indicators through them

## Downloads
Download file to click **Android SDK (AAR)** under **Notification > Push** from [TOAST Document](http://docs.toast.com/ko/Download/).

## Supporting Environment
#### Version
* 15 (4.0.3) or higher API level

#### Supportive Platforms
* Google Cloud Messaging, or GCM
* Tencent Mobile Push, or Tencent
* Amazon Device Messaging, or ADM

## Common Project Setting  
* Download and Add SDK (AAR)
    * Create libs folder under project, if there's none.
    * Add the downloaded AAR file to the libs folder
* Add SDK to build.gradle
    * Add the following to dependencies
```
dependencies {
    compile fileTree(dir: 'libs', include: ['*.aar'])
    compile 'com.android.support:appcompat-v7:26.1.0'
    compile 'com.android.support:support-v4:26.1.0'
}
```

## For GCM   
### Project Setting  
* Add GCM SDK to build.gradle
    * Add the following to dependencies
```groovy
dependencies {
    compile 'com.google.android.gms:play-services-gcm:11.0.0'
}
```

### Modify AndroidManifest.xml   
* Change all parts of '[YOUR_PACKAGE_NAME]' below to default application package name.  

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="[YOUR_PACKAGE_NAME]">
<application android:icon="@drawable/app_icon">
        <receiver android:name="com.google.android.gms.gcm.GcmReceiver" android:exported="true"
                android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="[YOUR_PACKAGE_NAME]" />
            </intent-filter>
        </receiver>
    <service android:name="com.toast.android.pushsdk.PushSdk$GcmListener" android:exported="false">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
    </intent-filter>
    </service>
        <service android:name="com.toast.android.pushsdk.PushService$IdListener" android:exported="false">
            <intent-filter>
                <action android:name="com.google.android.gms.iid.InstanceID"/>
            </intent-filter>
        </service>
</application>
<permission android:name="[YOUR_PACKAGE_NAME].permission.C2D_MESSAGE" android:protectionLevel="signature"/>
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
<uses-permission android:name="[YOUR_PACKAGE_NAME].permission.C2D_MESSAGE" />
</manifest>
```

## For Tencent  
### Project Setting  
This guide has been written for Tencent SDK 3.2.3.

* Download Tencent SDK from Tencent SDK download page, [腾讯移动推送 | 信鸽](http://xg.qq.com/xg/ctr_index/download).
* Decompress downloaded SDK and add the files as below to the libs folder under the project:
    * Required
        * libs/jg_filter_sdk_1.1.jar
        * libs/mid-core-sdk-4.0.6.jar
        * libs/wup-1.0.0.E-SNAPSHOT.jar
        * libs/Xg_sdk_v3.2.3_20180403_1839.jar
        * The whole libs/armeabi folder  
    * Optional: To use another architecture
        * Other-Platform-SO/{Folder named after architecture}
* Modify build.gradle
```groovy
android {
    sourceSets {
        main {
            jniLibs.srcDirs = ['libs']
        }
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
}
```

### Modify AndroidManifest.xml
* Change all part of '[YOUR_PACKAGE_NAME]' below to default application package name.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="[YOUR_PACKAGE_NAME]">
  <application android:icon="@drawable/app_icon">
    <receiver android:name="com.tencent.android.tpush.XGPushReceiver" android:process=":xg_service_v3" >
      <intent-filter android:priority="0x7fffffff" >
        <action android:name="com.tencent.android.tpush.action.SDK" />
        <action android:name="com.tencent.android.tpush.action.INTERNAL_PUSH_MESSAGE" />
        <action android:name="android.intent.action.USER_PRESENT" />
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
      </intent-filter>
    </receiver>
    <service android:name="com.tencent.android.tpush.service.XGPushServiceV3" android:exported="true" android:persistent="true" android:process=":xg_service_v3">
    </service>
    <receiver android:name="com.toast.android.pushsdk.PushSdk$XgListener">
      <intent-filter>
        <action android:name="com.tencent.android.tpush.action.PUSH_MESSAGE" />
        <action android:name="com.tencent.android.tpush.action.FEEDBACK" />
      </intent-filter>
    </receiver>
    <service android:name="com.tencent.android.tpush.rpc.XGRemoteService" android:exported="true" >
        <intent-filter>
            <action android:name="[YOUR_PACKAGE_NAME].PUSH_ACTION" />
        </intent-filter>
    </service>
    <provider
    android:name="com.tencent.android.tpush.XGPushProvider"
    android:authorities="[YOUR_PACKAGE_NAME].AUTH_XGPUSH"
    android:exported="true"/>
    <provider
        android:name="com.tencent.android.tpush.SettingsContentProvider"
        android:authorities="[YOUR_PACKAGE_NAME].TPUSH_PROVIDER"
        android:exported="false" />
    <provider
        android:name="com.tencent.mid.api.MidProvider"
        android:authorities="[YOUR_PACKAGE_NAME].TENCENT.MID.V3"
        android:exported="true" >
    </provider>
  </application>
  <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
  <uses-permission android:name="android.permission.BROADCAST_STICKY" />
  <uses-permission android:name="android.permission.KILL_BACKGROUND_PROCESSES" />
  <uses-permission android:name="android.permission.GET_TASKS" />
  <!--permissions requiring user alerts-->
  <uses-permission android:name="android.permission.READ_PHONE_STATE" />
  <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
  <uses-permission android:name="android.permission.RECEIVE_USER_PRESENT" />
  <uses-permission android:name="android.permission.RESTART_PACKAGES" />
  <uses-permission android:name="android.permission.WRITE_SETTINGS" />
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  <uses-permission android:name="android.permission.READ_LOGS" />

  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="android.permission.WAKE_LOCK" />
  <uses-permission android:name="android.permission.VIBRATE" />
</manifest>
```

## For ADM
### Project Setting
* Amazon Device Messaging, or ADM, is available on Fire OS 2 or newer devices.
* Download Amazon Device Messaging SDK from below:
  - [Download Amazon Developer SDKs](https://developer.amazon.com/sdk-download).
* Decompress downloaded SDK and add the amazon-device-messaging-*.jar file to the libs folder under project.
* Modify build.gradle.
```groovy
dependencies {
    compileOnly files('libs/amazon-device-messaging-1.0.1.jar')
}
```

### Modify AndroidManifest.xml
* Change all parts of '[YOUR_PACKAGE_NAME]' below to default application package name.

#### Add Namespace
```xml
<manifest xmlns:amazon="http://schemas.amazon.com/apk/res/android">
```

#### Add Authorities
```xml
<permission
    android:name="[YOUR_PACKAGE_NAME].permission.RECEIVE_ADM_MESSAGE"
    android:protectionLevel="signature" />
<uses-permission android:name="[YOUR_PACKAGE_NAME].permission.RECEIVE_ADM_MESSAGE" />
<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
```

#### Add Handler and Receiver
- Enter Handler class of user writing for [YOUR_HANDLER_CLASS].
- Enter Receiver class of user writing for [YOUR_RECEIVER_CLASS].
    - Implement Handler and Receiver, in reference of the below:
```xml
<amazon:enable-feature android:name="com.amazon.device.messaging" android:required="true"/>
<service
    android:name="[YOUR_HANDLER_CLASS]"
    android:exported="false" />

<receiver
    android:name="[YOUR_RECEIVER_CLASS]"
    android:permission="com.amazon.device.messaging.permission.SEND" >
    <intent-filter>
        <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
        <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

        <category android:name="[YOUR_PACKAGE_NAME]" />
    </intent-filter>
</receiver>
```

### Implement Handler and Receiver
- When a notification requires title and body only, default Handler and Receiver can be used.
    - Default Handler : com.toast.android.pushsdk.listener.DefaultPushSdkADMHandler
    - Default Receiver : com.toast.android.pushsdk.listener.DefaultPushSdkADMReceiver
- To apply User Handler, it is required to inherit the com.toast.android.pushsdk.listener.AbstractPushSdkADMHandler class.
```java
public class CustomADMHandler extends AbstractPushSdkADMHandler {
    public CustomADMHandler() {
        super(CustomADMHandler.class);
    }

    @Override
    protected void onMessage(Intent intent) {
        Bundle extras = intent.getExtras();
        String value = extras.getString("key");
        // Display notifications by using received data
    }
}
```
- To apply User Receiver, it is required to inherit the com.amazon.device.messaging.ADMMessageReceiver class.
```java
public class CustomADMReceiver extends ADMMessageReceiver {
    public CustomADMReceiver() {
        super(CustomADMHandler.class); // Must enter User Handler class.
    }
}
```

## PushParams
### What is PushParams?
* Refers to an object required to register and query tokens.
* Can be created through the PushParams.Builder class.
* PushParams includes information as follows:

| Property                | Description                                    | Required | Default              |
| ----------------------- | ---------------------------------------------- | -------- | -------------------- |
| appKey                  | Push service key                               | Required | N/A                  |
| userId                  | User identifier                                | Required | N/A                  |
| context                 | Conext                                         | Required | N/A                  |
| pushType                | Push type (GCM or Tencent)                     | Required | N/A                  |
| channel                 | Channel                                        | Optional | Default              |
| country                 | Country code                                   | Optional | System country code  |
| language                | Language code                                  | Optional | System language code |
| isNotificationAgreement | Consent to display notifications               | Optional | False                |
| isAdAgreement           | Consent to display ad notifications            | Optional | False                |
| isNightAdAgreement      | Consent to display night-time ad notifications | Optional | False                |

#### Create PushParams
* Refers to an object required to register and query tokens.
* Can be created through the PushParams.Builder class.
* Example
```java
PushType pushType = null;
if (isGCM) {
    pushType = PushType.gcm("[YOUR_SENDER_ID]");
} else if (isTencent) {
    long yourAccessId = 1234567890L; // [YOUR_ACCESS_ID]
    pushType = PushType.tencent(yourAccessId, "[YOUR_ACCESS_KEY]");
} else if (isADM) {
    pushType = PushType.adm();
}

PushParams.Builder builder = new PushParams.Builder(this, "[YOUR_APP_KEY]", "[YOUR_USER_ID]", pushType);

builder.setChannel("default"); // Selected value
builder.setNotificationAgreement(true); // Selected value
builder.setAdAgreement(true); // Selected value
builder.setNightAdAgreement(true); // Selected value
builder.setCountry("KR"); // Selected value
builder.setLanguage("ko"); // Selected value

PushParams pushParams = builder.build();
```

## Register Tokens
* Create tokens depending on the push type and register to servers
* Example  
```java
PushSdk.register(pushParams, new PushRegisterCallback() {
    @Override
    public void onRegister(PushRegisterResult result) {
        if (result.isSuccessful()) {
            // Successful in token registration
        } else {
            // Failed in token registration
            Log.e(TAG, "error,code=" + result.getCode() + ",message=" + result.getMessage());
        }
    }
});
```

> **Exceptions for Tencent**
>
> Tencent requires WRITE_SETTINGS authority, for which API level 23 (6.0) displays a dialogue box. Even if authority is allowed in the dialogue box, ERROR_PERMISSION_REQUIRED is returned as callback. In this case, call a token registration again and it is normally registered.  

## Query Token Information
* Token information saved in the current server is returned as callback under the PushQueryResult object.
* Example
```java
PushSdk.query(pushParams, new PushQueryCallback() {
    @Override
    public void onQuery(PushQueryResult result) {
        if (result.isSuccessful()) {
            // Successful in token information query
            TokenInfo tokenInfo = result.getTokenInfo();
        } else {
            // Failed in token information query
            Log.e(TAG, "error,code=" + result.getCode() + ",message=" + result.getMessage());
        }
    }
});
```

## Collect Indicators
### Received  
* Automatically collect Received Indicators, when using default receiver provided by SDK.  
* Add the following method to receiver to collect indicators, when user implements the receiver.
* Example
```java
public static class CustomPushReceiver extends GcmListenerService {
    @Override
    public void onMessageReceived(String from, Bundle data) {
        PushAnalytics.onReceived(this, data);
        // Execute notification registration logic
    }
}
```

### Opened
* Refers to an execution through notifications on the notification bar.
* Must modify Activity and Push Receiver to collect opened indicators.
* Add the following code either to MainActivity or activities that run with an alarm click.
```java
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        PushAnalytics.onOpened(this, getIntent());
    }

    @Override
    protected void onNewIntent(Intent intent) {
        PushAnalytics.onOpened(this, intent);
    }
}
```
* Add the following code to Push Receiver.
    * No need to add this code, if SDK default receiver is used.
```java
public static class CustomPushReceiver extends GcmListenerService {
    @Override
    public void onMessageReceived(String from, Bundle data) {
        Intent launchIntent = new Intent(context, MainActivity.class);
        Intent intent = PushAnalytics.newIntentForOpenedEvent(launchIntent, bundle);

        // or, Intent intent = PushAnalytics.newIntentForOpenedEvent(context, MainActivity.class, bundle);
    }
}
```

## Enable Debug Logs
* Push SDK provides a method enabling debug logs of SDK.
* <span style="color:#f47141">Must enable debug logs only during development: remove or set false, for a release</span>.
```java
PushSdk.setDebug(true);
```

## Error Codes
* Error codes are defined as @IntDef in the annotation of com.toast.android.pushsdk.annotations.PushResultCode.  

| Error Code                | Description                                        |
| ------------------------- | -------------------------------------------------- |
| ERROR_SYSTEM_FAIL         | Token acquisition has failed due to a system issue |
| ERROR_NETWORK_FAIL        | Request has failed due to a network issue          |
| ERROR_SERVER_FAIL         | Server has returned failure response               |
| ERROR_ALREADY_IN_PROGRESS | Token registration/query is underway               |
| ERROR_INVALID_PARAMETERS  | Parameters are invalid                             |
| ERROR_PERMISSION_REQUIRED | Authority is required (only for Tencent)           |
| ERROR_PARSE_JSON_FAIL     | Server response has not been parsed.               |
