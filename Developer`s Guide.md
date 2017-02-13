## Notification > Push > Developer's Guide

## 서비스 활성화
[CONSOLE] > [Notification] > [Push] > [상품이용] 버튼 클릭

## CONSOLE 가이드

### 토큰 관리

#### 등록

토큰 등록 API를 통해서 토큰을 등록할 수 있다.

#### 수정

토큰이 새로 발급되었다면, 메시지 전송시 자동으로 최신 토큰으로 수정된다. 또는 토큰 등록 API를 통해서 수정할 수 있다.

#### 삭제

메시지 전송시 사용할 수 없는 토큰이 포함되어 있으면 자동으로 삭제된다. 삭제된 토큰들은 피드백 API를 통해서 얻을 수 있다.

### 인증서 관리

#### FCM API Key 생성, 가져오기

- [[Google Firebase Console](https://console.firebase.google.com)]로 접속
- CREATE NEW PROJECT 선택
- 프로젝트 이름 및 정보 입력 후 프로젝트 생성
- 프로젝트 첫 페이지에서 톱니바퀴(Gear) 아이콘 클릭
- Project settings 클릭
- Settings 페이지에서 CLOUD MESSAGING 탭 클릭
- Server key(API Key), Sender ID 확인

#### FCM API Key 등록

- [CONSOLE] > [Notification] > [Push] > [Certificates] 탭 클릭
- 위에서 생성한 FCM API Key를 복사해서 [GCM API Key]에 붙여넣기 > [REGISTER] 클릭, 등록 완료

#### APNS 인증서 생성, 가져오기

- Mac에서 [키체인]을 실행
- [체인 접근] > [인증서 지원] > [인증 기관에서 인증서 요청] 버튼 클릭
- [사용자 이메일 주소]에 Apple Developer ID 등 필요한 정보 입력, [디스크 저정됨] 선택, [본인이 키 쌍 정보 저장] 선택, [계속] 클릭
- [키 쌍 정보]에서 키 크기, 알고리즘 선택 후 [계속] 클릭, CSR(Certificate Signing Request)) 파일 생성 완료
- [[Apple Developer Certificates](https://developer.apple.com/account/ios/certificate/certificateList.action)]로 이동
- 우측 상단 '+'(Add) 버튼 클릭, Certificate 발급 페이지로 이동
- [Select Type] > [Request] > [Generate] 탭에서 [키체인]을 통해서 만든 CSR파일 업로드, [Generate] 버튼 클릭
- 인증서 생성, 다운로드 가능(승인 권한이 없다면 승인을 받고 다운로드 가능)
- 인증서 다운로드, [키체인] > [인증서]에서 인증서 선택
- 인증서 마우스 오른쪽 선택, 매뉴 중 ['...' 보내기] 선택
- 저장 위치 설정 후 [저장] 클릭
- 보낸 항목을 보호하는데 사용할 암호 암호 입력, APNS 인증서 생성 완료

#### APNS 인증서 등록

- [CONSOLE] > [Notification] > [Push] > [Certificates] 탭 클릭
- [Apple Push Certificates] > [Certificates] 파일 선택 > APNS 인증서 선택
- [Password]란에 인증서 비밀번호 입력
- [REGISTER] 클릭, 등록 완료

#### APNS 주의사항
##### APNS(Production), APNS_SANDBOX(Development) 차이
- Production Provisioning Profile로 빌드한 앱은 APNS(Production)를 이용해야 하며, Development Provisioning Profile로 빌드한 앱은 APNS_SANDBOX(Development)를 이용해야 한다.
이를 지키지 않으면, 푸시 메시지를 정상적으로 수신할 수 없다.
- 토스트 클라우드 푸시에 등록한 APNS 인증서와 앱을 빌드할 때 사용한 Provisioning Profile이 달라 메시지 발송이 실패한 경우가 대부분이다.   
 APNS(Production) = Production Provisioning Profile  
 APNS_SANDBOX(Development) = Development Provisioning Profile  
- 다음 링크를 참고 바란다.  
 [Provisioning and Development](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ProvisioningDevelopment.html)  
 [App Distribution Quick Start](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppStoreDistributionTutorial/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013839)

##### APNS 메시지 발송 실패 원인
- 인증서가 만료되었을 때  
- 잘 못된 인증서를 등록했을 때  
- 앱에서 푸시 메시지 수신을 거부했을 때  
- 디바이스가 인터넷에 연결이 안되어있을 때  

#### Tencent 회원가입

- [[Tencent 푸시 서비스 홈페이지](http://xg.qq.com/)]에 접속한다.
- 페이지 우측 상단 登录(로그인)을 클릭한다.
- 팝업 우측 하단 注册新帐号(회원 가입)을 클릭한다.
- [Email Account] 항목에서 Email Account, Nickname, Password, Verification Code 등 빠짐없이 입력하고 [Sign up now]을 클릭한다.
- [Send verification code] 밑에 [select your country/region]을 클릭한다.
- 'region'에서 'South Korea 0082'를 선택하고, 'Mobile Number'에 010AAAABBBB 형식으로 번호를 입력한다.
- 인증 코드를 입력하고 다음 단계로 넘어간다.
- 가입시 입력한 Email에서 'Activation Email'를 확인하고, Activation 시킨다.

#### Tencent 어플리케이션 등록

- 로그인 후, [[Tencent 푸시 서비스 대시보드](http://xg.qq.com/xg/ctr_index/login?go_to_url=http%3A%2F%2Fxg.qq.com%2Fxg%2Fapps%2Fctr_app%2Findex)]에 접속한다.
- 페이지 우측 상단 接入推送应用(어플리케이션 등록)을 클릭한다.
- 应用名称(어플리케이션 이름)을 입력한다.
- 所属分类(카테고리)를 선택한다.
- 应用平台(어플리케이션 플랫폼)에서 안드로이드를 선택하고, 패키지 네임을 입력한다.
- 接入应用(어플리케이션 생성)을 클릭한다.

#### Tencent ACCESS ID, SECRET KEY 등록

- 로그인 후, [[Tencent 푸시 서비스 대시보드](http://xg.qq.com/xg/ctr_index/login?go_to_url=http%3A%2F%2Fxg.qq.com%2Fxg%2Fapps%2Fctr_app%2Findex)]에 접속한다.
- 应用统计(통계)를 클릭해서, 어플리케이션 대시보드로 들어간다.
- 좌측 메뉴 중 配置管理(설정) > 应用配置(어플리케이션 설정) 클릭한다. ACCESS ID, ACCESS KEY, SECRET KEY를 확인한다.
- 토스트 클라우드로 돌아와 [CONSOLE] > [Notification] > [Push] > [Certificates] 탭 까지 이동한다.
- 확인한 [Tencent Credential]에 ACCESS ID, SECRET KEY를 입력한다.

### 채널 관리

[CONSOLE] > [Notification] > [Push] > [Channels] 탭 클릭, [ADD], [EDIT], [DELETE]로 채널을 관리할 수 있다.

### 즉시 전송

[CONSOLE] > [Notification] > [Push] > [Messages] 탭 > [Send] 버튼을 클릭, 푸시 메시지를 즉시 전송할 수 있다.

즉시 전송 폼은 다음과 같이 구성되어 있다.

|이름|	내용|
|---|---|
|Target| 푸시 메시지를 수신 설정할 수 있다. |
|Target > To | 채널 또는 UID를 입력할 수 있다. Target 우측 [channel/uid] 버튼을 클릭해서 채널, UID로 전환할 수 있다. 쉼표(',')를 구분자로 여러 개를 입력할 수 있다.|
|Target > Type|ALL, CAHNNEL, UID 중에서 하나를 선택할 수 있다. 쉼표(',')를 구분자로 여러 개를 입력할 수 있다.|
|Target > Channels, UIDs|쉼표(',')를 구분자로 여러 개를 입력할 수 있다.|
|Target > Countries|국가 코드를 입력할 수 있다. 쉼표(',')를 구분자로 여러 개를 입력할 수 있다.|
|Target > Push Types|GCM, APNS, APNS Sandbox, Tencent 복수로 선택할 수 있다.|
|Option > Time To Live|메시지 발송 유효 시간이다. 설정한 시간동안 발송이 지연되는 경우, 자동으로 실패 처리된다. 단위는 분이다. 0이면 발송 지연에 의해 실패 처리되지 않는다.|
|Message > Editor Type |SIMPLE, JSON 두 가지 Type이 있다. JSON 경우, 직접 메시지를 작성할 수 있다.|
|Message > Message Type |NOTIFICATION, AD 두 가지 Type이 있다. AD 경우, 광고성 푸시 메시지 수신 동의한 사용자들에게만 메시지가 발송된다.|


### 예약 전송

[CONSOLE] > [Notification] > [Push] > [Reservations] 탭 클릭, [ADD], [EDIT], [DELETE] 버튼을 통해 예약 전송을 관리할 수 있다.

예약 전송 폼은 다음과 같이 구성되어 있다.

|이름|	내용|
|---|---|
|Reservation Type| EVERY_DAY: StartDate, EndDate 사이 매일 지정된 시간에 메시지를 보낸다. <br/> EVERY_WEEK: Reservation Condition에 요일을 지정해 매주 저정한 요일에 메시지를 보낸다. <br/> EVERY_MONTH: Reservation Condition에 날짜를 지정해 매달 지정한 날짜에 메세지를 보낸다.|
|Day of week|요일을 선택할 수 있다.|
|Day of Month|쉼표(',')를 구분자로  날짜를 입력할 수 있다.|
|Reservation time|메시지를 전송할 시간을 입력한다. 시간 형식은 'HH:mm' 이다.|
|Start Date|메시지를 전송할 시작 날짜를 입력한다. 달력을 이용해 날짜를 선택할 수 있다. 형식은 'YYYY-MM-DD' 이다.|
|End Date|메시지를 전송하는 마지막 날짜를 입력한다. 달력을 이용해 날짜를 선택할 수 있다. 형식은 'YYYY-MM-DD' 이다.|
|Local time|true로 설정하면 현지 시간 기준으로 메시지를 전송한다.|

---

## API Reference

### API 테스트

[CONSOLE] > [Notification] > [Push] > [APIs] 탭에서 할 수 있다.

### Secret Key

```
Header
X-Secret-Key: [a-zA-Z0-9]{8}
```

[CONSOLE] > [Notification] > [Push] > [URL & AppKey] 에서 생성할 수 있다.

### Response

[Response HTTP Status Code]  

200 OK.  
모든 API 요청에 대해 200 OK로 응답한다.  
자세한 응답 결과는 Response Body의 Header를 통해 알 수 있다.  

[Response Header]  

```
{
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "Success."
	}
}
```

[resultCode, resultMessage]

| isSuccessful | resultCode | resultMessage |
| --- | --- | --- |
| true | 0 | Success. |
| false | 40001 | Client Error. Wrong URI. |
| false | 40002 | Client Error. Unavailable field value. |
| false | 40003 | Client Error. Bad request. Check your request parameter or body. |
| false | 40004 | Client Error. Target length is exceeded. CHANNEL: 100, UID: 10,000. |
| false | 40005 | Client Error. Content length is exceeded. |
| false | 40006 | Client Error. Wrong target format. Clound't read two target types at once. Send a target, channels or uids at once. |
| false | 40007 | Client Error. Invalid certificate. |
| false | 40008 | Client Error. Invalid APNS certificate. |
| false | 40009 | Client Error. Invalid APNS Sandbox certificate. |
| false | 40101 | Client Error. Permission denied. Access is not allowed. |
| false | 40102 | Client Error. Unavailable appkey. |
| false | 40402 | Client Error. No messages to send in body. |
| false | 40403 | Client Error. No target in body. |
| false | 40404 | Client Error. Not found certificate. |
| false | 40405 | Client Error. Not found instance. |
| false | 40406 | Client Error. Not found reservation. |
| false | 40407 | Client Error. Unavailable reservation. |
| false | 40408 | Client Error. Not found channel. |
| false | 40409 | Client Error. Not found token. |
| false | 40010 | Client Error. Invalid Tencent certificate. |
| false | 40011 | Client Error. Bad request. Check your content. |
| false | 40012 | Client Error. Expired APNS certificate. |
| false | 40013 | Client Error. Duplicate certificate. |
| false | 40014 | Client Error. Wrong message type. Check contact or removeGuide. |
| false | 40015 | Client Error. Wrong reservationDays. |
| false | 50001 ~ 50501 | Internal Error. Please report this. 'http://cloud.toast.com/support/qaa'. |

### 토큰

##### 토큰 등록

[Method, URL]

```
POST https://api-push.cloud.toast.com/push/v1.3/appkey/{appkey}/tokens
Content-Type: application/json;charset=UTF-8
```

[Request Body]

```
{
  "channel": "default",
  "oldToken": "oldToken",
  "token": "token",
  "isNotificationAgreement": true,
  "isAdAgreement": true,
  "isNightAdAgreement": true,
  "pushType": "GCM",
  "timezoneId": "Asia/Seoul",
  "uid": "uid",
  "country": "KR",
  "language": "ko"
}
```

[Response Body]

```
{
	"header" : {
		"isSuccessful" : true or false,
		"resultCode": 0,
		"resultMessage" : "Success."
	}
}
```

|Parameter|	Usage||
|---|---|---|
|token|	Required, String|	Token. 255 bytes.|
|oldToken|	Optional, String|	Old Token. 255 bytes.|
|channel|	Optional, String|	Channel name. 50 bytes.|
|pushType|	Required, String|	GCN, APNS, APNS_SANDBOX, TENCENT|
|isNotificationAgreement|	Required, Boolean|	true or false|
|isAdAgreement|	Required, Boolean|	true or false|
|isNightAdAgreement|	Required, Boolean|	true or false|
|timezoneId|	Required, String|	Area/Name. IANA time zone database.|
|country|	Required, String|	ISO 3166-1 alpha-2, ISO 3166-1 alpha-3. 3 bytes.|
|language|	Required, String|	ISO 639-1, ISO 639-2, iOS(language code + script code). 8 bytes.|
|uid|	Required, String|	User ID, 64 bytes.|

- 토큰 등록시 이미 토큰이 등록되어 있다면, 토큰 기준으로 다른 정보들이 업데이트 된다.
- 만약, 토큰이 변경된다면 oldToken에 기존 토큰을, token에 새로운 토큰을 설정하고 등록하면 새로운 토큰으로 업데이트 된다.
- 토큰 등록시 Channel을 등록하면, 메시지 발송시 특정 Channel로 발송할 수 있다. 필수는 아니며, 정의하지 않으면 "default"로 등록된다.
- 토큰은 하나의 Channel에만 속할 수 있다.
- "isNotificationAgreement" 푸시 메시지 수신 동의 여부, "isAdAgreement" 광고성 푸시 메시지 수신 여부, "isNightAdAgreement" 야간 광고성 푸시 메시지 수신 여부를 나타낸다.
- 예로, 모든 푸시 메시지 수신을 원할 경우, 필드 3개 모두 true로 설정하면 된다. 푸시 메시지만 수신할 경우, "isNotificationAgreement"만 true로 설정하면 된다.
- 수신 동의 여부는 정보통신망법 규정(제50조부터 제50조의 8)을 따른다.  
[KISA 가이드 바로 가기](http://spam.kisa.or.kr/kor/notice/dataView.jsp?p_No=49&b_No=49&d_No=52)    
[법령 바로 가기](http://www.law.go.kr/lsEfInfoP.do?lsiSeq=123210#)  

- 네트워크 상태가 좋지 않거나 여러 이유로 인한 응답 지연이 발생할 수 있다. 모바일 어플리케이션 구동에 영향을 최소화 하기위해 Timeout을 짧게 설정하고, 구동될 때 마다 토큰을 등록하는 것이 좋다.
- 토큰은 보안적인 이슈, 앱 업데이트, 삭제 등 여러가지 이유로 재발급될 수 있다. 자주 변경되는 것은 아니지만, 수신율을 높이기 위해 구동될 때 마다 최신 토큰을 등록하는 것이 좋다.
- 앱 삭제 등으로 토큰이 만료되어도 바로 GCM, APNS 서버에 적용되지 않아, 앱 삭제 후 푸시 메시지를 발송했을 때 발송이 성공할 수 있다.

#### 토큰 조회

###### a. 토큰과 푸시타입으로 토큰 조회

[Method, URL]

```
GET https://api-push.cloud.toast.com/push/v1.3/appkey/{appkey}/tokens?token={token}&pushType={pushType}
Content-Type: application/json;charset=UTF-8
```

[Response Body]

```
{
	"token" : {
		"channel": "default",
		"pushType" : "GCM",
		"isNotificationAgreement": true,
		"isAdAgreement": true,
		"isNightAdAgreement": true,
		"timezoneId" : "Asia/Seoul",
		"country": "KR",
		"language": "ko",
		"uid" : "User ID",
		"token" : "Token"
	},
	"header" : {
		"isSuccessful" : true or false,
		"resultCode": 0,
		"resultMessage" : "Success."
	}
}
```

##### b. uid로 토큰 조회

[Method, URL]

```
GET https://api-push.cloud.toast.com/push/v1.3/appkey/{appkey}/uids/{uid}/tokens
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

