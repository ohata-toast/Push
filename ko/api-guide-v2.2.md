## Notification > Push > API v2.2 Guide
### v2.2 API 소개

#### 추가
- '로그 조회' API가 추가되었습니다.

#### 수정
- API 인증시 사용하는 값이 변경되었습니다.
- API 인증시 Secret Key 대신 User Access Key ID와 Secret Access Key를 사용해야 합니다.
- 자세한 설명은 아래 'API 보안 설정' 항목에서 확인할 수 있습니다.

### 기본 정보
#### Endpoint
```
API Endpoint: https://api-push.cloud.toast.com
메시지 수신/확인 여부 수집 Endpoint: https://collector-push.cloud.toast.com
```
#### API 보안 설정
- API 보안 설정은 User Access Key ID (NHN Cloud 서비스 이용시, 사용자 인증을 위해 필요한 사용자 설정 키) 를 발급하는 기능입니다.
- User Access Key ID는 NHN Cloud ID에 5개까지 발급 가능합니다. 보안을 위해 발급된 키는 안전한 장소에 보관해 주세요.
- User Access Key ID는 90일마다 변경하기를 권장합니다.
- v2.2 API부터 Secret Key 대신 User Access Key ID를 이용해 호출해야 합니다.
```
Header
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```
[회원정보] > [API 보안 설정] 에서 생성할 수 있습니다.


#### Response

##### Response HTTP Status Code
200 OK.  
모든 API 요청에 대해 200 OK로 응답합니다.  
자세한 응답 결과는 Response Body의 Header를 통해 알 수 있습니다.  

##### Response Header

```
{
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```
##### resultCode, resultMessage

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
| false | 50001 ~ 50501 | Internal Error. Please report this. 'http://cloud.toast.com/support/faq'. |
| false | 400 | Client Error. 태그 API에서 발생한 클라이언트 오류입니다  |
| false | 500 | Internal Error. 태그 API에서 발생한 내부 오류입니다 |

## 토큰
### 생성
- 클라이언트에서 조회 가능합니다.

##### Method, URL
```
POST /push/v2.2/appkeys/{appkey}/tokens
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
  "language": "ko",
  "deviceId": "X3LOdJSQdNzCCvcbiSPZTGK1M9srPU5EumRD"
}
```

|Field|	Usage | Description |
|---|---|---|
|token|	Required, String|	토큰, 최대 1,600 글자|
|oldToken|	Optional, String|	기존 토큰, 최대 1,600 글자 |
|pushType|	Required, String| 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
|isNotificationAgreement|	Required, Boolean|	true or false|
|isAdAgreement|	Required, Boolean|	true or false|
|isNightAdAgreement| Required, Boolean|	true or false|
|timezoneId|	Required, String|	Area/Name. IANA time zone database.|
|country|	Required, String|	ISO 3166-1 alpha-2, ISO 3166-1 alpha-3, 3 글자|
|language|	Required, String|	ISO 639-1, ISO 639-2, iOS(language code + script code), 8 글자|
|uid|	Required, String|	사용자 아이디, emoji 허용 안함, 최대 64 글자|
|deviceId|	Optional, String|	디바이스 아이디, 36 글자|


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

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/tokens -d '{"oldToken":"oldToken","token":"token","isNotificationAgreement":true,"isAdAgreement":true,"isNightAdAgreement":true,"pushType":"GCM","timezoneId":"Asia/Seoul","uid":"uid","country":"KR","language":"ko"}'
```

##### Description

- 토큰이 이미 등록되어 있는 경우에 다시 등록하면, 기존 정보를 업데이트 합니다.
- 만약, 토큰이 변경된다면 oldToken에 기존 토큰을, token에 새로운 토큰을 설정하고 등록하면 새로운 토큰으로 업데이트됩니다.
- "isNotificationAgreement"는 푸시 메시지 수신 동의 여부, "isAdAgreement"는 광고성 푸시 메시지 수신 여부, "isNightAdAgreement"는 야간 광고성 푸시 메시지 수신 여부를 나타냅니다.
- 예로, 모든 푸시 메시지 수신을 원할 경우, 필드 3개를 모두 true로 설정하면 됩니다. 푸시 메시지만 수신할 경우, "isNotificationAgreement"만 true로 설정하면 됩니다.
- 수신 동의 여부는 정보통신망법 규정(제50조부터 제50조의 8)을 따른다.  
    - [KISA 가이드 바로 가기](https://spam.kisa.or.kr/spam/na/ntt/selectNttInfo.do?mi=1020&nttSn=1171&bbsId=1002)    
    - [법령 바로 가기](http://www.law.go.kr/lsEfInfoP.do?lsiSeq=123210#)  
- 네트워크 상태가 좋지 않거나 여러 이유로 응답 지연이 발생할 수 있습니다. 모바일 애플리케이션 구동에 영향을 최소화하기 위해 Timeout을 짧게 설정하고, 구동될 때마다 토큰을 등록하는 것이 좋습니다.
- 토큰은 보안적인 이슈, 앱 업데이트, 삭제 등 여러가지 이유로 재발급될 수 있습니다. 자주 변경되는 것은 아니지만, 수신율을 높이기 위해 구동될 때 마다 최신 토큰을 등록하는 것이 좋습니다.
- 앱 삭제 등으로 토큰이 만료되어도 바로 GCM, APNS 서버에 적용되지 않아, 앱 삭제 후 푸시 메시지를 발송했을 때 발송이 성공할 수 있습니다.

### 조회
#### 토큰으로 토큰 조회
- 클라이언트에서 조회 가능합니다.
##### Method, URL

```
GET /push/v2.2/appkeys/{appkey}/tokens/{token}?pushType={pushType}
Content-Type: application/json;charset=UTF-8
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| pushType | Required, String | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |

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
		"token" : "Token",
		"adAgreementDateTime": "2017-08-12T01:04:19.000+09:00",
		"nightAdAgreementDateTime": "2017-08-12T01:04:19.000+09:00",
		"deviceId": "X3LOdJSQdNzCCvcbiSPZTGK1M9srPU5EumRD",
		"activatedDateTime": "2017-08-12T01:04:19.000+09:00"
	},
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```

| Field | Usage | Description |
| - | - | - |
| adAgreementDateTime | -, DateTime String | 홍보성 푸시 메시지 수신 동의 일시 |
| nightAdAgreementDateTime | -, DateTime String | 야간 홍보성 푸시 메시지 수신 동의 일시 |
| deviceId | -, String | 디바이스 아이디 |
| activatedDateTime | -, Datetime String | 토큰의 최근 등록 요청 일시 |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/tokens/TOKEN?pushType=GCM
```

#### 사용자 아이디로 토큰 조회
- Secret Key가 필요한 API이며, 서버에서 호출되어야 합니다.
##### Method, URL

```
GET /push/v2.2/appkeys/{appkey}/tokens?uid={uid}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
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
		"token" : "Token",
        "adAgreementDateTime": "2017-08-12T01:04:19.000+09:00",
		"nightAdAgreementDateTime": "2017-08-12T01:04:19.000+09:00",
		"deviceId": "X3LOdJSQdNzCCvcbiSPZTGK1M9srPU5EumRD",
		"activatedDateTime": "2017-08-12T01:04:19.000+09:00"
	}],
	"header" : {
		"isSuccessful" : true,
		"resultCode": 0,
		"resultMessage" : "SUCCESS"
	}
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/tokens?uid=uid
```

