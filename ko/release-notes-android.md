## Notification > Push > 릴리스 노트 > Android

### 1.7.0 - 2018.10.30
#### 기능 추가
* 리치 메시지 지원
    * 메시지에 이미지 및 큰 아이콘을 사용할 수 있습니다.
    * 메시지에 버튼을 추가할 수 있습니다. (최대 3개)
        * 앱 열기, URL 열기, 닫기, 답장 (API 레벨 24 이상)
    * 메시지들의 그룹핑이 가능합니다. (API 레벨 24 이상)
* 리치 메시지 지원에 따른 API 추가
    * 답장 버튼의 처리를 위한 리스너 클래스 등록 API가 추가되었습니다.

#### 기능 개선
* Android 9 대응
    * 타겟 버전이 28(Android 9) 이상일 경우, 안드로이드 9 기기에서 수신/오픈 지표가 수집되지 않는 버그 수정

### 1.6.0 - 2018.07.24
* Amazon Device Messaging 지원

### 1.5.0 - 2018.06.26
#### 기능 개선
* 지원하는 Tencent SDK 버전 업데이트 (3.2.3)

#### 기능 추가
* 개선된 API 추가 (기존 API도 호환)

### 1.4.4 - 2018.05.29
#### 기능 개선
* PushAnalytics.initialize 호출하지 않아도 동작하도록 수정했습니다.
    * PushAnalytics.initialize 는 Deprecated 될 예정입니다.
* 기본 리시버를 사용할 경우, 안드로이드 8.0 이상에서 Notification Channel을 사용합니다.

### 1.4.3 - 2018.05.02
#### 버그 수정
* GCM, Tencent를 동시에 사용할 경우, Tencent 토큰이 GCM 토큰을 덮어쓰는 문제를 해결했습니다.

#### 기능 개선
* 기존 최소 버전 API level 9(2.3) 에서 API level 15(4.0.1)로 변경

### 1.4.2 - 2017.08.24
#### 기능 추가
* PushAnaytics의 초기화 여부를 확인하는 **PushAnalytics.isInitialize** 메소드가 추가되었습니다.

#### 버그 수정
* 토큰 등록시 잘못된 요청으로 인해 토큰이 삭제될 수 있는 로직에 대한 방어 로직이 추가되었습니다.

### 1.4.1 - 2017.05.25
#### 기능 추가
* SDK 버전 확인을 위한 API가 추가되었습니다.
* 수신 및 오픈 확인 API 추가 및 Deprecated
    * 수신 및 오픈 확인 API 일부가 추가 및 Deprecated 되었습니다.
    * 해당 기능을 사용하기 위해서 반드시 **PushAnalytics.initialize** 메소드를 호출해줘야 합니다.

### 1.4.0 - 2017.04.25
#### 기능 추가
* 메시지 수신, 확인 데이터 수집(Message Delivery Receipt) 기능이 추가되었습니다.
    * 현재 GCM만 지원하며, Tencent는 추후 지원 예정입니다.

### 1.32 - 2017.02.23
#### 버그 수정
* 빌드시 Warning Log 노출되지 않도록 수정
    * 기능상 문제가 없는 것으로, ProGuard 설정 변경을 통해 더 이상 Warning Log가 출력되지 않도록 수정했습니다.

#### 기능 개선
* [Android SDK] 안드로이드 Gradle의 play-services 의존을 play-services-gcm로 수정
    * SDK 빌드시 필요한 컴포넌트만 의존하도록 개선했습니다.

### 1.3 - 2016.11.24
#### 기능 개선/변경
* 지원하는 GCM 라이브러리 버전 업데이트(com.google.android.gms:play-services:9.6.0)
* 오류 로그 세분화

#### 버그 수정
* 채널 기본값 null로 변경
* Appkey가 설정되지 않았을 때 동작안하는 버그 수정
* AAR 패키징시 텐센트 푸시 설정 제거