[Response Body]

```
{
	"tokens": [{
		"channel": "default",
		"pushType" : "GCM",
		"isNotificationAgreement": true,
		"isAdAgreement": true,
		"isNightAdAgreement": true,
		"timezoneId" : "Asia/Seoul",
		"country": "KR",
		"language": "ko",
		"uid" : "User ID",
		"token" : "Token"
	}],
	"header" : {
		"isSuccessful" : true or false,
		"resultCode": 0,
		"resultMessage" : "Success."
	}
}
```

- Secret Key가 필요한 API이며, 서버에서 호출되어야 한다.

### 메시지

#### 메시지 발송

[Method, URL]

```
POST https://api-push.cloud.toast.com/push/v1.3/appkey/{appkey}/messages
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

[Request Body]

```
{
	"target" : {
		"type" : "ALL"
	},
	"content" : {
		"default" : {
            "title": "title",
            "body": "body"
        }
	},
	"messageType" : "NOTIFICATION"
}
```

[Response Body]

```
{
	"message" : {
		"messageId" : long
	},
	"header" : {
		"isSuccessful" : true or false,
		"resultCode": 0,
		"resultMessage" : "Success."
	}
}
```

| Parameter | Usage |  |
| --- | --- | --- |
| target.type | Required, String | ALL, CHANNEL, UID |
| target.to | Optional, String Array | target.type이 CHANNEL이면 100개, UID면 10,000개다. |
| target.pushTypes | Optional, String Array | GCM, APNS, APNS_SANDBOX, TENCENT |
| target.countries | Optional, String Array | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3. 3 bytes. |
| content | Required, Map | 8192 bytes |
| content.default | Required, Map |  |
| content.default.title | Optional, String |  |
| content.default.body | Optional, String |  |
| messageType | Required, String | NOTIFICATION, AD |
| contact | Optional, String | messageType이 AD일 경우 필요하다. |
| removeGuide | Optional, String | messageType이 AD일 경우 필요하다. |
| timeToLive | Optional, Number | 단위는 분이다. 범위는 0(무제한)포함 1 이상이다. 기본 값은 60 이다. |
| isStored | Optional, Boolean | 메시지를 저장할지 여부다. 기본값은 false다. |

- "target.pushTypes" 필드로 특정 푸시 타입으로만 메시지를 발송할 수 있다.
만약, 정의하지 않으면 모든 푸시 타입, GCM, APNS, APNS_SANDBOX, TENCENT로 발송한다.
- "target.countries" 필드가 "['KR', 'JP']"면 토큰 국가 코드가 "KR" 또는 "JP"인 Token에 발송한다.
- "content.default" 필드는 필수이며, "content" 필드에 대한 자세한 내용은 아래 [공통 메시지 포맷]을 참고 바란다.
- 메시지를 광고 타입, "messageType": "AD", 으로 보낼 경우, "contact", "removeGuide" 필드를 반드시 포함해야 한다.
"contact" 필드에 연락처를 입력해야 하며, "removeGuide" 필드에 수신 철회 방법에 대해 입력해야 한다.
- timeToLive 필드를 설정하면, 설정한 시간 이상 발송이 지연되는 경우 자동으로 실패 처리된다.


["target" Example]

```
"target.type"이 "ALL"이면 "target.to"는 필요 없다.
Request Body
{
	"target" : {
		"type" : "ALL"
	},
	"content" : {
		"default" : {
			"title": "title",
			"body": "body"
		}
	}
}
"target.type"이 "UID" 또는 "CHANNEL"이면 "target.to"에 전송할 uid 또는 channel name을 입력해야 한다.
Request Body
{
	"target" : {
		"type" : "UID",
		"to" : ["uid-01", "uid-02"]
	},
	"content" : {
		"default" : {
			"title": "title",
			"body": "body"
		}
	}
}
```

["messageType" Example]

```
광고성 푸시 메시지가 아닌 일반 알림 푸시 메시지로 보낼 경우,
"messageType"를 "NOTIFICATION"으로 하고, "contact", "removeGuide"는 입력하지 않는다.
Request Body
{
	"target" : {
		"type" : "ALL"
	},
	"content" : {
		"default" : {
			"title": "title",
			"body": "body"
		}
	},
	"messageType" : "NOTIFICATION"
}
광고성 푸시 메시지로 보낼 경우, "messageType"를 "AD"하고, "contact"에 연락처,
"removeGuide"에 수신 동의 철회 방법을 입력한다.
Request Body
{
	"target" : {
		"type" : "ALL"
	},
	"content" : {
		"default" : {
			"title": "title",
			"body": "body"
        }
	},
	"messageType" : "AD",
	"contact" : "연락처",
	"removeGuide" : "수신 철회 방법"
}
```

#### 공통 메시지

API v1.3 부터 공통 메시지 형식을 지원 한다. "content"에 아래 표대로 메시지를 작성하면, 각 푸시 타입에 맞게 메시지가 생성되어 발송된다.

|Reserved Word|	Platform|	Usage|	GCM|	APNS|	TENCENT|
|---|---|---|---|---|---|
|title|	Android, <br/> iOS Watch, <br/> Tencent|	Optional, String|	data.title|	aps.alert.title|	title|
|body|	Android, <br/> iOS, <br/> Tencent|	Optional, String|	data.body|	aps.alert.body|	body|
|title-loc-key|	iOS|	Optional, String| - | aps.alert.title-loc-key| - |
|title-loc-args|	iOS|	Optional, Array of Strings| - | aps.alert.title-loc-args	| - |
|action-loc-key|	iOS|	Optional, String| - |aps.alert.action-loc-key	| - |
|loc-key|	iOS|	Optional, String| - |aps.alert.loc-key	| - |
|loc-args|	iOS|	Optional, Array of String	| - | aps.alert.loc-args	| - |
|launch-image|	iOS|	Optional, String	| - | aps.alert.launch-image	| - |
|badge|	iOS|	Optional, Number| - | aps.badge	| - |
|sound|	Android, <br/> iOS, <br/> Tencent|	Optional, String|	data.sound|	aps.sound|	custom_content.sound|
|content-available|	iOS|	Optional, String	| - | aps.content-available	| - |
|category|	iOS|	Optional, String	| - | aps.category	| - |

그 외 사용자가 정의한 Word는 다음과 같이 Custom Key/Value 필드에 들어간다.

|Reserved Word|	Platform|	Usage|	GCM|	APNS|	TENCENT|
|---|---|---|---|---|---|
|customKey|	Android, <br/> iOS, <br/> Tencent|	Optional, <br/> Object, <br/> Array, <br/> String, <br/> Number|	data.customKey|	customKey|	custom_content.customKey|

["content" Example]

```
"content.default"는 필수다. 아래 "content.ko", "content.ja"는 토큰의 언어 코드 값이다.
해당 토큰의 언어 코드에 맞게 메시지가 발송된다.
Request Body
{
	"target" : {
		"type" : "ALL"
	},
	"content" : {
        "default" : {
            "title": "title",
            "body": "body",
            "badge": 1,
            "key": "value"
        },
        "ko" : {
            "title": "제목",
            "body": "내용"
            "key": "값"
        },
        "ja" : {
            "title": "タイトル",
            "body": "プッシュ・メッセージ"
        }
	},
	"messageType" : "NOTIFICATION"
}
"ko" GCM 메시지
 {
    "data": {
        "title": "제목",
        "body": "내용",
        "key": "값"
    }
}
"ja" GCM 메시지
 {
    "data": {
        "title": "タイトル",
        "body": "プッシュ・メッセージ",
        "key": "value"
    }
}
"ko" APNS 메시지
{
    "aps": {
        "alert": {
            "title": "제목",
            "body": "내용"
        },
        "badge": 1
    },
    "key": "값"

}
"ja" APNS 메시지
{
    "aps": {
        "alert": {
            "title": "タイトル",
            "body": "プッシュ・メッセージ"
        },
        "badge": 1
    },
    "key": "value"
}
"ko" TENCENT 메시지
 {
	"title": "제목",
	"body": "내용",
	"custom_content": {
		"key": "값"
	}
}
"ja" TENCENT 메시지
 {
	"title": "タイトル",
	"body": "プッシュ・メッセージ",
	"custom_content": {
		"key": "value"
	}
}
```

#### 메시지 조회

[Method, URL]

```
GET https://api-push.cloud.toast.com/push/v1.3/appkey/{appkey}/messages/{message-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

