## v2.0 API Reference (Beta)

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

#### 토큰 등록

[Method, URL]

```
POST https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tokens
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

##### 1. 토큰과 푸시타입으로 토큰 조회

[Method, URL]

```
GET https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tokens/{token}?&pushType={pushType}
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

##### 2. 사용자 아이디로 토큰 조회

[Method, URL]

```
GET https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/tokens?uid={uid}
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

#### 토큰 통계 조회

##### 1. 토큰 속성 통계 조회

###### Method, URL, Headers
```
GET https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/statistics/token-properties?from={from}&to={to}&tokenProperties={tokenProperties}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| from | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| tokenProperties | Optional, String Array | 'agreement', 'country', 'language', 'timezone'<br/>','로 구분, e.g. tokenProperties=country,language |

###### Request Body
```
없음
```

###### Response Body
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
			}
		}
	],
	"header" : {
		"isSuccessful" : true,
		"resultCode" : 0,
		"resultMessage" : "Success."
	}
}
```

##### 2. 토큰 등록 통계 조회

###### Method, URL, Headers
```
GET https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/statistics/token-registration?from={from}&to={to}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

###### Request Body
```
없음
```

###### Response Body
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
		"resultMessage" : "Success."
	}
}
```

### 메시지

#### 메시지 발송
###### Method, URL, Headers
```
POST https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/messages
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
###### Request Body
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
    "timeToLive": 1
}
```
| Field | Usage | Description |
| - | - | - |
| target.type | Required, String | 'ALL', 'UID', 수신 타겟 타입 |
| target.to | Optional, String Array | target.type이 'UID'일 때, 수신자 UID 목록. (최대 10,000 개) |
| target.pushTypes | Optional, String Array | 'GCM', 'APNS', 'APNS_SANDBOX', 'TENCENT' |
| target.countries |	Optional, String Array | ISO 3166-1 alpha-2, ISO 3166-1 alpha-3 (최대 3 byte) |
| content | Required, Map | 수신자에게 전달될 내용 (최대 8,192 byte) |
| content.default | Required, Map | '자세한 내용은 아래 공통 메시지 형식' 참고 |
| content.default.title | Optional, String |  |
| content.default.body | Optional, String |  |
| messageType | Required, String | NOTIFICATION, AD |
| contact | Optional, String | messageType이 AD이면 경우 필수 |
| removeGuide | Optional, String | messageType이 AD이면 경우 필수 |
| timeToLive | Optional, Number | 단위는 분이다. 범위는 1에서 60까지다. 기본 값은 10 이다. |

###### Response Body
```json
{
    "message" : {
        "messageId" : 0,
        "messageIdString": "0"
    },
    "header" : {
        "isSuccessful" : true,
        "resultCode": 0,
        "resultMessage" : "Success."
    }
}
```


#### 메시지 목록 조회
- CONSOLE에서 발송한 메시지들만 조회할 수 있다.

###### Method, URL, Headers
```
GET https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/messages?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&messageStatus={messageStatus}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
| pageIndex | Optional, Number | 기본 값 0 |
| pageSize | Optional, Number | 기본 값 25, 최대 값 100 |
| from | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| to | Optional, DateTime String | 최근 30일 까지 (ISO 86091, e.g. YYYY-MM-DDThh:mm:ss.SSSTZD) |
| messageStatus | Optional, String | 'READY', 'PROCESSING', 'COMPLETE', 'CANCEL_NO_TARGET', 'CANCEL_INVALID_CERTIFICATE', 'CANCEL_INVALID_MESSAGE', 'CANCEL_UNSUPPORTED_MESSAGE_TYPE', 'CANCEL_UNAUTHORIZED', 'CANCEL_UNKNOWN' |

