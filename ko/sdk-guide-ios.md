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

> PushSDK를 초기화한 후에 토큰을 등록하거나 검색할 수 있습니다.

```
TCPushConfiguration *configuration = [[TCPushConfiguration alloc] initWithAppKey:@"INPUT_YOUR_APPKEY"
                                                                          userId:@"INPUT_USER_ID"];

configuration.channel = @"CHANNEL";                 // 채널 설정 (default:@"default")
configuration.isAgreeNotifications = YES;           // 알림 동의 여부 (default:YES)
configuration.isAgreeAdvertisement = YES;           // 광고성 정보 알림 동의 여부 (default:NO)
configuration.isAgreeNightAdvertisement = YES;      // 야간 광고성 정보 알림 동의 여부 (default:NO)

[TCPushSdk initWithConfiguration:configuration];
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

> PushSDK를 초기화한 후에 요청할 수 있습니다.

```
[TCPushSdk registerWithPushType:TCPushTypeAPNs completionHandler:^(NSError *error) {
    if (error == nil) {
        // Success

    } else {
        // Fail
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

> PushSDK를 초기화한 후에 요청할 수 있습니다.

```
[TCPushSdk queryWithPushType:TCPushTypeAPNs completionHandler:^(TCPushTokenInfo *tokenInfo, NSError *error) {
    if (error == nil) {
        // Success

    } else {
        // Fail
    }
}];
```

#### TokenInfo

> 광고성 정보 알림 동의 관련 프로퍼티(isAgreeAdvertisement, isAgreeNightAdvertisement)는
> 토큰 등록 시 사용자 언어 코드가 한글(ko)인 경우에만 설정된 값을 반환하고,
> 한글이 아닌 다른 언어 코드인 경우에는 YES를 반환합니다.

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

> 푸시 메시지 수신에 대해 사용자 코드 실행을 위한 대리인(delegate)을 설정할 수 있습니다.<br>
> 일반 푸시 메시지는 애플리케이션이 실행 중이지 않을 때는 수신 대리인이 메시지를 전달받을 수 없습니다.<br>
> VoIP 푸시 메시지는 애플리케이션이 실행 중이지 않을 때 백그라운드에서 자동으로 애플리케이션이 실행되어 수신 대리인에 메시지를 전달합니다.

```
@interface AppDelegate () <TCPushDelegate>

@end

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    [TCPushSdk setDelegate:self];

    return YES;
}

@end

- (void)pushSdk:(TCPushSdk *)pushSdk didReceiveAPNsNotificationWithPayload:(NSDictionary *)payload {
    // 일반 푸시 메시지 수신
}

- (void)pushSdk:(TCPushSdk *)pushSdk didReceiveVoIPNotificationWithPayload:(NSDictionary *)payload {
    // VoIP 푸시 메시지 수신
}
```

### 지표 수집

> 클라이언트에서 푸시 메시지 수신 및 알림에 의한 애플리케이션 실행 여부가 서버에 전송됩니다.
> 이 내용은 **통계** 탭에서 확인할 수 있습니다.

#### 수신(Received) 지표

> 수신 지표를 수집하려면 애플리케이션에 Notification Service Extension(iOS 10.0+)을 추가해야 합니다. <br>
**File New > Target > iOS > Notification Service Extension**


```
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

#### 확인(Opened) 지표

> 확인 지표 수집과 전송은 SDK 내부에서 자동으로 진행됩니다.

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
