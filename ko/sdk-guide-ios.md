## Notification > Push > iOS SDK 가이드
Push SDK를 적용하면 모바일 애플리케이션과 Push를 쉽게 연동할 수 있습니다.

## 주요 기능
* OS에 알림 토큰 등록
* 알림 메시지 수신 및 표시
* 메시지 수신 및 수신된 메시지를 통한 애플리케이션 실행 지표 수집

## 다운로드
[TOAST Document](http://docs.toast.com/ko/Download/)에서 **Notification > Push** 아래의 **iOS SDK**를 클릭해 파일을 다운로드합니다.

## 지원 환경
* iOS 8.0 이상

## 프로젝트 설정
### 공통
* Capabilities 설정<br/>
  ![Remote Notifications](http://static.toastoven.net/toastcloud/sdk/push/ios/settings_capabilities_1.png)<br/>
  ![Push Notifications](http://static.toastoven.net/toastcloud/sdk/push/ios/settings_capabilities_2.png)
* Linked Framework and Libraries 설정<br/>
  ![Linked Frameworks](http://static.toastoven.net/toastcloud/sdk/push/ios/settings_libraries.png)

### VoIP
* info.plist 설정
```xml
<key>UIBackgroundModes</key>
<array>
    <string>remote-notification</string>
    <string>voip</string>
</array>
```

* Linked Framework and Libraries 설정<br/>
  ![Linked Frameworks](http://static.toastoven.net/toastcloud/sdk/push/ios/settings_libraries_voip.png)




## SDK 사용 가이드

### 초기화

> PushSDK의 초기화를 해야만 토큰을 등록할 수 있습니다.

```
TCPushConfiguration *configuration = [[TCPushConfiguration alloc] initWithAppKey:@"INPUT_YOUR_APPKEY"
                                                                          userId:@"INPUT_USER_ID"];

configuration.channel = @"CHANNEL";                 // 채널 설정 (default:@"default")
configuration.isAgreeNotifications = YES;           // 알림 동의 여부 (default:YES)
configuration.isAgreeAdvertisement = YES;           // 광고성 정보 알림 동의 여부 (default:NO)
configuration.isAgreeNightAdvertisement = YES;      // 야간 광고성 정보 알림 동의 여부 (default:NO)

// 토큰 설정 초기화
[TCPushSdk initWithConfiguration:configuration];
```

### 카테고리 설정

> 카테고리 설정은 초기화시에만 가능합니다.

```
TCPushConfiguration *configuration = [[TCPushConfiguration alloc] initWithAppKey:@"INPUT_YOUR_APPKEY"
                                                                          userId:@"INPUT_USER_ID"];

configuration.channel = @"CHANNEL";                 // 채널 설정 (default:@"default")
configuration.isAgreeNotifications = YES;           // 알림 동의 여부 (default:YES)
configuration.isAgreeAdvertisement = YES;           // 광고성 정보 알림 동의 여부 (default:NO)
configuration.isAgreeNightAdvertisement = YES;      // 야간 광고성 정보 알림 동의 여부 (default:NO)

// 토큰 설정과 카테고리 함께 초기화
UNNotificationCategory *category = ...;
NSSet *categories = [NSSet setWithObject:category];

[TCPushSdk initWithConfiguration:configuration
                      categories:categories];
```

#### Configuration

| 프로퍼티                  | 설명                                 | 필수 여부 | 기본값  |
| ------------------------- | ------------------------------------ | --------- | ------- |
| appKey                    | Push 서비스 키                       | 필수      | 없음    |
| userId                    | 사용자 식별자                        | 필수      | 없음    |
| channel                   | 채널                                 | 선택      | default |
| isAgreeNotification       | 알림 표시 동의 여부                  | 선택      | YES     |
| isAgreeAdvertisement      | 광고성 정보 알림 표시 동의 여부      | 선택      | NO      |
| isAgreeNightAdvertisement | 야간 광고성 정보 알림 표시 동의 여부 | 선택      | NO      |

### 토큰 등록

> PushSDK를 초기화한 후에 요청할 수 있습니다.<br>
> 시스템에 토큰을 등록하고 해당 토큰을 초기화 설정정보로 토스트 클라우드에 등록합니다.<br>
> 초기화를 하지 않고 토큰 등록 요청시 시스템 토큰만 등록됩니다.

```
[TCPushSdk registerWithPushType:TCPushTypeAPNs completionHandler:^(NSString *token, NSError *error) {
    if (error == nil) {
        // Success

    } else {
        if (token == nil) {
            // Fail

        } else {
            // 시스템 토큰 등록만 성공
        }
    }
}];
```

#### PushType

| 타입                  | 설명                    |
| --------------------- | ----------------------- |
| TCPushTypeAPNs        | 일반 푸시 메시지        |
| TCPushTypeAPNsSandbox | 개발용 일반 푸시 메시지 |
| TCPushTypeVoIP        | VoIP 푸시 메시지        |
| TCPushTypeVoIPSandbox | 개발용 VoIP 푸시 메시지 |

### 토큰 정보 조회

> 현재 단말기에서 가장 최근에 등록된 토큰과 설정정보를 조회 합니다.

```
// 토큰 정보 조회
[TCPushSdk queryWithPushType:TCPushTypeAPNs completionHandler:^(TCPushTokenInfo *tokenInfo, NSError *error) {
    if (error == nil) {
        // Success

    } else {
        // Fail
    }
}];

// 토큰 조회
NSString *token = [TCPushSdk deviceTokenForPushType:TCPushTypeAPNs];
```

#### TokenInfo

> 광고성 정보 알림 동의 관련 프로퍼티(isAgreeAdvertisement, isAgreeNightAdvertisement)는<br>
> 토큰 등록 시 사용자 언어 코드가 한글(ko)인 경우에만 설정된 값을 반환하고,<br>
> 한글이 아닌 언어 코드인 경우에는 무조건 YES를 반환합니다.

| 프로퍼티                  | 자료형   | 설명                                 |
| ------------------------- | -------- | ------------------------------------ |
| userId                    | NSString | 사용자 식별자                        |
| token                     | NSString | 등록된 토큰                          |
| countryCode               | NSString | 국가 코드                            |
| languageCode              | NSString | 언어 코드                            |
| pushType                  | ENUM     | 푸시 타입                            |
| isAgreeNotification       | BOOL     | 알림 표시 동의 여부                  |
| isAgreeAdvertisement      | BOOL     | 광고성 정보 알림 표시 동의 여부      |
| isAgreeNightAdvertisement | BOOL     | 야간 광고성 정보 알림 표시 동의 여부 |
| timezone                  | NSString | 시각대                               |
| updateDate                | NSDate   | 최종 업데이트 날짜                   |

### 푸시 수신

> 푸시 메시지 수신에 대해 사용자 코드 실행을 위한 Delegate를 설정할 수 있습니다.<br>
> 일반 푸시 메시지(TCPushTypeAPNs)의 경우 애플리케이션이 실행 중이지 않을 때는 수신 Delegate로 메시지를 전달받을 수 없습니다.<br>
> VoIP 푸시 메시지(TCPushTypeVoIP)는 애플리케이션이 실행 중이지 않을 때 백그라운드에서 자동으로 애플리케이션이 실행되어 수신 Delegate로 메시지를 전달합니다.

```
@interface AppDelegate () <TCPushDelegate>

@end

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    [TCPushSdk setDelegate:self];

    return YES;
}

@end

// 일반 푸시 메시지 수신
- (void)pushSdk:(TCPushSdk *)pushSdk didReceiveAPNsNotificationWithPayload:(NSDictionary *)payload {
    
}

// VoIP 푸시 메시지 수신
- (void)pushSdk:(TCPushSdk *)pushSdk didReceiveVoIPNotificationWithPayload:(NSDictionary *)payload {
    
}
```

### 푸쉬 액션(Action) 수신

>  사용자 정의 카테고리로 수신한 메세지의 액션을 수신합니다.

```
// 텍스트 입력을 제외한 다른 액션 수신
- (void)pushSdk:(TCPushSdk *)pushSdk
   handleAction:(NSString *)actionIdentifier
       category:(NSString *)categoryIdentifier
        payload:(NSDictionary *)payload {

}

// 텍스트 입력 액션 수신
- (void)pushSdk:(TCPushSdk *)pushSdk
   handleAction:(NSString *)actionIdentifier
       category:(NSString *)categoryIdentifier
        payload:(NSDictionary *)payload
       userText:(NSString *)userText {
 
}
```

### 리치 메세지 수신
> 수신 지표를 수집하려면 애플리케이션에 Notification Service Extension(iOS 10.0+)을 추가해야 합니다. <br>
> Notification Service Extension을 생성하고 TCPushServiceExtension을 확장 구현해야 리치 메세지가 자동으로 생성됩니다. <br>
> **File New > Target > iOS > Notification Service Extension** <br>
> 리치 메세지는 카테고리 기반으로 동작하기 때문에 사용자 카테고리랑 중복하여 사용할 수 없습니다.


```
// Notification Service Extension 생성할때 함께 생성되는 NotificationService 클래스에
// TCPushSDK를 추가하고 TCPushServiceExtension을 확장 구현

#import <UserNotifications/UserNotifications.h>
#import <TCPushSDK/TCPushSDK.h>

@interface NotificationService : TCPushServiceExtension

@end
```

#### 리치 메세지 버튼 액션 수신
 > 사용자 추가 카테고리 액션 수신과 동일하게 Delegate를 통해 액션과 메세지를 전달받을 수 있습니다.

```
// 텍스트 입력을 제외한 다른 액션 수신
- (void)pushSdk:(TCPushSdk *)pushSdk
   handleAction:(NSString *)actionIdentifier
       category:(NSString *)categoryIdentifier
        payload:(NSDictionary *)payload {

}

// 텍스트 입력 액션 수신
- (void)pushSdk:(TCPushSdk *)pushSdk
   handleAction:(NSString *)actionIdentifier
       category:(NSString *)categoryIdentifier
        payload:(NSDictionary *)payload
       userText:(NSString *)userText {
 
}
```

### 지표 수집

> 클라이언트에서 푸시 메시지 수신 및 알림에 의한 애플리케이션 실행 여부가 서버에 전송됩니다.
> 이 내용은 **통계** 탭에서 확인할 수 있습니다.

#### 수신(Received) 지표

> 수신 지표를 수집하려면 애플리케이션에 Notification Service Extension(iOS 10.0+)을 추가해야 합니다. <br>
> Extension은 서비스앱이 설치될때 함께 설치되지만 서비는 앱과는 분리되어 있는 별도의 컨테이너(Container) 입니다. <br>
> **File New > Target > iOS > Notification Service Extension**

##### 지표 전송 정보 설정

> Notification Service Extension 타겟의 info.plist 파일 내부에 앱키를 입력하시면 수신 지표 수집과 전송이 자동으로 이루어집니다.

* Property List<br>
![Remote Notifications](http://static.toastoven.net/toastcloud/sdk/push/ios/analytics_settings_extension.png)<br/>

* Source Code
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>TCPushAnalytics</key>
    <dict>
        <key>AppKey</key>
        <string>INPUT_YOUR_APPKEY</string>
    </dic>
</dict>
</plist>
```

##### Notification Service Extension 추가

> Notification Service Extension을 생성하고 TCPushServiceExtension을 확장 구현해야 수신 지표의 수집과 전송이 자동으로 이루어집니다.

```
// Notification Service Extension 생성할때 함께 생성되는 NotificationService 클래스에
// TCPushSDK를 추가하고 TCPushServiceExtension을 확장 구현

#import <UserNotifications/UserNotifications.h>
#import <TCPushSDK/TCPushSDK.h>

@interface NotificationService : TCPushServiceExtension

@end
```

#### 확인(Opened) 지표

> 확인 지표 수집과 전송은 SDK 내부에서 자동으로 진행됩니다. <br>
> 초기화가 진행되어야 확인 지표 전송이 가능합니다. <br>
> SDK 초기화를 하지않고 사용하는 사용자의 경우 서비스앱의 info.plist 파일 **(Notification Service Extension이 아닌 서비스앱의 plist 파일)** 내부에 앱키를 입력하시면 초기화를 하지 않아도 확인 지표의 수집과 전송이 자동으로 이루어 집니다.

* Property List<br>
![Remote Notifications](http://static.toastoven.net/toastcloud/sdk/push/ios/analytics_settings_app.png)<br/>

* Source Code
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>TCPushAnalytics</key>
    <dict>
        <key>AppKey</key>
        <string>INPUT_YOUR_APPKEY</string>
    </dic>
</dict>
</plist>
```

### 오류 코드

| 오류 코드                       | 설명                  |
| ------------------------------ | --------------------- |
| TCPushErrorNotInitialized      | 초기화되지 않음      |
| TCPushErrorInvalidParameters   | 파라미터 오류         |
| TCPushErrorPermissionDenined   | 권한 미획득           |
| TCPushErrorSystemFail          | 시스템 알림 등록 실패 |
| TCPushErrorNetworkFail         | 네트워크 송수신 실패  |
| TCPushErrorServerFail          | 서버 응답 실패        |
| TCPushErrorInvalidUrl          | 잘못된 URL 요청       |
| TCPushErrorNetworkNotReachable | 네트워크 미연결       |
