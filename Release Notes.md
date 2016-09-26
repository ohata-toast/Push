## Notification > Push > Release Notes

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
* [API] 약 1억 건 이상 메시지를 발송한 프로젝트에서 메시지 조회시 타임아웃 발생하는 오류 수정
* [CONSOLE] 예약 메시지 수정시 요일이 저장한 내용과 다르게 표시되는 오류 수정