[Response Body]

```
{
  "header": {
    "resultCode": 0,
    "resultMessage": "SUCCESS",
    "isSuccessful": true
  },
  "message": {
    "messageId": 42983,
    "messageType": "NOTIFICATION",
    "target": {
      "type": "ALL",
      "pushTypes": [
        "GCM"
      ]
    },
    "content": {
      "default": {
        "title": "title",
        "body": "body"
      }
    },
    "targetCount": 1048576,
    "timeToLive": 0,
    "sentTime": "2015-11-23T18:39:38.000+0900",
    "createdDateTime": "2015-11-23T18:39:38.000+0900",
    "messageStatus": "COMPLETE"
  }
}



```

"messageStatus" 필드는 메시지 상태를 나타낸다. 다음과 같은 상태가 있다.

- READY: 메시지 발송 요청이 등록된 상태다.
- IN_MQ: 메시지 생성이 끝나고, 대기 또는 발송 중이다.
- COMPLETE: 메시지 발송이 완료된 상태다.
- CANCEL_NO_TARGET: 메시지 발송 대상이 없어서 취소된 상태다. 다음과 같은 이유로 발송이 취소될 수 있다.  
 등록된 토큰이 없을 때  
 해당 Channel 또는 Uid가 없을 때  
 광고 푸시 메시지의 경우, 수신 동의한 사용자가 없을 때  
 야간 광고 푸시 메시지(21시 ~ 8시)의 경우, 야간 광고 수신 동의한 사용자가 없을 때  
 기존 등록된 토큰들이 삭제되어 토큰이 없을 때    
- CANCEL_INVALID_CERTIFICATE: 인증서가 잘 못되어 취소된 상태다. 인증서 상태를 확인해야 한다.
- CANCEL_INVALID_MESSAGE: 메시지 형식이 맞지않아 취소된 상태다.
- CANCEL_UNSUPPORTED_MESSAGE_TYPE: 메시지 형식이 맞지않아 취소된 상태다.
- CANCEL_UNAUTHORIZED: 인증서 인증 과정에서 실패한 상태다. 인증서 상태를 확인해야 한다.
- CANCEL_UNKNOWN: 내부 오류가 발생한 상태다.

### 피드백

#### 피드백 확인

[Method, URL]

```
GET https://api-push.cloud.toast.com/push/v1.3/appkey/{appkey}/feedback
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

[Response Body]

```
{
	"feedback" : [{
		"uid" : "Uid",
		"token" : "Deleted Token",
		"newToken" : "New Token. Nullable",
		"pushType" : "GCM",
		"updateTime" : "yyyy-MM-dd'T'HH:mm:ss.SSSZ"
	}],
	"header" : {
		"isSuccessful" : true or false,
		"resultCode": 0,
		"resultMessage" : "Success."
	}
}
```
