## Notification > Push > SDK v1.5 사용 가이드
TOAST Cloud Push SDK를 적용하면 모바일 애플리케이션과 TOAST Cloud Push를 쉽게 연동할 수 있다.

## 주요기능
* OS에 알림 토큰 등록
* 알림 메세지 수신 및 표시
* 메세지 수신 및 수신된 메세지를 통한 어플리케이션 실행 지표 수집

## 다운로드
[Downloads](http://docs.toast.com/ko/Download/) 페이지에서 Push SDK를  다운로드 받을 수 있습니다.
```
[DOCUMENTS] > [Download] > [Notification > Push]
```

## Android
### 지원환경

### 프로젝트 설정

### 토큰 등록
### 토큰 정보 조회
### 지표 수집
#### 수신(Received) 지표
#### 실행(Opened) 지표

### 오류 코드


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
| -- | -- | -- |
| TCPushErrorInvalidParameters | 파라미터 오류 |
| TCPushErrorPermissionDenined | 권한 미획득 |
| TCPushErrorSystemFail | 시스템 알림 등록 실패 |
| TCPushErrorNetworkFail | 네트워크 송수신 실패 |
| TCPushErrorServerFail | 서버 응답 실패 |
| TCPushErrorInvalidUrl | 잘못된 URL 요청 |
| TCPushErrorNetworkNotReachable | 네트워크 미연결 |