#### 유효하지 않는 토큰 조회
##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/invalid-tokens?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&messageId={messageId}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| pageIndex | Optional, Number | 기본값 0 |
| pageSize | Optional, Number | 기본값 25, 최댓값 100 |
| from | Optional, DateTime String | 최근 30일 까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | 최근 30일 까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| messageId | Optional, Number | 유효하지 않는 토큰이 발생한 메시지 아이디 |

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
	"invalidTokens" : [{
			"messageId" : 0,
			"uid" : "uid",
			"token" : "invalid-token",
			"pushType" : "GCM",
			"createdDateTime" : "2017-02-08T19:39:04.000+09:00"
		}
	]
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/invalid-tokens
```


#### 토큰 속성 통계 조회 API

**Fade-out 된 API입니다. v2.4 이상의 API를 이용해 주세요.**

##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/statistics/token-properties?from={from}&to={to}&tokenProperties={tokenProperties}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| from | Optional, DateTime String | 최근 30일 까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | 최근 30일 까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| tokenProperties | Optional, String Array | 'agreement', 'country', 'language', 'timezoneId'<br/>','로 구분, e.g. tokenProperties=country,language |

##### Request Body
```
없음
```

##### Response Body
```json
{
	"tokenPropertiesStatistics" : [{
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
			"timezoneIds": {
				"Asia/Seoul": 260
			},
			"agreements": {
				"ON": 260
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
			"timezoneIds": {
				"Asia/Seoul": 260
			},
			"agreements": {
				"ON": 260
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
| countries.XX | String | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3, 3 글자 |
| languages.XX | String | ISO 639-1, ISO 639-2, iOS(language code + script code), 8 글자 |
| timezoneIds.XX | String | Area/Name. IANA time zone database |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/statistics/token-properties
```

#### 토큰 등록 통계 조회

**Fade-out 된 API입니다. v2.4 이상의 API를 이용해 주세요.**

##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/statistics/token-registrations?from={from}&to={to}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| from | Optional, DateTime String | 최근 30일 까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | 최근 30일 까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |

##### Request Body
```
없음
```

##### Response Body
```json
{
	"tokenRegistrationStatistics" : [{
			"dateTime" : "2016-07-11 17:50:00.00+9:00",
			"registered" : 90,
			"deleted" : 20
		}, {
			"dateTime" : "2016-07-11 17:51:00.00+9:00",
			"registered" : 45,
			"deleted" : 10
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
| registered | Number | 등록된 토큰 수 |
| deleted | Number | 삭제된 토큰 수 |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/statistics/token-registrations
```

## 메시지
### 발송
※ API로 발송한 푸시 메시지는 콘솔과 단건, 목록 조회 API에서 조회할 수 없습니다. API로 발송한 푸시 메시지는 Logging 기능을 활성화한 후 로그 조회 API를 이용해 주세요.
##### Method, URL, Headers
```
POST /push/v2.2/appkeys/{appkey}/messages
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
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
    "provisionedResourceId": "id",
    "adWordPosition": "TITLE"
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

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| target.type | Required, String | 'ALL', 'UID', 'TAG' 수신 타겟 타입 |
| target.to | Optional, String Array | target.type이 수신자 UID 목록(최대 10,000 개) 또는 TAG 조건 |
| target.pushTypes | Optional, String Array | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
| target.countries | Optional, String Array | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3 (최대 3 글자) |
| content | Required, Map | 수신자에게 전달될 내용 (최대 8,192 글자) |
| content.default | Required, Map | '자세한 내용은 아래 공통 메시지 형식' 참고 |
| content.default.title | Optional, String |  |
| content.default.body | Optional, String |  |
| messageType | Required, String | NOTIFICATION, AD |
| contact | Optional, String | messageType이 AD이면 경우 필수, 숫자(0-9)와 하이픈(Hypen, -)만 가능합니다. |
| removeGuide | Optional, String | messageType이 AD이면 경우 필수 |
| timeToLiveMinute | Optional, Number | 단위는 분입니다. 범위는 1에서 60까지다. 기본값은 10 입니다. |
| provisionedResourceId | Optional, String | 할당 받은 전용 리소스(provisioned Resource) 아이디입니다. 사용 문의 support@cloud.toast.com |
| adWordPosition | Optional, String | 'TITLE', 'BODY' 광고 표시 문구 위치. 기본값은 'TITLE' 입니다. |

##### Description
- "target.type"에 'UID'로 설정시 "target.to"에 최대 10,000 개까지 UID를 설정할 수 있습니다.
- "target.type"에 'TAG'로 설정시 "target.to"에 태그 아이디와 3개의 조건과 1개의 괄호('()')를 넣은 조건을 설정할 수 있습니다.
    - 예, 남자, 30대 태그가 붙었거나 여자 태그가 붙은 대상에게 메시지를 발송한다면,    
    "target.to=(,남자_ID,AND,30대_ID,),OR,여자_ID"로 설정할 수 있습니다.
- "target.pushTypes" 필드로 특정 푸시 타입으로만 메시지를 발송할 수 있습니다.
만약, 정의하지 않으면 모든 푸시 타입, GCM, APNS, APNS_SANDBOX, TENCENT, ADM로 발송합니다.
- "target.countries" 필드가 "['KR', 'JP']"면 토큰 국가 코드가 "KR" 또는 "JP"인 Token에 발송합니다.
- "content.default" 필드는 필수이며, "content" 필드에 대한 자세한 내용은 아래 [공통 메시지 포맷]을 참고 바랍니다.
- 메시지를 광고 타입, "messageType": "AD", 으로 보낼 경우, "contact", "removeGuide" 필드를 반드시 포함해야 합니다.
"contact" 필드에 연락처를 입력해야 하며, "removeGuide" 필드에 수신 철회 방법에 대해 입력해야 합니다.
- timeToLiveMinute 필드를 설정하면, 설정한 시간 이상 발송이 지연되는 경우 자동으로 실패 처리됩니다.

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/messages -d '{"target":{"type":"UID","to":["uid"]},"content":{"default":{"title":"title","body":"body","customKey1":"It is default"},"ko":{"title":"제목","body":"내용","customKey2":"한국어 입니다."}},"messageType":"AD","contact":"1588-1588","removeGuide":"매뉴 > 설정","timeToLiveMinute":1}'
```

### 공통 메시지
"content"에 아래 표대로 메시지를 작성하면, 각 푸시 타입에 맞게 메시지가 생성되어 발송됩니다.

|Reserved Word|	Platform|	Usage|	GCM|	APNS|	TENCENT| ADM |
|---|---|---|---|---|---|---|
|title|	Android, <br/> iOS Watch, <br/> Tencent, <br/> ADM|	Optional, String|	data.title|	aps.alert.title|	title| data.title |
|body|	Android, <br/> iOS, <br/> Tencent, <br/> ADM|	Optional, String|	data.body|	aps.alert.body|	content| data.body|
|title-loc-key|	iOS|	Optional, String| - | aps.alert.title-loc-key| - | - |
|title-loc-args|	iOS|	Optional, Array of Strings| - | aps.alert.title-loc-args	| - | - |
|action-loc-key|	iOS|	Optional, String| - |aps.alert.action-loc-key	| - | - |
|loc-key|	iOS|	Optional, String| - |aps.alert.loc-key	| - | - |
|loc-args|	iOS|	Optional, Array of String	| - | aps.alert.loc-args	| - | - |
|launch-image|	iOS|	Optional, String	| - | aps.alert.launch-image	| - | - |
|badge|	iOS|	Optional, Number| - | aps.badge	| - | - |
|sound|	Android, <br/> iOS, <br/> Tencent, <br/> ADM|	Optional, String|	data.sound|	aps.sound|	custom_content.sound| data.sound |
|content-available|	iOS|	Optional, String	| - | aps.content-available	| - | - |
|category|	iOS|	Optional, String	| - | aps.category	| - | - |
|mutable-content| iOS | Optional, String | - | aps.mutable-content | - | - |
|consolidationKey| ADM | Optional, String | - | - | - | consolidationKey |
|expiresAfter| ADM | Optional, Number | - | - | - | expiresAfter |
|messageDeliveryReceipt| Android, <br/>iOS, <br/> Tencent | Unnecessary | - | - | - | - |
|messageDeliveryReceiptData| Android, <br/>iOS, <br/> Tencent | Unnecessary | - | - | - | - |

Reserved Word는 메시지 생성 시 Platform별로 알맞는 위치에 설정됩니다. 사용자가 임의로 데이터 타입과 위치 등을 변경할 수 없습니다.
그 외 사용자가 정의한 Word는 다음과 같이 Custom Key/Value 필드에 들어갑니다.

|Word|	Platform|	Usage|	GCM|	APNS|	TENCENT| ADM|
|---|---|---|---|---|---|---|
|customKey|	Android, <br/> iOS, <br/> Tencent|	Optional, <br/> Object, <br/> Array, <br/> String, <br/> Number|	data.customKey|	customKey|	custom_content.customKey| data.customKey|

### 메시지 발송 예제

- 메시지 발송 API의 요청 본문(Request Body)의 content.default은 필수입니다.

#### 1. 전체에게 발송
등록된 모든 대상에게 메시지를 발송하는 예제입니다.

##### Request Body
```json
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
##### Description
- target.type을 'ALL'로 설정하면, 모든 토큰에 메시지를 발송합니다.

#### 2. 특정 사용자에게 발송
사용자 아이디를 입력해 특정 사용자에게 메시지를 발송하는 예제입니다.

##### Request Body
```json
{
    "target" : {
        "type" : "UID",
        "to": ["uid-01", "uid-02"]
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
##### Description
- target.type을 'UID'로 설정하고, target.to에 사용자 아이디를 설정해 특정 사용자에게 메시지를 발송합니다.

#### 3. 일부 국가나 푸시 타입의 사용자들에게 발송
특정 국가나 기기(Android, iOS, ...)를 사용하는 사용자들에게만 메시지를 발송하는 예제입니다.

##### Request Body
```json
{
    "target" : {
        "type" : "ALL",
        "countries": ["KR", "JP"],
        "pushTypes": ["GCM", "APNS"]
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
##### Description
- target.countries에 국가 코드, target.pushTypes에 푸시 타입을 설정해 조건에 만족하는 사용자에게 메시지를 발송합니다.

#### 4. 푸시 타입별 메시지 변환
메시지를 보내게되면 푸시 타입별로 메시지가 변환되어 발송되는데, 변환되는 규칙을 설명한 예제입니다.

##### Request Body
```json
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
        }
    },
    "messageType" : "NOTIFICATION"
}
```

##### GCM(Android)에 수신되는 메시지
```json
{
	"data": {
		"title": "title",
		"body": "body",
		"customKey": "value"
	}
}
```
##### APNS(iOS)에 수신되는 메시지
```json
{
    "aps": {
        "alert": {
            "title": "title",
            "body": "body"
        },
        "badge": 1
    },
    "customKey": "value"

}
```
##### TENCENT(Android)에 수신되는 메시지
```json
 {
    "title": "title",
    "body": "body",
    "custom_content": {
        "customKey": "value"
    }
}
```

##### ADM(Fire OS)에 수신되는 메시지
```json
{
	"data": {
		"title": "title",
		"body": "body",
		"customKey": "value"
	}
}
```

##### Description
- content에 입력한 메시지 내용은 각 푸시 타입에 맞게 변환되어 발송됩니다.
- title, body와 같은 예약어들은 푸시 타입에 맞는 메시지로 변환시 지정된 위치에 설정되어 발송됩니다.
그외 사용자가 정의한 필드들은 각 푸시 타입의 Custom Key 위치에 설정됩니다.
- badge, consolidationKey와 같이 특정 푸시 타입에만 정의된 예약어는 다른 푸시 타입에서는 제외됩니다.
예로, badge는 APNS(iOS) 메시지에만 설정되며, GCM, TENCENT, ADM에는 제외됩니다.

#### 5. 광고성 메시지
광고성 메시지로 발송하면 메시지 내용에 추가되는 광고 문구에대한 예제입니다.

##### Request Body
```json
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "title": "금요일 특별 이벤트",
            "body": "지금 주문하시면 50% 할안된 가격으로!"
        }
    },
    "messageType" : "AD",
    "contact": "1588",
    "removeGuide": "메뉴 > 알림 설정"
}
```

##### GCM(Android), ko(한국어)에 수신되는 메시지
```json
 {
    "data": {
        "title": "(광고) 금요일 특별 이벤트 1588",
        "body": "지금 주문하시면 50% 할안된 가격으로!\n메뉴 > 알림 설정"
    }
}
```
##### APNS(iOS), ko(한국어)에 수신되는 메시지
```json
{
    "aps": {
        "alert": {
            "title": "(광고) 금요일 특별 이벤트 1588",
            "body": "지금 주문하시면 50% 할안된 가격으로!\n메뉴 > 알림 설정"
        }
    }
}
```
##### GCM(Android), ja(일본어)에 수신되는 메시지
```json
 {
    "data": {
        "title": "금요일 특별 이벤트",
        "body": "지금 주문하시면 50% 할안된 가격으로!"
    }
}
```
##### APNS(iOS), ja(일본어)에 수신되는 메시지
```json
{
    "aps": {
        "alert": {
            "title": "금요일 특별 이벤트",
            "body": "지금 주문하시면 50% 할안된 가격으로!"
        }
    }
}
```
##### Description
- 광고성 메시지를 발송하기 위해서는 messageType을 AD(광고)로 설정하고, contact와 removeGuide에 대표 번호와 수신 동의 철회 방법을 입력해야 됩니다.
- 각 푸시 타입별로 메시지가 발송될때, title에 광고 표시 문구와 대표 번호가, body에 수신 동의 철회 방법이 추가되어 발송됩니다.
- 광고성 메시지는 언어 코드가 한국어(ko, ko-)인 사용자들에게만 광고 문구가 추가됩니다. 위 예처럼 해외 사용자(일본어)들에게는 광고 문구가 추가되지 않습니다.

#### 6. 다국어 메시지
다양한 언어로 메시지를 발송하는 예제입니다.

##### Request Body
```json
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "title": "title",
            "body": "body",
            "customKey": "value"
        },
        "ko" : {
            "title": "제목",
            "body": "내용",
            "customKey": "'ko', 'ko-'로 시작하는 언어 코드에 설정됩니다."
        },
        "ja" : {
            "title": "タイトル",
            "body": "プッシュ・メッセージ"
        }
    },
    "messageType" : "NOTIFICATION"
}
```

##### GCM(Android), ko(한국어)에 수신되는 메시지
```json
{
    "data": {
        "title": "제목",
        "body": "내용",
        "customKey": "'ko', 'ko-'로 시작하는 언어 코드에 설정됩니다."
    }
}
```
##### GCM(Android), ko-KR(한국어)에 수신되는 메시지
```json
{
    "data": {
        "title": "제목",
        "body": "내용",
        "customKey": "'ko', 'ko-'로 시작하는 언어 코드에 설정됩니다."
    }
}
```
##### GCM(Android), ja(일본어)에 수신되는 메시지
```json
{
    "data": {
        "title": "タイトル",
        "body": "プッシュ・メッセージ",
        "customKey": "value"
    }
}
```
##### GCM(Android), en(영어)에 수신되는 메시지
```json
{
    "data": {
        "title": "title",
        "body": "body",
        "customKey": "value"
    }
}
```
##### Description
- content 하위에 각 언어 코드에 대한 메시지를 입력하면, 토큰의 언어 코드와 일치하거나 유사한 언어의 메시지로 변환되어 발송됩니다.
토큰의 언어 코드와 매칭되는 언어 코드가 없다면, default의 내용이 발송됩니다. 언어 코드가 en(영어)인 사용자에게는 conent.default의 내용이 발송됩니다.
- 토큰의 언어 코드와 완벽히 일치하지 않아도, 언어 코드의 유사도를 비교해 최대한 가까운 언어로 변환합니다.
요청 본문에 content.ko만 입력되어 있지만, 언어 코드가 ko-KR(한국어)인 사용자에게도 content.ko의 내용이 발송됩니다.
- customKey는 content.ja에 정의되어 있지 않기 때문에, content.default의 값으로 발송됩니다. 공통적인 내용은 content.default에 입력할 수 있습니다.

#### 7. 리치 메시지
메시지 발송시 'content'에 'richMessage' 필드를 정의하면 리치 메시지로 메시지를 발송할 수 있습니다.
공통 메시지, 광고성 메시지, 다국어 메시지와 함께 사용할 수 있습니다.
v1.7이상 SDK가 적용된 곳에서만 사용할 수 있습니다.

##### Request Body
```json
{
    "target" : {
        "type" : "ALL"
    },
    "content" : {
        "default" : {
            "title" : "title",
            "body" : "body",
            "richMessage" : {
                "buttons" : [{
                        "name" : "버튼 이름",
                        "buttonType" : "REPLY | DEEP_LINK | OPEN_APP | OPEN_URL | DISMISS",
                        "link" : "URL | ...",
                        "hint" : "메시지를 입력해주세요."
                    }
                ],
                "media" : {
                    "sourceType" : "REMOTE | LOCAL",
                    "source" : "URL | LOCAL_RESOURCE",
                    "mediaType" : "IMAGE | GIF | VEDIO | AUDIO",
                    "extension" : "jpg | png",
                    "expandable" : true
                },
                "largeIcon" : {
                    "sourceType" : "REMOTE | LOCAL",
                    "source" : "URL | LOCAL_RESOURCE"
                },
                "group" : {
                    "key" : "KEY",
                    "description" : "알림 모음"
                }
            }
        }
    },
    "messageType" : "NOTIFICATION"
}
```

| Field | Usage | Description |
| - | - | - |
| richMessage | Optional, Object | 리치 메시지 사용시 필요 |
| richMessage.buttons | Optional, Object Array | 리치 메시지에 추가되는 버튼, 최대 3개까지 가능 |
| richMessage.button.name | Required, String | 버튼 이름 |
| richMessage.button.buttonType | Required, String | 버튼 타입, REPLY, DEEP_LINK, OPEN_APP, OPEN_URL, DISMISS |
| richMessage.button.link | Required, String | 버튼을 눌렀을때, 연결되는 링크 |
| richMessage.button.hint | Required, String | 버튼에대한 힌트 |
| richMessage.media | Optional, Object | 리치 메시지에 추가되는 미디어 |
| richMessage.media.sourceType | Required, String | 미디어의 위치, REMOTE, LOCAL |
| richMessage.media.source | Required, String | 미디어의 위치한 곳의 주소 |
| richMessage.media.mediaType | Required, String | 미디어의 타입, IMAGE, GIF, VEDIO, AUDIO. Android에서는 IMAGE만 지원 |
| richMessage.media.extension | Required, String | 미디어 파일의 확장자 |
| richMessage.media.expandable | Required, Boolean | Android에서 미디어를 클릭 시 펼침 기능 사용 여부 |
| richMessage.largeIcon | Optional, Object | 리치 메시지에 추가되는 큰 아이콘, Android에서만 지원 |
| richMessage.largeIcon.sourceType | Required, String | 큰 아이콘의 위치, REMOTE, LOCAL |
| richMessage.largeIcon.source | Required, String | 미디어의 위치한 곳의 주소 |
| richMessage.group | Optional, Object | 여러 개의 메시지를 그룹 단위로 묶는 기능, Android에서만 지원 |
| richMessage.group.key | Required, String | 그룹의 키 |
| richMessage.group | Required, String | 그룹에대한 설명 |


### 조회
#### 목록 조회
※ 콘솔로 발송한 푸시 메시지만 목록 조회 API로 조회할 수 있습니다. API로 발송한 푸시 메시지는 Logging 기능을 활성화한 후 로그 조회 API를 이용해 주세요.
##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/messages?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&deliveryType={deliveryType}&messageStatus={messageStatus}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| pageIndex | Optional, Number | 기본값 0 |
| pageSize | Optional, Number | 기본값 25, 최댓값 100 |
| from | Optional, DateTime String | 최근 30일 까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| to | Optional, DateTime String | 최근 30일 까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD, 2018-04-24T06:00:00.000%2B09:00) |
| deliveryType | Optional, String | 'INSTANT'(즉시 발송), 'RESERVATION'(예약 발송) |
| messageStatus | Optional, String | 'READY', 'PROCESSING', 'COMPLETE', 'CANCEL_NO_TARGET', 'CANCEL_INVALID_CERTIFICATE', 'CANCEL_INVALID_MESSAGE', 'CANCEL_UNSUPPORTED_MESSAGE_TYPE', 'CANCEL_UNAUTHORIZED', 'CANCEL_UNKNOWN' |

##### Request Body
```
없음
```
##### Response Body
```json
{
    "header" : {
        "isSuccessful" : true,
        "resultCode": 0,
        "resultMessage" : "SUCCESS"
    },
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
        "timeToLiveMinute": 60,
        "createdDateTime": "2017-02-13T09:30:00.000+09:00",
        "completedDateTime": "2017-02-13T09:30:00.000+09:00",
        "targetCount": 1000,
		"sentCount": 1000,
        "messageStatus": "COMPLETE",
        "provisionedResourceId": "[a-zA-Z0-9]{16}"
    }],
    "toatalCount": 1
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/messages
```

| Field | Usage | Description |
| - | - | - |
| createdDateTime | - | 메시지가 생성된 일시 (ISO 8601) |
| completedDateTime | - | 메시지 발송이 완료된 일시 (ISO 8601) |
| targetCount | - | 발송될 타겟 토큰 수 |
| sentCount | - | 실제 발송된 토큰 수 |
| provisionedResourceId | - | 메시지가 발송된 전용 리소스 아이디 |
| totalCount | - | 필터링된 전체 메시지  수 |

- "messageStatus" 필드는 메시지 상태를 나타냅니다. 다음과 같은 상태가 있습니다.
    - READY: 메시지 발송 요청이 등록된 상태입니다.
    - PROCESSING: 메시지 생성이 끝나고, 대기 또는 발송 중입니다.
    - COMPLETE: 메시지 발송이 완료된 상태입니다.
    - CANCEL_NO_TARGET: 메시지 발송 대상이 없어서 취소된 상태입니다. 다음과 같은 이유로 발송이 취소될 수 있습니다.  
 등록된 토큰이 없을 때  
 광고 푸시 메시지의 경우, 수신 동의한 사용자가 없을 때  
 야간 광고 푸시 메시지(21시 ~ 8시)의 경우, 야간 광고 수신 동의한 사용자가 없을 때  
 기존 등록된 토큰들이 삭제되어 토큰이 없을 때    
    - CANCEL_INVALID_CERTIFICATE: 인증서가 잘못되어 취소된 상태입니다. 인증서 상태를 확인해야 합니다.
    - CANCEL_INVALID_MESSAGE: 메시지 형식이 맞지 않아 취소된 상태입니다.
    - CANCEL_UNSUPPORTED_MESSAGE_TYPE: 메시지 형식이 맞지 않아 취소된 상태입니다.
    - CANCEL_UNAUTHORIZED: 인증서 인증 과정에서 실패한 상태입니다. 인증서 상태를 확인해야 합니다.
    - CANCEL_UNKNOWN: 내부 오류가 발생한 상태입니다.

#### 단건 조회
※ 콘솔로 발송한 푸시 메시지만 단건 조회 API로 조회할 수 있습니다. API로 발송한 푸시 메시지는 Logging 기능을 활성화한 후 로그 조회 API를 이용해 주세요.
##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/messages/{message-id}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
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
        "timeToLiveMinute": 60,
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

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/messages/{messageId}
```

#### 실패한 메시지 목록 조회
발송에 실패한 메시지를 조회할 수 있습니다.
단, 토큰이 존재하지 않는 경우(INVALID_TOKEN)는 발송 실패로 판단하지 않습니다.

##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/message-errors?messageId={messageId}&messageErrorType={messageErrorType}&messagErrorCause={messageErrorCause}&from={from}&to={to}&limit={limit}
HEADER
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| messageId | Optional, Number | 메시지 아이디 |
| messageErrorType | Optional, String | 'CLIENT_ERROR', 'EXTERNAL_ERROR', 'INTERNAL_ERROR' |
| messageErrorCause | Optional, String | 'UNSUPPORTED_MESSAGE_TYPE', 'INVALID_MESSAGE', 'INVALID_CERTIFICATE', 'UNAUTHORIZED', 'EXPIRED_TIME_OUT', 'APNS_ERROR', 'GCM_ERROR', 'TENCENT_ERROR', 'AGENT_ERROR', 'ADM_ERROR'  |
| from | Optional, DateTime String | 최근 30일까지, 기본값은 최근 7일 전(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 최근 30일까지, 기본값은 현재(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| limit | Optional, Number | 한 번에 조회할 목록 크기, 기본값과 최댓값은 100 |
| pageNumber | Optional, Number | 페이지 번호, 기본값 1 |

##### Description
- messageErrorType와 messageErrorCause는 다음과 같은 뜻을 의미합니다.
    - CLIENT_ERROR: 클라이언트의 잘못된 요청
        - UNSUPPORTED_MESSAGE_TYPE: 지원하지 않는 메시지 타입
        - INVALID_MESSAGE: 비정상적인 메시지
        - INVALID_CERTIFICATE: 인증서 만료 또는 인증서 정보가 올바르지 않음
        - UNAUTHORIZED: 인증서 만료 또는 인증서 정보가 올바르지 않음
    - EXTERNAL_ERROR: APNS, GCM, Tencent, ADM 등 푸시와 연결된 외부 서비스 오류
        - APNS_ERROR: APNS(iOS)로 발송 실패
        - GCM_ERROR: GCM(Google)로 발송 실패
        - TENCENT_ERROR: Tencent로 발송 실패
        - ADM_ERROR: ADM로 발송 실패
    - INTERNAL_ERROR: 푸시 내부에서 발생한 오류
        - EXPIRED_TIME_OUT: 발송 지연으로 인한 메시지 유효 시간 만료
        - AGENT_ERROR: Agent 내부 오류로 인한 발송 실패
- Response Body에서 header.resultCode가 40010인 경우, 조회 기간(from, to)을 줄여서 조회해야 합니다.

##### Request Body
```
없음
```

##### Response Body
```
{
	"messageErrors" : [{
			"messageId" : 0,
			"messageIdString" : "0",
			"pushType" : "GCM",
			"messageErrorType" : "ClientError",
			"messageErrorCause" : "INVALID_CERTIFICATE",
			"payload" : {
				"data" : {
					"title" : "title",
					"body" : "body"
				}
			},
			"createdDateTime" : "2017-05-18T15:47:00.000+09:00",
			"tokens" : [{
					"uid" : "uid-1",
					"token" : "token-1"
				}
			]
		}
	],
	"header" : {
		"isSuccessful" : true,
		"resultCode" : 0,
		"resultMessage" : "Success."
	}
}
```
```
{
    "messageErrors" : [{
        ...
    }],
	"header" : {
		"resultCode" :  40010,
		"resultMessage" :  "Client Error. It's too many. Please, change 'from' and 'to' shortly. totalCount<10>",
		"isSuccessful" :  false
	}
}
```
| Field | Usage | Description |
| - | - | - |
| messageId | - | 실패한 메시지 아이디 |
| messageIdString | - | 실패한 메시지 아이디 |
| pushType | - | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT', 'APNS_VOIP', 'APNS_SANDBOXVOIP', 'ADM' |
| payload | - | 기기에 발송된 실제 메시지 내용 |
| tokens | - | 발송한 실패한 수신자의 uid와 token |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/message-errors
```

#### 메시지 수신, 확인 통계 조회

**Fade-out 된 API입니다. v2.4 이상의 API를 이용해 주세요.**

메시지 수신, 확인 수집(Message Delivery Receipt) 기능을 화성화하고, v1.4 이상 SDK를 적용하면 발송한 메시지에 대해 수신, 확인 정보를 확인할 수 있습니다.
수집된 정보를 통계 API로 조회할 수 있습니다. 기능은 [Console] > [Settings] 탭에서 활성화할 수 있습니다.

##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/statistics/message-delivery-receipts?from={from}&to={to}&event={event}&timeUnit={timeUnit}&messageId={messageId}
HEADER
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| from | Optional, DateTime String | 최근 30일 까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 최근 30일 까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| event | Optional, String | 'SENT', 'SENT_FAILED', 'RECEIVED', 'OPENED' |
| timeUnit | Optional, String | 'MINUTES', 'HOURS', 'DAYS'<br>값이 없다면 조회기간에 따라 임의로 통계가 제공됩니다.<br>조회기간이 1일 이상은 일 단위, 1시간에서 24시간 사이는 시간 단위, 1시간 이하는 분 단위로 표시됩니다. |
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
			"sent" : 13,
			"sentFailed": 0,
			"received" : 12,
			"opened" : 10
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
| dateTime | Optional, DateTime String | ISO 8601 |
| sent | Optional, Number | 서버에서 발송한 수 |
| sentFailed | Optional, Number | 서버에서 발송 실패한 수 |
| received | Optional, Number | 기기에서 수신한 수 |
| opened | Optional, Number | 기기에서 사용자가 클릭해 오픈한 수 |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/statistics/message-delivery-receipts
```

### 로그 조회
- 로그 조회 API는 Logging 기능을 활성화한 상태에서만 호출가능 하다.
- Logging 기능은 [Console] > [Notification] > [Push] > [Setting] 탭에서 활성화 시킬 수 있습니다.

#### 일반 로그 조회
- 최대 100개까지 조회 가능합니다.

##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/logs/message?messageId={messageId}&uid={uid}&token={token}&pushType={pusyType}&from={from}&to={to}&limit={limit}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| messageId | Optional, Number | 메시지 아이디 |
| uid | Optional, String | 사용자 아이디 |
| token | Optional, String | 사용자 토큰 |
| pushType | Optional, String | 푸시 타입 |
| from | Optional, DateTime String | 최근 30일까지(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 최근 30일까지(ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| limit | Optional, Number | 최대 조회 개수, 기본값 100 |
| pageNumber | Optional, Number | 페이지 번호, 기본값 1 |

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
    "data" : {
        "count" : 0,
        "logs" : [{
                "logType" : "message-result",
                "logSource" : "tc-push",
                "messageId" : "1",
                "body" : "{\"tokens\":[{\"uid\":\"gimbimloki\",\"token\":\"1\"}],\"payload\":{\"aps\":{\"alert\":{\"title\":\"title\",\"body\":\"body\"},\"mutable-content\":1}}}",
                "logTime" : "1",
                "pushType" : "APNS",
                "sendTime" : "1",
								"searchKey1": "1746041784729856",
						    "searchKey2": "GCM",
						    "searchKey3": "SENT",
                "sentResult" : "SENT",
                "host" : "127.0.0.1",
                "appkey" : "APP_KEY",
                "logVersion" : "v2",
                "isNeedStored" : "bulk",
                "projectName" : "L&CS_APP_KEY",
                "SinkVersion" : "-",
                "projectVersion" : "v2.2"
            }
        ]
    }
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/logs/message?messageId=1&limit=10
```

#### 대량 로그 카운트 조회
- 검색 조건으로 검색된 로그의 수를 확인할 수 있습니다.

##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appKey}/bulk-logs/message/count?from={from}&to={to}&messageId={messageId}&pushType={pushType}&sendResult={sendReesult}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

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
    "data" : {
        "count" : 0
    }
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/bulk-logs/message/count
```

#### 대량 로그 조회
- 대량으로 로그를 조회하는 API입니다.
- 응답시 application/stream+json로 응답합니다.

```
GET /push/v2.2/appkeys/{appKey}/bulk-logs/message?from={from}&to={to}&messageId={messageId}&pushType={pushType}&sendResult={sendReesult}
Content-Type: application/json;charset=UTF-8
Accept: application/stream+json
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

| Field | Usage | Description |
| - | - | - |
| sendResult | Optional, String | 발송 결과입니다. 'SENT', 'SENT_FAILED' |

#### Request Body
```
없음
```

#### Response Body
```
{
    "body": "{\"tokens\":[{\"uid\":\"gimbimloki\",\"token\":\"1\"}],\"payload\":{\"aps\":{\"alert\":{\"title\":\"title\",\"body\":\"body\"},\"mutable-content\":1}}}",
    "host": "10.161.240.23",
    "appkey": "eCHQcPuPAiI6TgY8",
    "logTime": "1533802967999",
    "logType": "message-result",
    "pushType": "GCM",
    "sendTime": "1533802967999",
    "logSource": "tc-push",
    "messageId": "1746041784729856",
    "logVersion": "v2",
    "searchKey1": "1746041784729856",
    "searchKey2": "GCM",
    "searchKey3": "SENT",
    "sentResult": "SENT",
    "projectName": "4x7ybimqlRZImbfV",
    "isNeedStored": "bulk",
    "projectVersion": "v2.2"
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/bulk-logs/message?messageId=1
```

## 예약 메시지

### 생성
#### 예약 메시지 발송 스케줄 생성
##### Method, URL, Headers
```
POST /push/v2.2/appkeys/{appkey}/schedules
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
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
    ],
 	"daysOfWeek": [
 		"SUNDAY",
 		"MONDAY"
  	]
}
```

| Field | Usage | Description |
| - | - | - |
| type | Required, String | 'EVERY_DAY' (매일), 'EVERY_WEEK' (매주), 'EVERY_MONTH' (매월) |
| fromDate | Required, Date String | 예약 메시지 시작 년월일 (YYYY-MM-DD) |
| toDate | Required, Date String | 예약 메시지 종료 년월일 (YYYY-MM-DD) |
| times | Required, Time String | 예약 메시지 발송 시분 (hh:mm) |
| days | Optional, Number Array | type이 'EVERY_MONTH'일 때 설정합니다. (1, 2, ..., 31: 1일, 2일, ..., 31일) |
| daysOfWeek | Optional, String Array | type이 'EVERY_WEEK'일 때 설정합니다. ('SUNDAY', 'MONDAY', 'TUESDAY', 'WEDNESDAY', 'THURSDAY', 'FRIDAY', 'SATURDAY') |

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
| schedules | - | 일시 (ISO 8601, e.g. YYYY-MM-DDThh:mm) |

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/schedules -d '{"type":"EVERY_MONTH","fromDate":"2016-12-30","toDate":"2017-01-02","times":["12:00","17:00"],"days":[1,15],"daysOfWeek":["SUNDAY","MONDAY"]}'
```

#### 예약 메시지 생성
##### Method, URL, Headers
```
POST /push/v2.2/appkeys/{appkey}/reservations
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
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

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/reservations -d '{"schedules":["2016-12-30T12:40","2016-12-31T12:40"],"isLocalTime":false,"target":{"type":"UID","to":["uid"]},"content":{"default":{"title":"title","body":"body"}},"messageType":"AD","contact":"1588-1588","removeGuide":"매뉴 > 설정","timeToLiveMinute":1}'
```

### 조회
#### 목록 조회
##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/reservations?pageIndex={pageIndex}&pageSize={pageSize}&reservationStatus={reservationsStatus}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| pageIndex | Optional, Number | 기본값 0 |
| pageSize | Optional, Number | 기본값 25, 최댓값 100 |
| from | Optional, DateTime String | 최근 30일 까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 최근 30일 까지 (ISO 8601, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
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
	"reservations" : [{
			"reservationId" : 666810348995587,
			"reservationIdString" : "666810348995587",
			"schedules" : [{
					"scheduleId" : 2455708,
                    "scheduleIdString" : "2455708",
                    "reservationId" : 666810348995587,
                    "reservationIdString" : "666810348995587",
					"deliveryDateTime" : "2016-12-30T12:40:00.000+09:00",
					"timezoneOffset" : 0,
					"scheduleStatus" : "READY"
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
	],
    "totalCount" : 1
}

```

| Field | Usage | Description |
| - | - | - |
| reservationIdString | - | 예약 메시지 아이디 문자열 |
| createdDateTime | - | 예약 메시지 등록 일시 (ISO 8601) |
| updatedDateTime | - | 예약 메시지 수정 일시 (ISO 8601) |
| completedDateTime | - | 예약 메시지 발송 완료 일시, 완료가 안되었다면 현재 시간 표시 (ISO 8601) |
| reservationStatus | - | 'RESERVED', 'COMPLETED' |
| schedules.scheduleId | - | 예약 메시지 발송 스케줄 아이디 |
| schedules.scheduleIdString | - | 예약 메시지 발송 스케줄 아이디 문자열 |
| schedules.reservationIdString | - | 예약 메시지 발송 스케줄이 속한 예약 메시지 아이디 문자열 |
| schedules.deliveryDateTime | - | 예약 메시지 발송 일시 |
| schedules.timezoneOffset | - | 예약 메시지 발송 타임존, 현지 시간 발송시 설정 |
| schedules.scheduleStatus | - | 'READY', 'SENDING', 'CANCELED', 'DONE' 예약 메시지 발송 스케줄 상태 |
| totalCount | - | 등록된 전체 예약 메시지 수 |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/reservations
```

#### 단건 조회
##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/reservations/{reservation-id}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

##### Request Body
```
없음
```

#### Response Body
```json
{
	"header" : {
		"resultCode" : 0,
		"resultMessage" : "SUCCESS",
		"isSuccessful" : true
	},
	"reservation" : {
		"reservationId" : 666810348995587,
		"reservationIdString" : "666810348995587",
		"schedules" : [{
                "scheduleId" : 2455708,
                "scheduleIdString" : "2455708",
                "reservationId" : 666810348995587,
                "reservationIdString" : "666810348995587",
				"deliveryDateTime" : "2016-12-30T12:40:00.000+09:00",
				"timezoneOffset" : 0,
				"scheduleStatus" : "READY"
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
| updatedDateTime | DateTime String | 예약 수정 일시(ISO 8601) |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/reservations/{reservationId}
```

#### 발송된 예약 메시지 조회
##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/reservations/{reservation-id}/messages?pageIndex={pageIndex}&pageSize={pageSize}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

| Field | Usage | Description |
| - | - | - |
| appkey | Required, String | Path Variable, 상품 이용시 발급 받은 앱키 |
| reservationId | Required, Number | 예약 메시지 아이디 |
| pageIndex | Optional, Number | 기본값 0 |
| pageSize | Optional, Number | 기본값 25, 최댓값 100 |

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
				"pushTypes" : ["GCM", "APNS", "APNS_SANDBOX", "TENCENT", "ADM"]
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

| Field | Usage | Description |
| - | - | - |
| totalCount | - | 발송된 전체 메시지  수 |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/reservations/{reservationId}/messages
```

### 수정
#### 예약 메시지 수정
##### Method, URL, Headers
```
PUT /push/v2.2/appkeys/{appkey}/reservations/{reservationId}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
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

##### Example
```
curl -X PUT -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/reservations/{reservationId} -d '{"schedules":["2018-12-30T12:40","2018-12-31T12:40"],"isLocalTime":false,"target":{"type":"UID","to":["uid"]},"content":{"default":{"title":"title","body":"body"}},"messageType":"AD","contact":"1588-1588","removeGuide":"매뉴 > 설정","timeToLiveMinute":1}'
```

### 삭제
#### 예약 메시지 삭제
##### Method, URL, Headers
```
DELETE /push/v2.2/appkeys/{appkey}/reservations?reservationIds={reservationId,}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
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

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/reservations?reservationIds={reservationId,}
```

## 태그

### 생성
#### 태그 생성
##### Method, URL, Headers
```
POST /push/v2.2/appkeys/{appkey}/tags
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```
##### Request Body

```json
{
    "tagName" :  "서른"
}
```

| Field | Usage | Description |
| - | - | - |
| tagName | Required, String | 태그 이름, 최대 길이 255, 공백(Space) 문자 불가 |

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    },
    "tag" : {
        "tagId" :  "12345678"
    }
}
```

| Field | Usage | Description |
| - | - | - |
| tagId | Required, String | 생성된 태그 아이디, 길이 8 |

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/tags -d '{"tagName":"서른"}'
```

#### 태그에 Uid 추가 생성
- 태그에 Uid를 추가(Append)하는 것으로, 기존에 있던 Uid를 추가하면 Uid의 태그는 늘어납니다.
- 한 Uid의 최대 태그 수는 16개다.
##### Method, URL, Headers
```
POST /push/v2.2/appkeys/{appkey}/tags/{tag-id}/uids
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```
##### Request Body
```json
{
    "uids" : [
         "uid-01",
         "uid-02"
    ]
}
```
| Field | Usage | Description |
| - | - | - |
| uids | Required, String Array | Uid 배열, 최대 길이 16, Uid 최대 길이 64 |

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/tags/{tagId}/uids -d '{"uids":["uid"]}'
```

#### Uid에 태그 목록 설정
- Uid의 태그를 교체(Replace)하는 것으로, 기존에 설정된 태그는 삭제되고 새로운 태그로 설정됩니다.
##### Method, URL, Headers
```
POST /push/v2.2/appkeys/{appkey}/uids
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```
##### Request Body
```json
{
    "uid" :  "uid-01",
    "tagIds" : [
         "12345678",
         "23456789"
    ]
}
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/uids -d '{"uid":"uid","tagIds":["TAG_ID"]}'
```

### 조회
#### 태그 목록 조회
##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/tags?tagName={tagName}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

| Field | Usage | Description |
| - | - | - |
| tagName | Optional, String | 태그 이름 |

##### Request Body
```
없음
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    },
    "tags" : [
        {
            "tagId" :  "12345678",
            "tagName" :  "tagName",
            "createdDateTime" :  "2017-07-07T07:07:07.777+09:00",
            "updatedDateTime" :  "2017-07-07T07:07:07.777+09:00"
        }
    ]
}
```

| Field | Usage | Description |
| - | - | - |
| createdDateTime | Required, Date Time String | 생성 일시 (ISO 8601) |
| updatedDateTime | Required, Date Time String | 수정 일시 (ISO 8601) |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/tags
```

#### 태그 단건 조회
##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```
##### Request Body
```
없음
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    },
    "tag" : {
        "tagId" :  "12345678",
        "tagName" :  "서른",
        "createdDateTime" :  "2017-07-07T07:07:07.777+09:00",
        "updatedDateTime" :  "2017-07-07T07:07:07.777+09:00"
    }
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/tags/{tagId}
```

#### 태그의 Uid 목록 조회
- 태그가 달린 Uid 목록을 조회합니다.

##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/tags/{tag-id}/uids?offsetUid={uid}&limit={limit}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

| Field | Usage | Description |
| - | - | - |
| offsetUid | Optional, String | 설정된 Uid 다음 부터 조회 |
| limit | Optional, Number | 조회할 Uid 수 |

##### Request Body
```
없음
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    },
    "uids" : [
        {
            "uid" :  "uid-01",
            "tags" : [
                {
                    "tagId" :  "tag-id-01",
                    "tagName" :  "tag-name-01",
                    "createdDateTime" :  "2017-07-07T07:07:07.777+09:00",
                    "updatedDateTime" :  "2017-07-07T07:07:07.777+09:00"
                }
            ],
            "contacts" : [
                {   
                    "contactType" :  "TOKEN_GCM",
                    "contact" :  "token",
                    "createdDateTime" :  "2017-07-07T07:07:07.777+09:00"
                }
            ]
        }
    ]
}
```

| Field | Usage | Description |
| - | - | - |
| contacts | -, Object Array | Uid의 연락처, 토큰 정보 목록 |
| contactType | -, String | 토큰 타입, 'TOKEN_GCM', 'TOKEN_APNS', 'TOKEN_APNS_SANDBOX', 'TOKEN_TENCENT', 'TOKEN_ADM' |
| contact | -, String | 토큰 |
| createdDateTime | Required, Date Time String | 생성 일시 (ISO 8601) |

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/tags/{tagId}/uids
```

#### Uid 조회
- Uid를 조회합니다.
- 토큰 등록시 Contact(연락처)가 등록됩니다.
##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/uids/{uid}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```
##### Request Body
```
없음
```

##### Response Body
```json
{
"header" : {
"isSuccessful" :  true,
"resultCode" :  0,
"resultMessage" :  "SUCCESS"
},
"uid" : {
    "uid" :  "uid-01",
        "tags" : [
            {
                "tagId" :  "12345678",
                "tagName" :  "tag-name-01",
                "createdDateTime" :  "2017-07-07T07:07:07.777+09:00",
                "updatedDateTime" :  "2017-07-07T07:07:07.777+09:00"
            }
            ],
            "contacts" : [
            {
                "contactType" :  "TOKEN_GCM",
                "contact" :  "token",
                "createdDateTime" :  "2017-07-07T07:07:07.777+09:00"
            }
        ]
    }
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/uids/uid
```

### 수정
#### 태그 수정
##### Method, URL, Headers
```
PUT /push/v2.2/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```
##### Request Body
```json
{
    "tagName" :  "30대"
}
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X PUT -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/tags/{tagId} -d '{"tagName":"서른셋"}'
```

### 삭제
#### 태그 삭제
##### Method, URL, Headers
```
DELETE /push/v2.2/appkeys/{appkey}/tags/{tag-id}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```
##### Request Body
```
없음
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/tags/{tagId}
```

#### Uid 삭제
- Uid 삭제시 Contact, Token도 같이 삭제됩니다.
##### Method, URL, Headers
```
DELETE /push/v2.2/appkeys/{appkey}/uids?uids={uid,}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```

| Field | Usage | Description |
| - | - | - |
| uids | -, Object Array | 삭제할 Uid 목록, 쉼표(,)로 구분합니다. 한번에 16개까지 삭제할 수 있습니다. |

##### Request Body
```
없음
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/uids?uids=uid
```

#### 태그의 Uid 삭제
- Tag와 Uid 관계만 삭제합니다.
- Contact, Token이 삭제되지는 않습니다.
##### Method, URL, Headers
```
DELETE /push/v2.2/appkeys/{appkey}/tags/{tagId}/uids?uids={uid,}
Content-Type: application/json;charset=UTF-8
X-User-Access-Key-ID: [a-zA-Z0-9]{20}
X-Secret-Access-Key: [a-zA-Z0-9]{16}
```
##### Request Body
```
없음
```

##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" -H "X-User-Access-Key-ID: USER_ACCESS_KEY_ID" -H "X-Secret-Access-Key: SECRET_ACCESS_KEY" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/tags/{tagId}/uids?uids=uid
```

## Uid

### 생성

#### 태그 추가
- Uid에 태그 아이디로 태그를 추가합니다.
- Secret Key가 필요없다. 앱에서 호출 가능합니다.
##### Method, URL, Headers
```
POST /push/v2.2/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```json
{
    "tagIds": ["TAG_ID01"]
}
```
##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/uids/uid/tag-ids -d '{"tagIds":["TAG_ID"]}'
```

### 조회

#### Uid의 태그 아이디 조회
- Uid의 태그 아이디를 조회합니다.
- Secret Key가 필요없다. 앱에서 호출 가능합니다.
##### Method, URL, Headers
```
GET /push/v2.2/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```
없음
```
##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    },
    "tagIds": ["TAG_ID01"]
}
```

##### Example
```
curl -X GET -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/uids/uid/tag-ids
```

### 수정
#### Uid의 태그 수정
- Uid에 태그 아이디로 태그를 수정합니다.
- Secret Key가 필요없다. 앱에서 호출 가능합니다.
##### Method, URL, Headers
```
PUT /push/v2.2/appkeys/{appkey}/uids/{uid}/tag-ids
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```json
{
    "tagIds": ["TAG_ID02"]
}
```
##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

##### Example
```
curl -X PUT -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/uids/uid/tag-ids -d '{"tagIds":["TAG_ID"]}'
```

### 태그 삭제
- Uid의 태그 아이디를 조회합니다.
- Secret Key가 필요없다. 앱에서 호출 가능합니다.
##### Method, URL, Headers
```
DELETE /push/v2.2/appkeys/{appkey}/uids/{uid}/tag-ids?tagIds={tagId,}
Content-Type: application/json;charset=UTF-8
```
##### Request Body
```
없음
```
##### Response Body
```json
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS"
    }
}
```

| Field | Usage | Description |
| - | - | - |
| tagIds | Required, String Array | Query String, 삭제할 태그 아이디, 쉼표(,)로 구분 |

##### Example
```
curl -X DELETE -H "Content-Type: application/json;charset=UTF-8" https://api-push.cloud.toast.com/push/v2.2/appkeys/{appkey}/uids/uid/tag-ids?tagIds=TAG_ID_01,TAG_ID_02
```

* *문서 수정 내역*
    * *(2018.08.28) v2.2 API 업데이트*
    * *(2018.08.28) Logging API 추가*
