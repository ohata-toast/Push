## Notification > Push > iOS SDK 사용 가이드
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

## 지원환경
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
>PushSDK 초기화 수행 후에 토큰 등록, 조회 기능의 사용이 가능하다.

```objc
TCPushConfiguration *configuration = [[TCPushConfiguration alloc] initWithAppKey:@"INPUT_YOUR_APPKEY"
                                                                          userId:@"INPUT_USER_ID"];

configuration.channel = @"CHANNEL";                 // 채널 설정 (default:@"default")
configuration.isAgreeNotifications = YES;           // 알림 동의 여부 (default:YES)
configuration.isAgreeAdvertisement = YES;           // 광고성 알림 동의 여부 (default:NO)
configuration.isAgreeNightAdvertisement = YES;      // 야간 광고성 알림 동의 여부 (default:NO)

[TCPushSdk initWithConfiguration:configuration];
```

#### Configuration
| 프로퍼티 | 설명 | 기본값 |
| -- | -- | -- |
| appKey | Push 서비스키 | 필수요소 |
| userId | 사용자 식별자 | 필수요소 |
| channel | 채널 | default |
| isAgreeNotification | 알림 표시 동의 여부 | YES |
| isAgreeAdvertisement | 광고성 알림 표시 동의 여부 | NO |
| isAgreeNightAdvertisement | 야간 광고성 알림 표시 동의 여부 | NO |

### 토큰 등록
>PushSDK 초기화 후에 요청이 가능하다.

```objc
[TCPushSdk registerWithPushType:TCPushTypeAPNs completionHandler:^(NSError *error) {
    if (error == nil) {
        // Success

    } else {
        // Fail
    }
}];
```

#### PushType
| 타입 | 설명 |
| -- | -- |
| TCPushTypeAPNs | 일반 푸시 메세지 |
| TCPushTypeAPNsSandbox | 개발용 일반 푸시 메세지 |
| TCPushTypeVoIP | VoIP 푸시 메세지 |
| TCPushTypeVoIPSandbox | 개발용 VoIP 푸시 메세지 |

### 토큰 정보 조회
>PushSDK 초기화 후에 요청이 가능하다.

```objc
[TCPushSdk queryWithPushType:TCPushTypeAPNs completionHandler:^(TCPushTokenInfo *tokenInfo, NSError *error) {
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

### 푸시 수신
>푸시 메세지 수신에 대해 사용자 코드 실행을 위한 Delegate를 설정 할 수 있다.<br>
>일반 푸시 메세지의 경우 어플리케이션이 실행 중이 않을 때에는 수신 Delegate를 받을 수 없다.<br>
>VoIP 푸시 메세지는 어플리케이션이 실행 중이지 않을 때 메세지 수신시 백그라운드에서 자동으로 어플리케이션이 실행되어 수신 Delegate로 메세지를 전달한다.

```objc
@interface AppDelegate () <TCPushDelegate>

@end

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    
    [TCPushSdk setDelegate:self];

    return YES;
}

@end

- (void)pushSdk:(TCPushSdk *)pushSdk didReceiveAPNsNotificationWithPayload:(NSDictionary *)payload {
    // 일반 푸시 메세지 수신
}

- (void)pushSdk:(TCPushSdk *)pushSdk didReceiveVoIPNotificationWithPayload:(NSDictionary *)payload {
    // VoIP 푸시 메세지 수신
}
```

### 지표 수집
>클라이언트에서 푸시 메세지 수신 및 알림에 의한 어플리케이션 실행 여부에 대한 이벤트를 서버에 전송하고,<br>
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

#### 확인(Opened) 지표
>확인 지표에 대한 수집 및 전송은 SDK 내부에서 자동으로 진행된다.

### 오류 코드
| 에러코드 | 설명 |
| -- | -- |
| TCPushErrorNotInitialized | 초기화 되지 않음 |
| TCPushErrorInvalidParameters | 파라미터 오류 |
| TCPushErrorPermissionDenined | 권한 미획득 |
| TCPushErrorSystemFail | 시스템 알림 등록 실패 |
| TCPushErrorNetworkFail | 네트워크 송수신 실패 |
| TCPushErrorServerFail | 서버 응답 실패 |
| TCPushErrorInvalidUrl | 잘못된 URL 요청 |
| TCPushErrorNetworkNotReachable | 네트워크 미연결 |