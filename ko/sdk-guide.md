## Notification > Push > SDK v1.5 사용 가이드
TOAST Cloud Push SDK를 적용하면 모바일 애플리케이션과 TOAST Cloud Push를 쉽게 연동할 수 있다.

## 주요기능
* OS에 알림 토큰 등록
* 알림 메세지 수신 및 표시
* 메세지 수신 및 수신된 메세지를 통한 어플리케이션 실행 지표 수집

## 다운로드
[Downloads](http://docs.toast.com/ko/Download/) 페이지에서 Push SDK를  다운로드 받을 수 있다.
```
[DOCUMENTS] > [Download] > [Notification > Push]
```

## Android
### 지원환경
* API 레벨 15(4.0.3) 이상

### 프로젝트 설정
#### 공통
* SDK(AAR) 다운로드 및 추가
    * 프로젝트 폴더 하위에 libs 폴더가 없으면 생성한다.
    * 다운로드 받은 AAR 파일을 프로젝트의 libs 폴더에 추가한다.
* build.gradle에 SDK 추가
    * dependencies에 다음과 같이 추가한다
```
dependencies {
    compile fileTree(dir: 'libs', include: ['*.aar'])
    compile 'com.android.support:appcompat-v7:26.1.0'
    compile 'com.android.support:support-v4:26.1.0'
}
```

#### GCM을 사용하는 경우
* build.gradle에 GCM SDK 추가
    * dependencies에 다음과 같이 추가한다
```groovy
dependencies {
    compile 'com.google.android.gms:play-services-gcm:11.0.0'
}
```

#### Tencent를 사용하는 경우
* (가이드는 Tencent SDK 3.2.3 을 기준으로 작성되었다)
* 아래 페이지에서 Tencent SDK를 다운로드 한다.
    * [Tencent SDK 다운로드 페이지](http://xg.qq.com/xg/ctr_index/download)
* 다운로드받은 SDK의 압축을 해제하고 아래 파일들을 프로젝트 하위의 libs 폴더에 추가한다.
    * 필수
        * libs/jg_filter_sdk_1.1.jar
        * libs/mid-core-sdk-4.0.6.jar
        * libs/wup-1.0.0.E-SNAPSHOT.jar
        * libs/Xg_sdk_v3.2.3_20180403_1839.jar
        * libs/armeabi 폴더 전체
    * 선택 : 다른 아키텍쳐 사용을 원하는 경우
        * Other-Platform-SO/{아키텍쳐 이름의 폴더}
* build.gradle 수정
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

### AndroidManifest.xml 수정
#### GCM을 사용하는 경우
* 아래에 '[YOUR_PACKAGE_NAME]'으로 되어 있는 모든 부분을 애플리케이션 기본 페키지 네임으로 변경한다.

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

#### Tencent를 사용하는 경우
* 아래에 '[YOUR_PACKAGE_NAME]'으로 되어 있는 모든 부분을 애플리케이션 기본 페키지 네임으로 변경한다.

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

### PushParams
#### PushParams 란?
* PushParams는 토큰 등록 및 조회를 위해서 필요한 객체이다.
* PushParams.Builder 클래스를 통해서 객체를 생성할 수 있다.
* PushParams에 포함된 정보는 아래와 같다.

| 프로퍼티 | 설명 | 필수여부 | 기본값 |
|---|---|---|---|
| appKey | Push 서비스키 | 필수 | 없음 |
| userId | 사용자 식별자 | 필수 | 없음 |
| context | 컨텍스트 | 필수 | 없음 |
| pushType | 푸쉬 타입 (GCM, Tencent) | 필수 | 없음 |
| channel | 채널 | 선택 | default |
| country | 국가코드 | 선택 | 시스템 국가코드 |
| language | 언어코드 | 선택 | 시스템 언어코드 |
| isNotificationAgreement | 알림 표시 동의 여부 | 선택 | false |
| isAdAgreement | 광고성 알림 표시 동의 여부 | 선택 | false |
| isNightAdAgreement | 야간 광고성 알림 표시 동의 여부 | 선택 | false |

#### PushParams 생성
* PushParams는 토큰 등록 및 조회를 위해서 필요한 객체이다.
* PushParams.Builder 클래스를 통해서 객체를 생성할 수 있다.
* 예제 코드
```java
PushType pushType = null;
if (isGCM()) {
    pushType = PushType.gcm("[YOUR_SENDER_ID]");
} else if (isTencent) {
    long yourAccessId = 1234567890L; // [YOUR_ACCESS_ID]
    pushType = PushType.tencent(yourAccessId, "[YOUR_ACCESS_KEY]");
}

PushParams.Builder builder = new PushParams.Builder(this, "[YOUR_APP_KEY]", "[YOUR_USER_ID]", pushType);

builder.setChannel("default"); // 선택값
builder.setNotificationAgreement(true); // 선택값
builder.setAdAgreement(true); // 선택값
builder.setNightAdAgreement(true); // 선택값
builder.setCountry("KR"); // 선택값
builder.setLanguage("ko"); // 선택값

PushParams pushParams = builder.build();
```

### 토큰 등록
* 푸시 타입에 따라 토큰을 생성해서 서버에 토큰을 등록한다.
* 예제 코드
```java
PushSdk.register(pushParams, new PushRegisterCallback() {
    @Override
    public void onRegister(PushRegisterResult result) {
        if (result.isSuccessful()) {
            // 토큰 등록 성공
        } else {
            // 토큰 등록 실패
            Log.e(TAG, "error,code=" + result.getCode() + ",message=" + result.getMessage());
        }
    }
});
```

#### Tencent를 사용하는 경우 예외사항
* Tencent는 WRITE_SETTINGS 권한이 필요하며, API 레벨 23(6.0) 에서는 별도의 다이얼로그가 노출된다.
* 설정 다이얼로그에서 권한을 허용하더라도, 콜백으로 ERROR_PERMISSION_REQUIRED 오류가 반환된다.
* 이 경우, 다시 토큰 등록을 호출하면 정상적으로 토큰이 등록된다.

### 토큰 정보 조회
* 현재 서버에 저장된 토큰 정보가 PushQueryResult 객체에 담겨져 콜백으로 반환된다.
* 예제 코드
```java
PushSdk.query(pushParams, new PushQueryCallback() {
    @Override
    public void onQuery(PushQueryResult result) {
        if (result.isSuccessful()) {
            // 토큰 정보 조회 성공
        } else {
            // 토큰 정보 조회 실패
            Log.e(TAG, "error,code=" + result.getCode() + ",message=" + result.getMessage());
        }
    }
});
```

### 지표 수집
#### 수신(Received) 지표
* SDK에서 제공하는 기본 리시버를 사용하는 경우, 수신 지표가 자동으로 수집된다.
* 사용자가 직접 리시버를 구현하는 경우, 수신 지표 수집을 위해서 아래 메소드를 리시버에 추가해야한다.
* 예제 코드
```java
public static class CustomPushReceiver extends GcmListenerService {
    @Override
    public void onMessageReceived(String from, Bundle data) {
        PushAnalytics.onReceived(this, data);
        // 알림 등록 로직 수행
    }
}
```

#### 실행(Opened) 지표
* 알림바의 알림을 통해서 실행했을 경우를 실행 지표라고 한다.
* 실행 지표 수집을 위해서는 액티비티와 푸시 리시버를 수정해야 한다.
* MainActivity 혹은 알림 클릭시 실행되는 액티비티에 다음과 같은 코드를 추가해야한다.
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
* 푸시 리시버에 다음과 같은 코드를 추가해야한다.
    * 이 코드는 SDK에서 제공하는 기본 리시버를 사용하는 경우 추가할 필요가 없다.
```java
public static class CustomPushReceiver extends GcmListenerService {
    @Override
    public void onMessageReceived(String from, Bundle data) {
        Intent launchIntent = new Intent(context, MainActivity.class);
        Intent intent = PushAnalytics.newIntentForOpenedEvent(launchIntent, bundle);
        
        // 혹은 Intent intent = PushAnalytics.newIntentForOpenedEvent(context, MainActivity.class, bundle);
    }
}
```

### 디버그 로그 활성화
* Push SDK는 SDK의 디버그 로그를 활성화하는 메소드를 제공한다.
* <span style="color:#f47141">반드시 개발 중에만 디버그 로그를 활성화해야 한다. 릴리즈시에는 제거하거나 false로 설정해야한다.</span>
```java
PushSdk.setDebug(true);
```

### 오류 코드
* 오류 코드는 com.toast.android.pushsdk.annotations.PushResultCode 어노테이션에 @IntDef 로 정의되어있다.

| 에러코드 | 설명 |
|---|---|
| ERROR_SYSTEM_FAIL | 시스템 문제로 토큰 획득에 실패한 경우 |
| ERROR_NETWORK_FAIL | 네트워크 문제로 인해 요청이 실패한 경우 |
| ERROR_SERVER_FAIL | 서버에서 실패 응답을 반환한 경우 |
| ERROR_ALREADY_IN_PROGRESS | 토큰 등록/조회가 이미 실행중인 경우 |
| ERROR_INVALID_PARAMETERS | 매개변수가 잘못된 경우 |
| ERROR_PERMISSION_REQUIRED | 권한이 필요한 경우 (Tencent Only) |
| ERROR_PARSE_JSON_FAIL | 서버 응답을 파싱하지 못한 경우 |
<br><br>

## iOS
### 지원환경
* iOS 8.0 이상

### 프로젝트 설정
* Capabilities 설정<br/>
![Remote Notifications](http://static.toastoven.net/toastcloud/sdk/push/ios/settings_capabilities_1.png)<br/>
![Push Notifications](http://static.toastoven.net/toastcloud/sdk/push/ios/settings_capabilities_2.png)
* Linked Framework and Libraries 설정<br/>
![Linked Frameworks](http://static.toastoven.net/toastcloud/sdk/push/ios/settings_libraries.png)

### 토큰 등록
>어플리케이션에서 별도로 OS에 토큰 등록 요청을 할 필요가 없다.
```objc
TCPushConfiguration *configuration = [[TCPushConfiguration alloc] initWithAppKey:@"INPUT_YOUR_APPKEY"
                                                                          userId:@"INPUT_USER_ID"];

configuration.pushType = TCPushTypeAPNsSandbox;     // Push 타입 (default:TCPushTypeAPNs)
configuration.channel = @"CHANNEL";                 // 채널 설정 (default:@"default")
configuration.isAgreeNotifications = YES;           // 알림 동의 여부 (default:YES)
configuration.isAgreeAdvertisement = YES;           // 광고성 알림 동의 여부 (default:NO)
configuration.isAgreeNightAdvertisement = YES;      // 야간 광고성 알림 동의 여부 (default:NO)

[TCPushSdk registerWithConfiguration:configuration completionHandler:^(NSError *error) {
    if (error == nil) {
        // Success

    } else {
        // Fail
    }
}];
```

#### Configuration
| 프로퍼티 | 설명 | 기본값 |
| -- | -- | -- |
| appKey | Push 서비스키 | 필수요소 |
| userId | 사용자 식별자 | 필수요소 |
| pushType | 푸쉬 타입 (TCPushTypeAPNs, TCPushTypeAPNsSandbox) | TCPushTypeAPNs |
| channel | 채널 | default |
| isAgreeNotification | 알림 표시 동의 여부 | YES |
| isAgreeAdvertisement | 광고성 알림 표시 동의 여부 | NO |
| isAgreeNightAdvertisement | 야간 광고성 알림 표시 동의 여부 | NO |

### 토큰 정보 조회
```objc
[TCPushSdk queryWithCompletionHandler:^(TCPushTokenInfo *tokenInfo, NSError *error) {
    if (error == nil) {
        // Success

    } else {
        // Fail
    }
}];
```

#### TokenInfo
>광고성 알림 동의 관련 프로퍼티(isAgreeAdvertisement, isAgreeNightAdvertisement)는<br>
>토큰 등록시 사용자 언어 코드가 한글(ko)이었을 경우에만 설정된 값을 반환하고,<br>
>한글이 아닌 다른 언어 코드에 대해서는 YES 로 반환한다.

| 프로퍼티 | 자료형 | 설명 |
| -- | -- | -- |
| userId | NSString | 사용자 식별자 |
| token | NSString | 등록된 토큰 |
| countryCode | NSString | 국가 코드 |
| languageCode | NSString | 언어 코드 |
| pushType | ENUM | 푸쉬 타입 |
| isAgreeNotification | BOOL | 알림 표시 동의 여부 |
| isAgreeAdvertisement | BOOL | 광고성 알림 표시 동의 여부 |
| isAgreeNightAdvertisement | BOOL | 야간 광고성 알림 표시 동의 여부 |
| timezone | NSString | 시각대 |
| updateDate | NSDate | 최종 업데이트 날짜 |

### 지표 수집
>클라이언트에서 푸시 메세지 수신 및 푸시 메세지에 의한 어플리케이션 실행 여부에 대한 정보를 서버에 전송하고,<br>
>이를 웹 콘솔 통계 페이지내에서 확인 가능하다.

#### 수신(Received) 지표
>수신 지표 수집을 위해서는 어플리케이션에 Notification Service Extension(iOS 10.0+)의 추가가 필요하다.
```
[File] > [New] > [Target] > [iOS] > [Notification Service Extension]
```

```objc
#import "NotificationService.h"
#import <TCPushSDK/TCPushSDK.h>

@interface NotificationService ()

@property (nonatomic, strong) void (^contentHandler)(UNNotificationContent *contentToDeliver);
@property (nonatomic, strong) UNMutableNotificationContent *bestAttemptContent;

@end

@implementation NotificationService

- (void)didReceiveNotificationRequest:(UNNotificationRequest *)request withContentHandler:(void (^)(UNNotificationContent * _Nonnull))contentHandler {
    [TCPushSdk setDebugMode:YES];
    
    self.contentHandler = contentHandler;
    self.bestAttemptContent = [request.content mutableCopy];
    
    [TCPushAnalytics didReceiveNotificationWithAppKey:@"INPUT_YOUR_APPKEY"
                                              request:request
                                    completionHandler:^(NSError *error) {
                                        self.contentHandler(self.bestAttemptContent);
                                    }];
}

- (void)serviceExtensionTimeWillExpire {
    self.contentHandler(self.bestAttemptContent);
}

@end
```

#### 실행(Opened) 지표
>실행 지표에 대한 수집 및 전송은 SDK 내부에서 자동으로 진행된다.

### 오류 코드
| 에러코드 | 설명 |
| -- | -- |
| TCPushErrorInvalidParameters | 파라미터 오류 |
| TCPushErrorPermissionDenined | 권한 미획득 |
| TCPushErrorSystemFail | 시스템 알림 등록 실패 |
| TCPushErrorNetworkFail | 네트워크 송수신 실패 |
| TCPushErrorServerFail | 서버 응답 실패 |
| TCPushErrorInvalidUrl | 잘못된 URL 요청 |
| TCPushErrorNetworkNotReachable | 네트워크 미연결 |