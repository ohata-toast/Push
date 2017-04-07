## Notification > Push > Developer's Guide
이전 버전보기 :   <select onchange="location.href=this.value"><option selected value="/ko/Notification/Push/Developer%60s%20Guide%20v2.0">API v2.0</option><option value="/ko/Notification/Push/Developer%60s%20Guide">API v1.3</option></select>

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

## v2.0 API Reference

### Endpoint
```
https://api-push.cloud.toast.com
```

### Secret Key

```
Header
X-Secret-Key: [a-zA-Z0-9]{8}
```

[CONSOLE] > [Notification] > [Push] > [URL & AppKey] 에서 생성할 수 있다.

### Response

#### Response HTTP Status Code
200 OK.  
모든 API 요청에 대해 200 OK로 응답한다.  
자세한 응답 결과는 Response Body의 Header를 통해 알 수 있다.  

#### Response Header

```
{
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```
#### resultCode, resultMessage

| isSuccessful | resultCode | resultMessage |
| --- | --- | --- |
| true | 0 | SUCCESS |
| false | 40001 | Client Error. Parameter is invalid. |
| false | 40002 | Client Error. Parameter is invalid format. |
| false | 40003 | Client Error. Parameter is empty or null. |
| false | 40004 | Client Error. Duplicate certificate. |
| false | 40005 | Client Error. Expired certificate. |
| false | 40006 | Client Error. Already registered. |
| false | 40007 | Client Error. Maximum limit exceeded. |
| false | 40008 | Client Error. Already completed. |
| false | 40101 | Client Error. Access is not allowed. |
| false | 40102 | Client Error. Unavailable key. |
| false | 40401 | Client Error. Not found. |
| false | 50001 ~ 50501 | Internal Error. Please report this. 'http://cloud.toast.com/support/qaa'. |

#### v1.3 API와 차이점
상세한 resultMessage를 리턴한다.
문제가 되는 필드, 가능한 경우 값까지 리턴한다.
- e.g. 잘 못된 메시지 아이디로 조회했을 경우, 다음과 같이 messageId 필드와 값을 resultMessage에 포함 시켜준다.
```
{
    "header" : {
		"resultCode" : 40401,
		"resultMessage" : "Client Error. Not found. messageId<3496615188236841>",
		"isSuccessful" : false
    }
}
```

### 토큰

#### 토큰 등록

