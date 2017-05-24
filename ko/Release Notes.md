## Notification > Push > Release Notes

### 2017.05.25
#### 기능 추가
* [SDK] SDK 버전 확인을 위한 API 추가
    * SDK 버전 확인을 위한 API가 추가되었습니다.
* [Android SDK] 수신 및 오픈 확인 API 추가 및 Deprecated
    * 수신 및 오픈 확인 API 일부가 추가 및 Deprecated 되었습니다.
    * 해당 기능을 사용하기 위해서 반드시 **PushAnalytics.initialize** 메소드를 호출해줘야 합니다.


### 2017.04.25
#### 기능 추가
* [API] 메시지 수신, 확인 데이터 수집(Message Delivery Receipt), 통계 조회 기능 추가
    * 메시지 발송 후, 기기에 수신, 사용자의 메시지 확인 데이터를 수집해 통계로 조회할 수 있는 기능을 추가되었습니다.
    * [CONSOLE] > [Settings] 탭에서 활성화 시킬 수 있으며, v2.0 API Reference에서 통계 조회 API 설명을 확인할 수 있습니다.
    * 해당 기능은 v1.4이상 SDK가 적용된 곳에서만 사용할 수 있습니다.    
    <a href="/ko/Notification/Push/ko/Client%20SDK%20Guide/#_4" target="_blank">SDK 수신 및 오픈 여부 적용 가이드 바로 가기</a>
* [API] v2.0 API 추가
    * 토큰 통계 API가 추가되었습니다.
    * 예약 메시지 API가 추가되었습니다.
    * 메시지 수신, 확인 통계 조회 API가 추가되었습니다.
    * v1.3 피드백 API는 v2.0 유효하지 않은 토큰 API로 변경되었습니다.
    * 응답 메시지를 더 자세하게 출력합니다.    
    <a href="/ko/Notification/Push/ko/Developer%60s%20Guide" target="_blank">v2.0 API Reference 바로 가기</a>
* [CONSOLE] Dashboard, Setting 탭 추가
    * 메시지 수신, 확인 통계를 확인할 수 있는 [Dashboar]d 탭이 추가되었습니다.
    * 메시지 수신, 확인 데이터 수집 기능을 설정할 수 있는 [Settings] 탭이 추가되었습니다.
* [SDK] v1.4 SDK 추가
    * 메시지 수신, 확인 데이터 수집(Message Delivery Receipt) 기능이 추가되었습니다.
    * 현재 GCM, iOS만 지원하며, Tencent는 추후 지원 예정입니다.
    * iOS는 Notification Service Extension를 사용하기 때문에 iOS 10이상에서만 동작합니다.

### 2017.02.23
#### 버그 수정
* [API] 발송 기간이 한 달 이상인 예약 메시지 발송 안되는 오류 수정
    * 2017년 1월까지 등록된 예약 메시지 중, 발송 종료일이 2월 이상인 예약 메시지가 발송안되는 오류를 수정했습니다.
* [Android SDK] 빌드시 Warning Log 노출되지 않도록 수정
    * 기능상 문제가 없는 것으로, ProGuard 설정 변경을 통해 더 이상 Warning Log가 출력되지 않도록 수정했습니다.
* [iOS SDK] 토큰 리프레쉬 과정에서 크래시나는 현상 수정
    * 앱 알림 설정을 껐다가 다시 켰을 경우 발생하는 오류로 토큰을 재생성하는 코드를 수정했습니다.

#### 기능 개선
* [Android SDK] 안드로이드 Gradle의 play-services 의존을 play-services-gcm로 수정
    * SDK 빌드시 필요한 컴포넌트만 의존하도록 개선했습니다.

### 2017.01.19

#### 기능 개선/변경
* [API] 메시지 조회 API Response Body에 createdDateTime(메시지 생성 시간) 필드 추가
* [Etc] 인증서 만료 안내 메일 계정 변경 (support@cloud.toast.com -> noreply@cloud.toast.com)

### 2016.12.22
#### 버그 수정
* [API] 예약 메시지 등록이 한 달이 지났을 경우 발송 안되는 오류 수정
* [API] 매월 1일 발송될 예약 메시지가 발송 안되는 오류 수정
* [API] 새 버전 FCM API Key가 등록 안되던 오류 수정
* [API] 발송 결과가 'MismatchSenderId', 'NotRegistered'인 경우 토큰이 삭제되지 않던 오류 수정

#### 개발자 가이드 수정
* [Client SDK Developer's Guide] 본문 내 'GCM Push Credentials', 'GCM API Key'로 변경

### 2016.11.24
#### 기능 개선/변경
* [Android SDK] 지원하는 GCM 라이브러리 버전 업데이트(com.google.android.gms:play-services:9.6.0)
* [Android SDK] 오류 로그 세분화

#### 버그 수정
* [Android, iOS SDK] 채널 기본값 null로 변경
* [Android SDK] Appkey가 설정되지 않았을 때 동작안하는 버그 수정
* [Android SDK] AAR 패키징시 텐센트 푸시 설정 제거

#### 개발자 가이드 수정
* [Client SDK Developer's Guide] 토큰 등록시 채널 설정 설명 변경 (SDK v1.3 이하 채널 설정 필수)

### 2016.10.06
#### 기능 개선/변경
* [API, CONSOLE] MPS 단위 1,000개/초에서 100개/초로 변경

### 2016.09.30
#### 개발자 가이드 수정
* [Client SDK Developer's Guide] '토큰 등록 > Android, GCM' 부분, 채널 설정시 'Optional'에서 'Required'로 수정

### 2016.09.29
#### 정책 변경
* 데이터 보관 기간 정책 변경, 최근 30 일까지 저장 (메시지, 예약 메시지, 피드백)

#### 기능 개선/변경
* [CONSOLE] 인증서 삭제 없이 바로 교체할 수 있도록 수정

#### 버그 수정
* [API, CONSOLE] APNS Universal Certificate APNS_SANDBOX(Development)에 등록 안되는 오류 수정
* [API] UID 기준 토큰 조회 API에서 APNS_SANDBOX 토큰이 제외되는 오류 수정
* [API] 토큰 등록 API에서 Empty String("")이 등록 되는 오류 수정

### 2016.08.18
#### 버그 수정
* [CONSOLE] 예약 메시지 수정시 요일이 저장한 내용과 다르게 표시되는 오류 수정