- Request Body
- Response Body
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
        "messageStatus": "COMPLETE",
        "provisionedResourceId": "[a-zA-Z0-9]{16}"
    }],
    "header" : {
        "isSuccessful" : true,
        "resultCode": 0,
        "resultMessage" : "Success."
    }
}
```

| Field | Usage | Description |
| - | - | - |
| createdDateTime | - | 메시지가 생성된 일시 (ISO 86091) |
| completedDateTime | - | 메시지 발송이 완료된 일시 (ISO 86091) |
| targetCount | - | 실제 발송된 타겟 토큰 수 |
| provisionedResourceId | - | 메시지가 발송된 전용 리소스 아이디 |

##### "messageStatus" 필드는 메시지 상태를 나타낸다. 다음과 같은 상태가 있다.

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

#### 메시지 조회
- CONSOLE에서 발송한 메시지들만 조회할 수 있다.

###### Method, URL, Headers
```
GET https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/messages/{message-id}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```
###### Request Body
```
없음
```
###### Response Body
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
        "resultMessage" : "Success."
    }
}
```

### 유효하지 않는 토큰

#### 유효하지 않는 토큰 조회

```
GET https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/invalid-tokens?pageIndex={pageIndex}&pageSize={pageSize}&from={from}&to={to}&messageId={messageId}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

| Field | Usage | Description |
| - | - | - |
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

### 예약 메시지

#### 예약 메시지 발송 스케줄 생성

##### Method, URL, Headers
```
POST https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/schedules
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

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
| days | Optional, Number Array | 'EVERY_WEEK' (1: 월, 2: 화, 3: 수, 4: 목, 5: 금, 6: 토, 7: 일), <br/>'EVERY_MONTH' (1, 2, ..., 31: 날짜) |

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
POST https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

##### Request Body
```json
{
	"schedules" : [
		"2016-12-30T12:40",
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
		"reservationId" : 666810348995587
	}
}
```

#### 예약 메시지 목록 조회

##### Method, URL, Headers
```
POST https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations?pageIndex={pageIndex}&pageSize={pageSize}&reservationStatus={reservationsStatus}
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
	"reservations" : [{
			"reservationId" : 666810348995587,
      "reservationIdString": "666810348995587",
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
			"timeToLive" : 60,
			"createdDateTime" : "2016-12-30T10:34:40.000+09:00",
      "updatedDateTime": "2016-12-30T10:34:40.000+09:00",
      "completedDateTime": "2016-12-30T10:34:40.000+09:00",
			"messageType" : "NOTIFICATION",
      "reservationStatus": "RESERVED",
			"schedules" : [{
					"scheduleId" : 2455708,
					"deliveryTime" : "2016-12-30T12:40:00.000+09:00",
					"timezoneOffset" : 0,
					"scheduleStatus" : "READY",
					"reservationId" : 666810348995587
				}, {
					"scheduleId" : 2455709,
					"deliveryTime" : "2016-12-31T12:40:00.000+09:00",
					"timezoneOffset" : 0,
					"scheduleStatus" : "READY",
					"reservationId" : 666810348995587
				}
			]
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
GET https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations/{reservation-id}
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
		"timeToLive" : 60,
		"createdDateTime" : "2016-12-30T10:34:40.000+09:00",
		"updatedDateTime" : "2016-12-30T10:34:40.000+09:00",
		"completedDateTime" : "2016-12-30T10:34:40.000+09:00",
		"messageType" : "NOTIFICATION",
		"reservationStatus" : "RESERVED",
		"schedules" : [{
				"scheduleId" : 2455708,
				"deliveryTime" : "2016-12-30T12:40:00.000+09:00",
				"timezoneOffset" : 0,
				"scheduleStatus" : "READY",
				"reservationId" : 666810348995587
			}, {
				"scheduleId" : 2455709,
				"deliveryTime" : "2016-12-31T12:40:00.000+09:00",
				"timezoneOffset" : 0,
				"scheduleStatus" : "READY",
				"reservationId" : 666810348995587
			}
		]
	}
}
```

#### 예약 메시지 수정

##### Method, URL, Headers
```
POST https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations/{reservationId}
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
DELETE https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations?reservationId={reservationId}
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
	}
}
```

#### 발송된 예약 메시지 조회 (수정 필요)

##### Method, URL, Headers
```
GET https://api-push.cloud.toast.com/push/v2.0/appkeys/{appkey}/reservations/{reservation-id}/messages?pageIndex={pageIndex}&pageSize={pageSize}
Content-Type: application/json;charset=UTF-8
X-Secret-Key: [a-zA-Z0-9]{8}
```

##### Request Body
```
없음
```

##### Response Body
```
수정 필요
```
