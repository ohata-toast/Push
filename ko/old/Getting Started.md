## Notification > Push > Getting Started

TOAST Cloud Push 서비스를 사용하려면 먼저 서비스를 활성화해야 합니다.
이 문서에서는 서비스를 활성화하고 Push 서비스를 사용하는 방법에 대해 설명합니다.

![](http://static.toastoven.net/prod_push/21-05-03/overview_ko.png)

### Console

인증서 관리, API 테스트, 메시지 발송 등 모든 기능을 사용할 수 있다.

### REST APIs

토큰 등록/조회, 메시지 전송, 피드백 확인 등 Public API를 호출할 수 있다.

### client SDK
토큰 등록/조회 및 푸시 메시지 수신을 편하게 사용할 수 있다.

## 기능

### 알림/홍보성 푸시 메시지 수신 동의 정보 저장 및 자동 필터링

정보통신망법 규정(제50조부터 제50조의 8)에 따라 토큰 등록 시 알림/홍보성/야간홍보성 푸시 메시지 수신에 관한 동의 여부도 함께 입력 받는다. 메시지 발송 시 수신 동의 여부를 기준으로 자동 필터링 한다.

[KISA 가이드 바로 가기](https://spam.kisa.or.kr/spam/na/ntt/selectNttInfo.do?mi=1020&nttSn=1171&bbsId=1002)

[법령 바로 가기](http://www.law.go.kr/lsEfInfoP.do?lsiSeq=123210#)

### 국가 필터링

토큰 등록 시 국가 코드를 입력받고, 메시지 발송 시 발송 국가를 지정할 수 있다.

### 공통 메시지 포맷

공통 메시지 포맷에 맞게 메시지를 작성하면, 기기 종류에 맞게 메시지가 생성되어 발송된다.  
공통 메시지 포맷에 맞게 입력 받은 언어 코드 기준으로 메시지를 작성하면, 해당 언어와 기기 종류에 맞게 생성되어 발송된다.

### 광고성 메시지 발송

광고성 푸시 메시지 표시 의무화를 따르고 있다.  
광고성 메시지 타입으로 메시지를 발송할 경우, (광고), 연락처, 수신 철회 방법을 메시지에 삽입해 발송한다.  

### 예약 메시지 발송

한 번, 매일, 매주, 매월 등 다양한 예약 발송 타입을 제공한다.  
현지 시간에 맞춰 발송할 수 있다.

### 메시지 유효기간 설정

메시지에 유효기간(TTL)을 설정할 수 있다. 메시지가 유효기간을 넘길 경우, 실패 처리된다.
단, 0은 유효기간이 없는 것으로 발송 지연에 의해 실패 처리되지 않는다.

### 실시간 모니터링

![](http://static.toastoven.net/prod_push/img_03.png)

[Messages] 탭에서 메시지 발송 상태를 실시간으로 확인할 수 있다.

### 피드백 제공

삭제되거나 잘못된 토큰은 메시지 발송 시 자동 삭제되며, 피드백 API로 삭제된 토큰을 조회할 수 있다.

### 인증서 관리

푸시 타입별 인증서 또는 API Key를 관리할 수 있다.

### Public APIs

토큰 등록/조회, 메시지 전송, 피드백 확인 API를 제공 한다.  
[APIs] 탭에서 Public APIs를 테스트할 수 있다.

## 서비스 활성화
[Console] > [Notification] > [Push] > [상품이용] 버튼 클릭

## Console 가이드

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

- [Console] > [Notification] > [Push] > [Certificates] 탭 클릭
- 위에서 생성한 FCM API Key를 복사해서 [GCM API Key]에 붙여넣기 > [REGISTER] 클릭하면, 등록 완료

#### APNS 인증서 생성, 가져오기

- Mac에서 [키체인]을 실행
- [체인 접근] > [인증서 지원] > [인증 기관에서 인증서 요청] 버튼 클릭
- [사용자 이메일 주소]에 Apple Developer ID 등 필요한 정보를 입력하고, [디스크 저정됨] 선택하고, [본인이 키 쌍 정보 저장]을 선택하고, [계속] 클릭
- [키 쌍 정보]에서 키 크기, 알고리즘 선택 후 [계속] 클릭, CSR(Certificate Signing Request)) 파일 생성 완료
- [[Apple Developer Certificates](https://developer.apple.com/account/ios/certificate/certificateList.action)]로 이동
- 우측 상단 '+'(Add) 버튼 클릭, Certificate 발급 페이지로 이동
- [Select Type] > [Request] > [Generate] 탭에서 [키체인]을 통해서 만든 CSR파일 업로드, [Generate] 버튼 클릭
- 인증서 생성, 다운로드 가능(승인 권한이 없다면 승인을 받고 다운로드 가능)
- 인증서 다운로드, [키체인] > [인증서]에서 인증서 선택
- 인증서에서 마우스 오른쪽 선택, 매뉴 중 ['...' 보내기] 선택
- 저장 위치 설정 후 [저장] 클릭
- 보낸 항목을 보호하는 데 사용할 암호 입력, APNS 인증서 생성 완료

#### APNS 인증서 등록

- [Console] > [Notification] > [Push] > [Certificates] 탭 클릭
- [Apple Push Certificates] > [Certificates] 파일 선택 > APNS 인증서 선택
- [Password]에 인증서 비밀번호 입력
- [REGISTER] 클릭, 등록 완료

#### APNS 주의 사항
##### APNS(Production), APNS_SANDBOX(Development) 차이
- Production Provisioning Profile로 빌드한 앱은 APNS(Production)를 이용해야 하며, Development Provisioning Profile로 빌드한 앱은 APNS_SANDBOX(Development)를 이용해야 한다.
이를 지키지 않으면, 푸시 메시지를 정상적으로 수신할 수 없다.
- 토스트 클라우드 푸시에 등록한 APNS 인증서와 앱을 빌드할 때 사용한 Provisioning Profile이 달라 메시지 발송이 실패한 경우가 대부분이다.   
 APNS(Production) = Production Provisioning Profile  
 APNS_SANDBOX(Development) = Development Provisioning Profile  
- 더 자세한 설명은 다음 링크를 참고 바란다.  
 [Provisioning and Development](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ProvisioningDevelopment.html)  
 [App Distribution Quick Start](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppStoreDistributionTutorial/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013839)

##### APNS 메시지 발송 실패 원인
- 인증서가 만료되었을 때  
- 잘못된 인증서를 등록했을 때  
- 앱에서 푸시 메시지 수신을 거부했을 때  
- 디바이스가 인터넷에 연결되어 있지 않을 때

#### Tencent 회원가입

- [[Tencent 푸시 서비스 홈페이지](http://xg.qq.com/)]에 접속한다.
- 페이지 우측 상단 登录(로그인)을 클릭한다.
- 팝업 우측 하단 注册新帐号(회원 가입)을 클릭한다.
- [Email Account] 항목에서 Email Account, Nickname, Password, Verification Code등을 빠짐없이 입력하고 [Sign up now]을 클릭한다.
- [Send verification code] 아래에 [select your country/region]을 클릭한다.
- 'region'에서 'South Korea 0082'를 선택하고, 'Mobile Number'에 010AAAABBBB 형식으로 번호를 입력한다.
- 인증 코드를 입력하고 다음 단계로 넘어간다.
- 가입 시 입력한 Email에서 'Activation Email'를 확인하고, 활성화시킨다.

#### Tencent 애플리케이션 등록

- 로그인 후, [[Tencent 푸시 서비스 대시보드](http://xg.qq.com/xg/ctr_index/login?go_to_url=http%3A%2F%2Fxg.qq.com%2Fxg%2Fapps%2Fctr_app%2Findex)]에 접속한다.
- 페이지 우측 상단 接入推送应用(애플리케이션 등록)을 클릭한다.
- 应用名称(애플리케이션 이름)을 입력한다.
- 所属分类(카테고리)를 선택한다.
- 应用平台(애플리케이션 플랫폼)에서 안드로이드를 선택하고, 패키지 네임을 입력한다.
- 接入应用(애플리케이션 생성)을 클릭한다.

#### Tencent ACCESS ID, SECRET KEY 등록

- 로그인 후, [[Tencent 푸시 서비스 대시보드](http://xg.qq.com/xg/ctr_index/login?go_to_url=http%3A%2F%2Fxg.qq.com%2Fxg%2Fapps%2Fctr_app%2Findex)]에 접속한다.
- 应用统计(통계)를 클릭해서, 애플리케이션 대시보드로 들어간다.
- 좌측 메뉴 중 配置管理(설정) > 应用配置(애플리케이션 설정) 클릭한다. ACCESS ID, ACCESS KEY, SECRET KEY를 확인한다.
- TOAST Cloud로 돌아와 [Console] > [Notification] > [Push] > [Certificates] 탭 까지 이동한다.
- 확인한 [Tencent Credential]에 ACCESS ID, SECRET KEY를 입력한다.

### 즉시 전송

인증서와 토큰을 정상적으로 등록한 후, [Console] > [Notification] > [Push] > [Messages] 탭 > [ADD] 버튼을 클릭, 푸시 메시지를 즉시 전송할 수 있다.

즉시 전송 폼은 다음과 같이 구성되어 있다.

|이름|	내용|
|---|---|
|Target| 수신 대상을 설정할 수 있다. |
|Target > Type| ALL, UID, TAG 중에서 하나를 선택할 수 있다. |
|Target > To | UID를 입력할 수 있다. Target > Type에서 UID를 선택해 UID로 전환할 수 있다. 쉼표(',')를 구분자로 여러 개를 입력할 수 있다.|
|Target > UIDs | 쉼표(',')를 구분자로 여러 개를 입력할 수 있다.|
|Target > Tags | Select 버튼을 클릭해 Tag를 선택할 수 있다. |
|Target > Selected Tags | 선택된 Tag가 표시된다. 다시 클릭해 선택에서 제외할 수 있다. |
|Target > Countries|국가 코드를 입력할 수 있다. 쉼표(',')를 구분자로 여러 개를 입력할 수 있다.|
|Target > Push Types|GCM, APNS, APNS Sandbox, Tencent 복수로 선택할 수 있다.|
|Option > Time To Live|메시지 발송 유효 시간이다. 설정한 시간동안 발송이 지연되는 경우, 자동으로 실패 처리된다. 단위는 분이다. 0이면 발송 지연에 의해 실패 처리되지 않는다.|
|Message > Editor Type |SIMPLE, JSON 두 가지 Type이 있다. JSON 경우, 직접 메시지를 작성할 수 있다.|
|Message > Message Type |NOTIFICATION, AD 두 가지 Type이 있다. AD 경우, 광고성 푸시 메시지 수신 동의한 사용자들에게만 메시지가 발송된다.|


### 예약 전송

[Console] > [Notification] > [Push] > [Reservation] 탭 클릭, [ADD], [EDIT], [DELETE] 버튼을 통해 예약 전송을 관리할 수 있다.

예약 전송 폼은 다음과 같이 구성되어 있다.

|이름|	내용|
|---|---|
|Reservation Type| EVERY_DAY: StartDate, EndDate 사이 매일 지정된 시간에 메시지를 보낸다. <br/> EVERY_WEEK: Reservation Condition에 요일을 지정해 매주 저정한 요일에 메시지를 보낸다. <br/> EVERY_MONTH: Reservation Condition에 날짜를 지정해 매달 지정한 날짜에 메세지를 보낸다.|
|Day of week|요일을 선택할 수 있다.|
|Day of Month|쉼표(',')를 구분자로 날짜(월)을 입력할 수 있다.|
|Reservation time|메시지를 전송할 시간을 입력한다. 시간 형식은 'HH:mm' 이다.|
|Start Date|메시지를 전송할 시작 날짜를 입력한다. 달력을 이용해 날짜를 선택할 수 있다. 형식은 'YYYY-MM-DD' 이다.|
|End Date|메시지를 전송하는 마지막 날짜를 입력한다. 달력을 이용해 날짜를 선택할 수 있다. 형식은 'YYYY-MM-DD' 이다.|
|Local time|true로 설정하면 현지 시간 기준으로 메시지를 전송한다.|

### 토큰 관리

[Console] > [Notification] > [Push] > [Token] 탭 클릭, [ADD], [DELETE] 버튼을 통해 토큰을 추가 삭제할 수 있다.

#### 토큰 검색

- Search Type을 'TOKEN'으로 선택하고, 적절한 푸시 타입을 선택하고 토큰을 검색할 수 있다.

#### UID 검색

- Search Type을 'UID'로 선택하고, UID를 검색할 수 있다.
- 일부만 일치되더라도 검색 결과에 포함된다.

### 태그 관리

[Console] > [Notification] > [Push] > [Tag] 탭 클릭, [ADD], [EDIT], [DELETE] 버튼을 통해 태그를 관리할 수 있다.

#### 태그가 붙은 UID 관리
- 태그를 클릭하면, 태그가 붙은 UID 목록을 확인할 수 있다.
- [ADD], [DELETE] 버튼을 통해 UID를 추가, 삭제할 수 있다.
- UID 추가시 한 번에 1,000개까지 가능하다.

### 메시지 수신/확인 데이터 수집

[Console] > [Notification] > [Push] > [Setting] 탭 클릭, 메시지 수신 및 확인 데이터 수집(Message Delivery Receipt) 기능을 활성화 시킬 수 있다.
활성화된 기능 동작을 위해 v1.4 이상 SDK가 적용되어야 한다.
수집된 데이터는 [Statistics] 탭에서 확인할 수 있다.

#### 통계 조회
- 수집된 데이터는 [Statistics] 탭에서 확인할 수 있다.
- 최근 30일 내 수집된 데이터를 조회할 수 있으며, 기간과 시간 단위를 설정할 수 있다.

### 메시지 발송 내역 저장
- 메시지 발송 내역을 지정한 Log & Crash Search에 전송하는 기능이다.
- [Console] > [Notification] > [Push] > [Setting] 탭 클릭, 로깅(Logging) 기능을 활설화 시킬 수 있다.
- Appkey에는 사용하는 Log & Crash Search의 Appkey를 입력한다.
- Log Source는 내역 저장시 같이 남길 값을 입력한다. 다른 로그와 구분하는 값이다.
- Log Level은 발송 내역중 특정 내역만 남길 수 있도록 한다.
     - ALL: 발송 성공, 실패 등 모든 내역을 남긴다.
     - INFO: 만료된 토큰에 대한 발송, 발송 실패에 대한 내역만 남긴다. 발송 성공은 남기지 않는다.
     - ERROR: 발송 실패에 대한 내역만 남긴다.
- 연동 후 메시지 발송 내역은 [Console] > [Analytics] > [Log & Crash Search] > [Log Search] 메뉴에서 확인할 수 있다.
- 전송되는 메시지 발송 내역은 Log & Crash Search의 과금 정책을 따른다.
     - https://cloud.toast.com/pricing/analytics

#### 발송 내역 로그 형식
##### Body
```
{
	"tokens" : [{
			"uid" : "User Id",
			"token" : "Device Token",
            "newToken": "New Deivce Token",
            "message": "Result Message"
		}
	],
	"payload" : {
		"priority" : "high",
		"data" : {
			"messageDeliveryReceipt" : true,
			"title" : "타이틀",
			"body" : "내용",
			"messageDeliveryReceiptData" : {
				"messageId" : 963854842757578,
				"sentDateTime" : "2017-11-06T10:41:55.619+09:00"
			}
		},
		"registration_ids" : ["토큰"]
	}
}
```
- tokens: 발송된 토큰 정보
    - uid: 사용자 아이디
    - token: 토큰
    - newToken: 새로 발급된 토큰(새로운 토큰이 있을 때만 표시)
    - message: 결과 메시지(비정상 응답일 때만 표시)
- payload: 실제 GCM이나, APNS, TENCENT로 발송된 메시지 내용(푸시 타입에 따라 내용이 다름)

##### Fields
- Appkey: 메시지를 발송한 푸시 appkey
- messageId: 메시지 아이디
- pushType: 푸시 타입(GCM, APNS, APNS_SANDBOX, TENCENT)
- sentResult: 발송 결과(SENT, INVALID_TOKEN, ERROR)
- messageErrorType: 발송 실패 타입
    - CLIENT_ERROR: 클라이언트 오류로 잘 못된 발송 요청으로 발송 실패
    - EXTERNAL_ERROR: 외부 오류로, 발송 시 Google이나 Apple, Tencent 서버에서 비정상 응답으로 발송 실패
    - INTERNAL_ERROR: 내부 오류로 발송 실패
- messageErrorCause: 발송 실패 원인
    - SKIP: 잘 못된 토큰이나 인증서
	- NO_TOKEN: 발송 대상이 없음
	- INVALID_TOKEN: 잘 못된 토큰으로 발송 요청
    - INVALID_MESSAGE: 잘 못된 메시지로 발송 요청
    - INVALID_CERTIFICATE: 인증서 만료
    - UNAUTHORIZED: 인증서 만료
	- EXPIRED_TIME_OUT: 발송 요청한 메시지가 만료
	- APNS_ERROR: APNS에서 비정상 응답
	- GCM_ERROR: GCM에서 비정상 응답
	- TENCENT_ERROR: TENCENT에서 비정상 응답
	- AGENT_ERROR: Google, Apple, Tencent 서버로 통신이 비정상
    - UNKOWN: 내부에서 알 수 없는 오류 발생

## 개인정보 수탁사 고지 안내

'고객'이 TOAST Cloud > Push 상품 이용 시, '고객' - '당사' 간 개인정보 처리에 관한 업무 위수탁 관계가 발생하는 바 정보통신망법 및 개인정보보호법에 따라 위탁자인 '고객'은 개인정보처리방침을 통해 '당사'에 개인정보를 위탁한 현황(수탁자 및 업무의내용)을 공개하여야 합니다. 이에, '당사'에서는 '고객'이 TOAST Cloud의 Push 상품을 이용함에 있어 관련 법령을 준수하고, 위탁현황 미공개로 인하여 과태료 등의 불이익을 받지 않도록 아래와 같이 가이드 할 수 있습니다.

[개인정보 수탁사 고지 안내] Push 상품 이용 시 고객사에서 운영하시는'개인정보처리방침' > 위탁 현황에 다음의 내용을 표기해주세요.
```
수탁사 : 엔에이치엔엔터테인먼트 업무의 내용 : 푸시 메시지 발송 대행
```

* *문서 수정 내역*
    * *(2017.11.23) 메시지 발송 내역 저장 설명 추가*
    * *(2017.09.21) 즉시 전송 설명 수정*
    * *(2017.07.20) 개인정보 수탁사 고지 안내 추가*
