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
### Common Setting (JCenter)
#### Add dependency
* Add dependency to build.gradle
```groovy
dependencies {
    implementation 'com.toast.android:pushsdk:1.7.0'
    // compile 'com.toast.android:pushsdk:1.7.0' // (Gradle < 3.4)
}
```

#### When occuping duplicatation of Android Support Library.
* Exclude Support library
```groovy
dependencies {
    implementation('com.toast.android:pushsdk:1.7.0') {
        exclude group: 'com.android.support', module: 'support-v4'
    }
}
```

### Common Setting (Manual)
* Download SDK and configure manually, when you don't use, or can't use JCenter.

#### Download and Add dependency
* Download and Add SDK (AAR)
    * Create libs folder under project, if there's none.
    * Add the downloaded AAR file to the libs folder
* Add SDK to build.gradle
    * Add the following to dependencies
```
dependencies {
    compile fileTree(dir: 'libs', include: ['*.aar'])
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

## SDK Guide
### Define PushParams
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

#### Register Tokens
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

### Query Token Information
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

### Enable Debug Logs
* Push SDK provides a method enabling debug logs of SDK.
* <span style="color:#f47141">Must enable debug logs only during development: remove or set false, for a release</span>.
```java
PushSdk.setDebug(true);
```

### Collect Indicators (GCM Only)
#### Received  
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

#### Opened
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

### Error Codes
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

## Rich Message Guide
- This is guide for rich message feature

### What is Rich Message ?
- You can notify notifications with title, body, and rich message to users.

### Support features and specification
#### Button
- Dismiss : Dismiss current notification.
- Open Application : Open your application.
- Open URL : Browse to URL
    * Can browse Activity/BroadcastReceiver with custom scheme.
- Reply : Reply message immediately in notification
    - Only above API level 24 (Android 7.0)
    - If you convert a notification using NotificationConverter, the reply button is invisiable in a notification under Android 7.0
    - For processing a reply, you must register listener.

> Maximum of butons is 3

> For button feature, you create a notification using *NotificationConverter class*

#### Media
- Image : Add image to notification (Support to internal, external images)
    - Recommend images with a 2 : 1 aspect ratio.
    - Images in different proportions can be exposed with clipping.
- Others : Not support other media(Video, Sounds, etc.)

#### Large icon
- Add large icon to notification (Support to internal, external images)
    - Recommend 1 : 1 aspect ratio.
    - Images in different ratios are forced to change to 1 : 1, which can expose different images than you expect.

#### Group
- Gather notifications with same key
    - Only above API level 24 (Android 7.0)
    - If you convert a notification using NotificationConverter, the reply button is invisiable in a notification under Android 7.0

### Receive rich messages and notify
- If you use default receiver provided by SDK, you can receive and notify notifications without configuration, and additonal codes.
- If you implement custom receiver, Check *ToastPushMessage class, and NotificationReceiver class*.

### Define ToastPushMessage
- Integration data structure for providers of push, GCM, Tencent, ADM.
- Contains rich messages in addition to the subject and content that sent from the TOAST Push server.
- You can convert notification easily, with NotificationConverter.
    - Also you can create notification manually using ToastPushMessage.
- Support input types
    - GCM(Google Cloud Messaging) : Bundle
    - ADM(Amazon Device Messaging) : Bundle
    - Tencent : XGPushTextMessage
- This is data structure of ToastPushMessage
```java
class ToastPushMessage {
    CharSequence title;
    CharSequence body;
    RichMessage richMessage;
    Map<String, String> extras;
}

class RichMessage {
    MediaInfo media;
    LargeIconInfo largeIcon;
    GroupInfo group;
    List<ButtonInfo> buttons;
}
```

### Using NotificationConverter
- Provide NotificationConverter for converting ToastPushMesage to Android Notification.
- NotificationConverter returns a notification object with rich messages.
- You can define additonal feature with Extender.
    - Extenders are processed in the order in which they were added after the internal processing of the NotificationConverter was completed.
- Example)


```java
// Example for GCM
final ToastPushMessage message = ToastPushMessage.fromBundle(bundle);
final NotificationManagerCompat manager = NotificationManagerCompat.from(context);
final NotificationConverter converter = new NotificationConverter("YOUR_NOTIFICATION_CHANNEL", message);
// Extends NotificationCompat.Builder when you need
converter.addExtender(new NotificationCompat.Extender() {
    @Override
    public NotificationCompat.Builder extend(NotificationCompat.Builder builder) {
        // Extends builder
        // ex) return builder.setAutoCancel(false);
        return builder;
    }
});
converter.convert(context, new NotificationConverter.ConvertCallback() {
    @Override
    public void onConvert(int notificationId, @NonNull Notification notification) {
        // Notify notification to notification bar
        manager.notify(notificationId, notification);
    }
});
```

### Register ReplyListener
* Once you receive user input through the Reply button, the app will have to process user input.
     * As a messenger, for example, you need to send user input to the messenger server.

#### Implementing a Reply Listener
* For reply processing, you must implement the listener by inheriting from the ReplyActionListener interface.

> (Note) When the user completes the input and presses the send button, the notification will be exposed and the notification will not be removed.
> Therefore, once your reply has been processed, you'll need to add code to remove or update your notifications.
> Please refer to the example code below.

* Example)
```java
public class ReplyHandler implements ReplyActionListener {
    @Override
    public void onReceive(@NonNull Context context, @NonNull ReplyActionResult result) {
        // Do Something (ex. Send message contents to server)

        // Choice 1. Remove previous reply notification with notification id
        NotificationManagerCompat.from(context).cancel(result.getNotificationId());

        // Choice 2. Update previous reply notification with notification id
        NotificationManagerCompat.from(context).notify(result.getNotificationId(),
                new NotificationCompat.Builder(context, result.getNotificationChannel())
                        .setSmallIcon(/* Resource ID of your icon */ getDefaultIcon(context))
                        .setContentTitle("Send")
                        .setContentText("Success to send message")
                        .build());
    }
}
```

#### Register ReplyListener
* You can register ReplyListener using **PushSdk.setReplyActionListenerClass** method.
* Example)
```java
PushSdk.setReplyActionListenerClass(/* Context */ this, ReplyHandler.class);
```