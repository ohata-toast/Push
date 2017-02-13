## Notification > Push > Release Notes

### 2016.02.23
#### 버그 수정
* [Android SDK] 빌드시 Warning 로그 노출되지 않도록 수정
* [iOS SDK] Channel의 기본값을 NSNull에서 "default"로 수정
* [iOS SDK] 토큰 리프레쉬 과정에서 크래시나는 현상 수정

#### 개발자 가이드 수정
* [Client SDK Developer's Guide] 안드로이드 Gradle의 play-services 의존을 play-services-gcm로 수정
* [Client SDK Developer's Guide] 토큰 조회 API 문서 보강

### 2016.01.19

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