##### Method, URL
```
POST /push/v2.0/appkeys/{appkey}/tokens
Content-Type: application/json;charset=UTF-8
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |

##### Request Body

```
{
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

|Field|	Usage||
|---|---|---|
|token|	Required, String|	토큰, 최대 255 byte|
|oldToken|	Optional, String|	기존 토큰, 최대 255 bytes |
|pushType|	Required, String|	GCN, APNS, APNS_SANDBOX, TENCENT|
|isNotificationAgreement|	Required, Boolean|	true or false|
|isAdAgreement|	Required, Boolean|	true or false|
|isNightAdAgreement| Required, Boolean|	true or false|
|timezoneId|	Required, String|	Area/Name. IANA time zone database.|
|country|	Required, String|	ISO 3166-1 alpha-2, ISO 3166-1 alpha-3, 3 byte|
|language|	Required, String|	ISO 639-1, ISO 639-2, iOS(language code + script code), 8 byte|
|uid|	Required, String|	사용자 아이디, emoji 허용 안함, 최대 64 bytes|


##### Response Body

```
{
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```
##### Description

- 토큰 등록시 이미 토큰이 등록되어 있다면, 토큰 기준으로 다른 정보들이 업데이트 된다.
- 만약, 토큰이 변경된다면 oldToken에 기존 토큰을, token에 새로운 토큰을 설정하고 등록하면 새로운 토큰으로 업데이트 된다.
- 토큰 등록시 Channel을 등록하면, 메시지 발송시 특정 Channel로 발송할 수 있다. 필수는 아니며, 정의하지 않으면 "default"로 등록된다.
- 토큰은 하나의 Channel에만 속할 수 있다.
- "isNotificationAgreement" 푸시 메시지 수신 동의 여부, "isAdAgreement" 광고성 푸시 메시지 수신 여부, "isNightAdAgreement" 야간 광고성 푸시 메시지 수신 여부를 나타낸다.
- 예로, 모든 푸시 메시지 수신을 원할 경우, 필드 3개 모두 true로 설정하면 된다. 푸시 메시지만 수신할 경우, "isNotificationAgreement"만 true로 설정하면 된다.
- 수신 동의 여부는 정보통신망법 규정(제50조부터 제50조의 8)을 따른다.  
    - [KISA 가이드 바로 가기](http://spam.kisa.or.kr/kor/notice/dataView.jsp?p_No=49&b_No=49&d_No=52)    
    - [법령 바로 가기](http://www.law.go.kr/lsEfInfoP.do?lsiSeq=123210#)  
- 네트워크 상태가 좋지 않거나 여러 이유로 인한 응답 지연이 발생할 수 있다. 모바일 어플리케이션 구동에 영향을 최소화 하기위해 Timeout을 짧게 설정하고, 구동될 때 마다 토큰을 등록하는 것이 좋다.
- 토큰은 보안적인 이슈, 앱 업데이트, 삭제 등 여러가지 이유로 재발급될 수 있다. 자주 변경되는 것은 아니지만, 수신율을 높이기 위해 구동될 때 마다 최신 토큰을 등록하는 것이 좋다.
- 앱 삭제 등으로 토큰이 만료되어도 바로 GCM, APNS 서버에 적용되지 않아, 앱 삭제 후 푸시 메시지를 발송했을 때 발송이 성공할 수 있다.

#### 토큰과 푸시타입으로 토큰 조회

##### Method, URL

```
GET /push/v2.0/appkeys/{appkey}/tokens/{token}?&pushType={pushType}
Content-Type: application/json;charset=UTF-8
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| pushType | Required, String | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT' |

##### Response Body

```
{
	"token" : {
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
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```

#### 사용자 아이디로 토큰 조회

##### Method, URL

```
GET /push/v2.0/appkeys/{appkey}/tokens?uid={uid}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| uid | Required, String | 조회할 사용자 아이디 |

##### Response Body

```
{
	"tokens": [{
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
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```
##### Description
- Secret Key가 필요한 API이며, 서버에서 호출되어야 한다.

#### 토큰 속성 통계 조회 API

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/statistics/token-properties?from={from}&to={to}&tokenProperties={tokenProperties}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| from | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| tokenProperties | Optional, String Array | 'agreement', 'country', 'language', 'timezone'<br/>','로 구분, e.g. tokenProperties=country,language |

##### Request Body
```
없음
```

##### Response Body
```json
{
	"tokenPropertyStatistics" : [{
			"dateTime" : "2016-07-11 17:50:00.00+9:00",
			"countries" : {
				"KR" : 100,
				"JP" : 60,
				"CN" : 100
			},
			"languages" : {
				"ko" : 90,
				"ja" : 60,
				"zh" : 100
			},
			"timezones": {
				"Asia/Seoul": 260
			}
		}, {
			"dateTime" : "2016-07-11 17:51:00.00+9:00",
			"countries" : {
				"KR" : 100,
				"JP" : 60,
				"CN" : 100
			},
			"languages" : {
				"ko" : 90,
				"ja" : 60,
				"zh" : 100
			},
			"timezones": {
				"Asia/Seoul": 260
			}
		}
	],
	"header" : {
		"isSuccessful" : true,
		"resultCode" : 0,
		"resultMessage" : "SUCCESS"
	}
}
```

| Field | Usage | Description |
| - | - | - |
| dateTime | String | 데이터가 수집된 일시 |
| agreements | String | 'ON'(모두 수신), 'NIGHT_AD_OFF'(야간 광고 수신 거부), 'AD_OFF'(광고 수신 거부), 'OFF'(모두 수신 거부) |
| countries.XX | String | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3, 3 byte |
| languages.XX | String | ISO 639-1, ISO 639-2, iOS(language code + script code), 8 byte |
| timezone.XX | String | Area/Name. IANA time zone database |

#### 토큰 등록 통계 조회

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/statistics/token-registration?from={from}&to={to}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| from | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |

##### Request Body
```
없음
```

##### Response Body
```json
{
	"tokenRegistrationStatistics" : [{
			"dateTime" : "2016-07-11 17:50:00.00+9:00",
			"registeredToken" : 90,
			"deletedToken" : 20
		}, {
			"dateTime" : "2016-07-11 17:51:00.00+9:00",
			"registeredToken" : 45,
			"deletedToken" : 10
		}
	],
	"header" : {
		"isSuccessful" : true,
		"resultCode" : 0,
		"resultMessage" : "SUCCESS"
	}
}
```

| Field | Usage | Description |
| - | - | - |
| dateTime | String | 데이터가 수집된 일시 |
| registeredToken | Number | 등록된 토큰 수 |
| deletedToken | Number | 삭제된 토큰 수 |

#### v1.3 API와 차이점
- 'channel' 필드가 삭제되었다.
- 토큰 등록, 속성에 대한 통계 API가 추가되었다.

### 메시지

#### 메시지 발송
##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/messages
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
##### Request Body
```json
{
    "target" : {
        "type" : "UID",
        "to": ["uid-1", "uid-2"]
    },
    "content" : {
        "default" : {
            "title": "title",
            "body": "body"
        }
    },
    "messageType" : "AD",
    "contact": "1588-1588",
    "removeGuide": "매뉴 > 설정",
    "timeToLiveMinute": 1,
	"provisionedResourceId": "id"
}
```

##### Response Body
```json
{
    "message" : {
        "messageId" : 0,
        "messageIdString": "0"
    },
    "header" : {
        "isSuccessful" : true,
        "resultCode": 0,
        "resultMessage" : "SUCCESS"
    }
}
```

##### Description

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| target.type | Required, String | 'ALL', 'UID', 수신 타겟 타입 |
| target.to | Optional, String Array | target.type이 'UID'일 때, 수신자 UID 목록. (최대 10,000 개) |
| target.pushTypes | Optional, String Array | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT' |
| target.countries | Optional, String Array | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3 (최대 3 byte) |
| content | Required, Map | 수신자에게 전달될 내용 (최대 8,192 byte) |
| content.default | Required, Map | '자세한 내용은 아래 공통 메시지 형식' 참고 |
| content.default.title | Optional, String |  |
| content.default.body | Optional, String |  |
| messageType | Required, String | NOTIFICATION, AD |
| contact | Optional, String | messageType이 AD이면 경우 필수 |
| removeGuide | Optional, String | messageType이 AD이면 경우 필수 |
| timeToLiveMinute | Optional, Number | 단위는 분이다. 범위는 1에서 60까지다. 기본 값은 10 이다. |
| provisionedResourceId | Optional, String | 할당 받은 전용 리소스(provisioned Resource) 아이디다. 사용 문의 support@cloud.toast.com |

#### 메시지 목록 조회

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/messages?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&messageStatus={messageStatus}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| pageIndex | Optional, Number | 기본 값 0 |
| pageSize | Optional, Number | 기본 값 25, 최대 값 100 |
| from | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| messageStatus | Optional, String | 'READY', 'PROCESSING', 'COMPLETE', 'CANCEL_NO_TARGET', 'CANCEL_INVALID_CERTIFICATE', 'CANCEL_INVALID_MESSAGE', 'CANCEL_UNSUPPORTED_MESSAGE_TYPE', 'CANCEL_UNAUTHORIZED', 'CANCEL_UNKNOWN' |

##### Request Body
```
없음
```
##### Response Body
```json
{
    "messages" : [{
        "messageId" : 0,
        "messageIdString": "0",
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
        "contact": "1588-1588",
        "removeGuide": "매뉴 > 설정",
        "timeToLive": 60,
        "createdDateTime": "2017-02-13T09:30:00.000+09:00",
        "completedDateTime": "2017-02-13T09:30:00.000+09:00",
        "targetCount": 1000,
		"sentCount": 1000,
        "messageStatus": "COMPLETE",
        "provisionedResourceId": "[a-zA-Z0-9]{16}"
    }],
    "header" : {
        "isSuccessful" : true,
        "resultCode": 0,
        "resultMessage" : "SUCCESS"
    }
}
```

| Field | Usage | Description |
| - | - | - |
| createdDateTime | - | 메시지가 생성된 일시 (ISO 86091) |
| completedDateTime | - | 메시지 발송이 완료된 일시 (ISO 86091) |
| targetCount | - | 발송될 타겟 토큰 수 |
| sentCount | - | 실제 발송된 토큰 수 |
| provisionedResourceId | - | 메시지가 발송된 전용 리소스 아이디 |


##### Description

- "messageStatus" 필드는 메시지 상태를 나타낸다. 다음과 같은 상태가 있다.
    - READY: 메시지 발송 요청이 등록된 상태다.
    - PROCESSING: 메시지 생성이 끝나고, 대기 또는 발송 중이다.
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

#### 메시지 조회

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/messages/{message-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| messageId | Required, Number | 메시지 아이디 |

##### Request Body
```
없음
```
##### Response Body
```json
{
    "message" : {
        "messageId" : 0,
        "messageIdString": "0",
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
        "contact": "1588-1588",
        "removeGuide": "매뉴 > 설정",
        "timeToLive": 60,
        "createdDateTime": "2017-02-13T09:30:00.000+09:00",
        "completedDateTime": "2017-02-13T09:30:00.000+09:00",
        "targetCount": 1000,
        "messageStatus": "COMPLETE",
        "provisionedResourceId": "[a-zA-Z0-9]{16}"
    },
    "header" : {
        "isSuccessful" : true,
        "resultCode": 0,
        "resultMessage" : "SUCCESS"
    }
}
```

#### 공통 메시지
"content"에 아래 표대로 메시지를 작성하면, 각 푸시 타입에 맞게 메시지가 생성되어 발송된다.

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
|mutable-content| iOS | Optional, String | - | aps.mutable-content | - |
| messageDeliveryReceipt | Android, <br/>iOS, <br/> Tencent | Unnecessary | - | - | - |
| messageDeliveryReceiptData | Android, <br/>iOS, <br/> Tencent | Unnecessary | - | - | - |

Reserved Word는 메시지 생성시 Platform 별로 알맞는 위치에 설정된다. 사용자가 임의로 데이터 타입과 위치등을 변경할 수 없다.
그 외 사용자가 정의한 Word는 다음과 같이 Custom Key/Value 필드에 들어간다.

|Word|	Platform|	Usage|	GCM|	APNS|	TENCENT|
|---|---|---|---|---|---|
|customKey|	Android, <br/> iOS, <br/> Tencent|	Optional, <br/> Object, <br/> Array, <br/> String, <br/> Number|	data.customKey|	customKey|	custom_content.customKey|

##### "content" Example

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
            "customKey": "value"
        },
        "ko" : {
            "title": "제목",
            "body": "내용"
            "customKey": "값"
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
        "customKey": "값"
    }
}
"ja" GCM 메시지
 {
    "data": {
        "title": "タイトル",
        "body": "プッシュ・メッセージ",
        "customKey": "value"
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
    "customKey": "값"

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
    "customKey": "value"
}
"ko" TENCENT 메시지
 {
	"title": "제목",
	"body": "내용",
	"custom_content": {
		"customKey": "값"
	}
}
"ja" TENCENT 메시지
 {
	"title": "タイトル",
	"body": "プッシュ・メッセージ",
	"custom_content": {
		"customKey": "value"
	}
}
```

#### 메시지 수신, 확인 통계 조회
메시지 수신, 확인 수집(Message Delivery Receipt) 기능을 화성화 시키고, v1.4 이상 SDK를 적용하면 발송한 메시지에 대해 수신, 확인 정보를 확인할 수 있다.
수집된 정보를 통계 API로 조회할 수 있다. 기능은 [CONSOLE] > [Settings] 탭에서 활성화 시킬 수 있다.

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/statistics/message-delivery-receipts?from={from}&to={to}&event={event}&messageId={messageId}
HEADER
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| from | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| event | Optional, String | 'SENT', 'SENT_FAILED', 'RECEIVED', 'OPENED' |
| messageId | Optional, Number | 메시지 아이디 |

##### Request Body
```
없음
```

##### Response Body

```
{
	"messageDeliveryReceiptStatistics" : [{
			"dateTime" : "2016-07-11 17:50:00.00+9:00",
			"SENT" : 13,
			"SENT_FAILED": 0,
			"RECEIVED" : 12,
			"OPENED" : 10
		}
	],
	"header" : {
		"isSuccessful" : true,
		"resultCode" : 0,
		"resultMessage" : "SUCCESS"
	}
}
```

| Field | Usage | Description |
| - | - | - |
| dateTime | Optional, DateTime String | ISO 86091 |
| SENT | Optional, Number | 서버에서 발송한 수 |
| RECEIVED | Optional, Number | 기기에서 수신한 수 |
| OPENED | Optional, Number | 기기에서 사용자가 클릭해 오픈한 수 |

#### v1.3 API와 차이
- 메시지 발송 API로 발송된 메시지는 발송 내역을 남기지 않는다. 다만, CONSOLE에서 발송하는 메시지는 내역을 남긴다.
- 모든 메시지는 메시지 아이디를 발급 받는다.
- target.type 'CHANNEL'은 더 이상 지원하지 않는다.
- MPS(Message Per Second)는 더 이상 지원하지 않는다.
- 메시지 조회시 기간(from, to)을 필터링할 수 있다.
- 메시지 조회시 메시지 상태(messageStatus)를 필터링할 수 있다.
- 등록일시(createdDateTime), 완료일시(completedDateTime) 필드가 추가되었다.
- 메시지 수신, 확인 통계 조회 API가 추가되었다.

### 유효하지 않는 토큰

#### 유효하지 않는 토큰 조회
##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/invalid-tokens?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&messageId={messageId}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| pageIndex | Optional, Number | 기본 값 0 |
| pageSize | Optional, Number | 기본 값 25, 최대 값 100 |
| from | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| messageId | Optional, Number | 유효하지 않는 토큰이 발생한 메시지 아이디 |

###### Request Body
```
없음
```
###### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"invalidTokens" : [{
			"feedbackId" : 0,
			"messageId" : 0,
			"uid" : "uid",
			"token" : "invalid-token",
			"pushType" : "GCM",
			"createdDateTime" : "2017-02-08T19:39:04.000+09:00"
		}
	]
}
```

#### v1.3 API와 차이
- Feedback API에서 Invalid Tokens API로 이름이 변경되었다.
- 페이징과 기간, 메시지 아이디로 필터링할 수 있다.

### 예약 메시지

#### 예약 메시지 발송 스케줄 생성

##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/schedules
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |

##### Request Body

```json
{
	"type" : "EVERY_MONTH",
	"fromDate" : "2016-12-30",
	"toDate" : "2017-01-02",
	"times" : [
		"12:00",
		"17:00"
	],
	"days" : [
		1,
		15
	]
}
```

| Field | Usage | Description |
| - | - | - |
| type | Required, String | 'EVERY_DAY' (매일), 'EVERY_WEEK' (매주), 'EVERY_MONTH' (매월) |
| fromDate | Required, Date String | 예약 메시지 시작 년월일 (YYYY-MM-DD) |
| toDate | Required, Date String | 예약 메시지 종료 년월일 (YYYY-MM-DD) |
| times | Required, Time String | 예약 메시지 발송 시분 (hh:mm) |
| days | Optional, Number Array | 'EVERY_WEEK' (1: 월, 2: 화, 3: 수, 4: 목, 5: 금, 6: 토, 7: 일), <br/>'EVERY_MONTH' (1, 2, ..., 31: 1일, 2일, ..., 31일) |

##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"schedules" : [
		"2016-12-01T12:00",
		"2016-12-01T17:00",
		"2016-12-15T12:00",
		"2016-12-15T17:00",
		"2017-01-01T12:00",
		"2017-01-01T17:00",
		"2017-01-15T12:00",
		"2017-01-15T17:00",
		"2017-02-01T12:00",
		"2017-02-01T17:00",
		"2017-02-15T12:00",
		"2017-02-15T17:00"
	]
}
```

| Field | Usage | Description |
| - | - | - |
| schedules | - | 일시 (ISO 86091, e.g. YYYY-MM-DDThh:mm) |

#### 예약 메시지 등록
##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/reservations
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |

##### Request Body
```json
{
	"schedules" : [
		"2016-12-30T12:40",
		"2016-12-31T12:40"
	],
	"isLocalTime" : false,
	"target" : {
        "type" : "UID",
        "to": ["uid-1", "uid-2"]
    },
    "content" : {
        "default" : {
            "title": "title",
            "body": "body"
        }
    },
    "messageType" : "AD",
    "contact": "1588-1588",
    "removeGuide": "매뉴 > 설정",
    "timeToLiveMinute": 1,
	"provisionedResourceId": "id"
}
```

| Field | Usage | Description |
| - | - | - |
| schedules | Required, DateTime String Array | 예약 메시지 발송 스케줄 목록 |
| isLocalTime | Required, Boolean | 현지 시간 발송 여부 |

##### Response Body

```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"reservation" : {
		"reservationId": 666810348995587,
		"reservationIdString": "666810348995587"
	}
}
```

| Field | Usage | Description |
| - | - | - |
| reservationId | Number | 예약 메시지 아이디 |
| reservationIdString | String | 예약 메시지 아이디 문자열 |


#### 예약 메시지 목록 조회

##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/reservations?pageIndex={pageIndex}&pageSize={pageSize}&reservationStatus={reservationsStatus}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| pageIndex | Optional, Number | 기본 값 0 |
| pageSize | Optional, Number | 기본 값 25, 최대 값 100 |
| from | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| reservationStatus | Optional, String | 'RESERVED', 'COMPLETE' |

##### Request Body
```
없음
```

##### Response Body

```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"totalCount" : 1,
	"reservations" : [{
			"reservationId" : 666810348995587,
			"reservationIdString" : "666810348995587",
			"schedules" : [{
					"scheduleId" : 2455708,
					"deliveryTime" : "2016-12-30T12:40:00.000+09:00",
					"timezoneOffset" : 0,
					"scheduleStatus" : "READY",
					"reservationId" : 666810348995587
				}
			],
			"isLocalTime" : false,
			"target" : {
				"type" : "UID",
				"to" : [
					"uid"
				]
			},
			"content" : {
				"default" : {
					"title" : "default title",
					"body" : "default body"
				},
				"ko" : {
					"title" : "한국어 제목",
					"body" : "한국어 내용"
				}
			},
			"messageType" : "NOTIFICATION",
			"timeToLiveMinute" : 60,
			"createdDateTime" : "2016-12-30T10:34:40.000+09:00",
			"updatedDateTime" : "2016-12-30T10:34:40.000+09:00",
			"completedDateTime" : "2016-12-30T10:34:40.000+09:00",
			"reservationStatus" : "RESERVED"
		}
	]
}

```

| Field | Usage | Description |
| - | - | - |
| totalCount | - | 등록된 전체 예약 메시지 수 |
| reservationIdString | - | 예약 메시지 아이디 스트링 |
| createdDateTime | - | 예약 메시지 등록 일시 (ISO 86091) |
| updatedDateTime | - | 예약 메시지 수정 일시 (ISO 86091) |
| completedDateTime | - | 예약 메시지 발송 완료 일시, 완료가 안되었다면 현재 시간 표시 (ISO 86091) |
| reservationStatus | - | 'RESERVED', 'COMPLETED' |
| schedules.scheduleId | - | 예약 메시지 발송 스케줄 아이디 |
| schedules.deliveryTime | - | 예약 메시지 발송 일시 |
| schedules.timezoneOffset | - | 예약 메시지 발송 타임존, 현지 시간 발송시 설정 |
| schedules.scheduleStatus | - | 'READY', 'SENDING', 'CANCELED', 'DONE' 예약 메시지 발송 스케줄 상태 |

#### 예약 메시지 조회

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/reservations/{reservation-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

##### Request Body
```
없음
```

##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"totalCount" : 1,
	"reservation" : {
		"reservationId" : 666810348995587,
		"reservationIdString" : "666810348995587",
		"schedules" : [{
				"scheduleId" : 2455708,
				"deliveryTime" : "2016-12-30T12:40:00.000+09:00",
				"timezoneOffset" : 0,
				"scheduleStatus" : "READY",
				"reservationId" : 666810348995587
			}
		],
		"isLocalTime" : false,
		"target" : {
			"type" : "UID",
			"to" : [
				"uid"
			]
		},
		"content" : {
			"default" : {
				"title" : "default title",
				"body" : "default body"
			},
			"ko" : {
				"title" : "한국어 제목",
				"body" : "한국어 내용"
			}
		},
		"messageType" : "NOTIFICATION",
		"timeToLiveMinute" : 60,
		"createdDateTime" : "2016-12-30T10:34:40.000+09:00",
		"updatedDateTime" : "2016-12-30T10:34:40.000+09:00",
		"completedDateTime" : "2016-12-30T10:34:40.000+09:00",
		"reservationStatus" : "RESERVED"
	}
}
```

| Field | Usage | Description |
| - | - | - |
| updatedDateTime | DateTime String | 예약 수정 일시(ISO 86091) |

#### 예약 메시지 수정

##### Method, URL, Headers
```
POST /push/v2.0/appkeys/{appkey}/reservations/{reservationId}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

##### Request Body
```json
{
	"schedules" : [
		"2016-12-30T10:05",
		"2016-12-31T12:40"
	],
	"target" : {
		"type" : "UID",
		"to" : [
			"uid"
		]
	},
	"content" : {
		"default" : {
			"title" : "default title",
			"body" : "default body"
		},
		"ko" : {
			"title" : "한국어 제목",
			"body" : "한국어 내용"
		}
	},
	"isLocalTime" : false,
	"messageType" : "NOTIFICATION"
}
```

##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	}
}
```

#### 예약 메시지 삭제

##### Method, URL, Headers
```
DELETE /push/v2.0/appkeys/{appkey}/reservations?reservationIds={reservationId,}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| reservationIds | Required, Number Array | ','로 구분, e.g. reservationIds=1,2 |

##### Request Body
```
없음
```

##### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	}
}
```

#### 발송된 예약 메시지 조회

##### Method, URL, Headers
```
GET /push/v2.0/appkeys/{appkey}/reservations/{reservation-id}/messages?pageIndex={pageIndex}&pageSize={pageSize}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| reservationId | Required, Number | 예약 메시지 아이디 |
| pageIndex | Optional, Number | 기본 값 0 |
| pageSize | Optional, Number | 기본 값 25, 최대 값 100 |

##### Request Body
```
없음
```

##### Response Body
```
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "success",
		"isSuccessful" : true
	},
	"messages" : [{
			"messageId" : 356125922591162,
			"messageIdString" : "356125922591162",
			"target" : {
				"type" : "ALL",
				"pushTypes" : ["GCM", "APNS", "APNS_SANDBOX", "TENCENT"]
			},
			"content" : {
				"default" : {
					"title" : "6시 55분 예약 메시지",
					"body" : "API v2"
				}
			},
			"messageType" : "NOTIFICATION",
			"createdDateTime" : "2017-04-05T18:55:00.000+09:00",
			"completedDateTime" : "2017-04-05T18:55:00.000+09:00",
			"targetCount" : 38,
			"sentCount" : 38,
			"messageStatus" : "COMPLETE"
		}
	],
	"totalCount" : 1
}
```
