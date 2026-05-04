# 고려녹두삼계탕 갈비찜 홈페이지

## 사용 방법
1. 압축을 풀고 `index.html` 파일을 더블클릭하면 홈페이지를 바로 확인할 수 있습니다.
2. 전화번호는 `02-992-5282`, 이메일은 `adsh86@naver.com`으로 적용되어 있습니다.
3. 카카오 상담 링크는 기존에 사용하신 `http://pf.kakao.com/_xbKMxhX/chat`으로 연결해두었습니다.

## 구성
- index.html
- style.css
- assets/ 음식 이미지 webp 파일

## 가맹문의 신청폼
하단에 이름, 연락처, 현재 매장주소(혹은 창업예정지역)를 받는 신청폼을 추가했습니다.


## 구글 스프레드시트 자동 저장 최종 세팅

대상 스프레드시트:
https://docs.google.com/spreadsheets/d/1BDCLP-LlAEDnWBTzxaZQMx8Kwsq0cK9kwDBt1QgU3Bw/edit?usp=sharing

구글 스프레드시트는 보안상 홈페이지에서 편집 URL만 넣는 방식으로 바로 저장되지 않습니다.
아래 Apps Script를 1회 배포한 뒤, 생성된 웹 앱 URL을 `index.html` 하단의 `GOOGLE_SCRIPT_URL`에 넣으면
가족점 문의 신청폼 내용이 위 구글시트로 자동 저장됩니다.

1. 위 구글시트를 엽니다.
2. [확장 프로그램] > [Apps Script] 클릭
3. 아래 코드를 붙여넣기
4. [배포] > [새 배포] > 유형: [웹 앱]
5. 실행 권한: 본인
6. 액세스 권한: 모든 사용자
7. 배포 후 나온 웹 앱 URL 복사
8. `index.html` 하단의 `const GOOGLE_SCRIPT_URL = "";` 따옴표 안에 붙여넣기

Apps Script 코드:

```javascript
function doPost(e) {
  var sheet = SpreadsheetApp.openById("1BDCLP-LlAEDnWBTzxaZQMx8Kwsq0cK9kwDBt1QgU3Bw").getSheets()[0];
  var data = JSON.parse(e.postData.contents);

  sheet.appendRow([
    new Date(),
    data["이름"] || "",
    data["연락처"] || "",
    data["현재 매장주소 혹은 창업예정지역"] || "",
    data["문의유형"] || "고려녹두삼계탕 가족점 문의"
  ]);

  return ContentService
    .createTextOutput(JSON.stringify({ result: "success" }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

권장 시트 첫 행 제목:
접수시간 | 이름 | 연락처 | 현재 매장주소 혹은 창업예정지역 | 문의유형


## 최종 반영 완료

아래 Apps Script 웹 앱 URL을 `index.html`에 반영했습니다.

https://script.google.com/macros/s/AKfycbw4bNVGVtE_VDw0OZq8Or_GHnm95huIjgukCRTkoCRvEKwNhlu0I7_OaOJEsoCHJQA/exec

첨부하신 Apps Script 화면 확인 결과:
- 위쪽의 `function myFunction() { }`는 삭제해도 됩니다.
- 코드 맨 위와 맨 아래에 들어간 ```javascript / ``` 표시는 Apps Script 편집기에 넣으면 안 됩니다.
- Apps Script에는 아래 코드만 남아 있어야 합니다.

function doPost(e) {
  var sheet = SpreadsheetApp.openById("1BDCLP-LlAEDnWBTzxaZQMx8Kwsq0cK9kwDBt1QgU3Bw").getSheets()[0];
  var data = JSON.parse(e.postData.contents);

  sheet.appendRow([
    new Date(),
    data["이름"] || "",
    data["연락처"] || "",
    data["현재 매장주소 혹은 창업예정지역"] || "",
    data["문의유형"] || "고려녹두삼계탕 가족점 문의"
  ]);

  return ContentService
    .createTextOutput(JSON.stringify({ result: "success" }))
    .setMimeType(ContentService.MimeType.JSON);
}


## 최종 웹앱 URL 반영 완료

아래 새 배포 웹앱 URL이 `index.html` 신청폼에 최종 반영되었습니다.

https://script.google.com/macros/s/AKfycbw4bNVGVtE_VDw0OZq8Or_GHnm95huIjgukCRTkoCRvEKwNhlu0I7_OaOJEsoCHJQA/exec

가족점 문의 신청폼 제출 시 위 Apps Script 웹앱 URL로 전송되도록 설정되어 있습니다.


## 접수폼 오류 수정 안내

기존 방식은 `application/json`으로 전송하고 Apps Script에서 `JSON.parse(e.postData.contents)`로 받는 구조였습니다.
구글 Apps Script 웹앱에서는 홈페이지 환경에 따라 이 방식이 막히거나 본문이 다르게 전달되어 저장이 안 될 수 있습니다.

이번 수정본은 신청폼을 `FormData` 방식으로 보내도록 바꿨습니다.
따라서 Apps Script 코드도 아래 코드로 교체한 뒤, 반드시 새 버전으로 다시 배포해야 합니다.

새 Apps Script 코드:

```javascript

function doPost(e) {
  var sheet = SpreadsheetApp.openById("1BDCLP-LlAEDnWBTzxaZQMx8Kwsq0cK9kwDBt1QgU3Bw").getSheets()[0];

  sheet.appendRow([
    new Date(),
    e.parameter["이름"] || "",
    e.parameter["연락처"] || "",
    e.parameter["현재 매장주소 혹은 창업예정지역"] || "",
    e.parameter["문의유형"] || "고려녹두삼계탕 가족점 문의"
  ]);

  return ContentService
    .createTextOutput(JSON.stringify({ result: "success" }))
    .setMimeType(ContentService.MimeType.JSON);
}

```

배포 후 웹앱 URL은 현재 index.html에 아래 URL로 반영되어 있습니다.

https://script.google.com/macros/s/AKfycbw4bNVGVtE_VDw0OZq8Or_GHnm95huIjgukCRTkoCRvEKwNhlu0I7_OaOJEsoCHJQA/exec


## 파라미터 3개만 사용하는 최종 Apps Script

요청하신 대로 홈페이지 신청폼에서 전송되는 parameter는 아래 3가지만 남겼습니다.

1. 이름
2. 연락처
3. 현재 매장주소 혹은 창업예정지역

Apps Script도 아래 코드로 교체한 뒤 새 버전으로 다시 배포해주세요.

```javascript
function doPost(e) {
  var sheet = SpreadsheetApp.openById("1BDCLP-LlAEDnWBTzxaZQMx8Kwsq0cK9kwDBt1QgU3Bw").getSheets()[0];

  sheet.appendRow([
    e.parameter["이름"] || "",
    e.parameter["연락처"] || "",
    e.parameter["현재 매장주소 혹은 창업예정지역"] || ""
  ]);

  return ContentService
    .createTextOutput(JSON.stringify({ result: "success" }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

권장 시트 첫 행 제목:
이름 | 연락처 | 현재 매장주소 혹은 창업예정지역

현재 index.html에 반영된 웹앱 URL:
https://script.google.com/macros/s/AKfycbw4bNVGVtE_VDw0OZq8Or_GHnm95huIjgukCRTkoCRvEKwNhlu0I7_OaOJEsoCHJQA/exec


## 최종 오류 대응 수정본

이번 수정본은 fetch 방식이 아니라, HTML form을 Apps Script 웹앱 URL로 직접 POST하는 방식입니다.
이 방식은 CORS 문제를 피하기 때문에 일반 정적 홈페이지에서 가장 안정적입니다.

현재 index.html의 form action:
https://script.google.com/macros/s/AKfycbw4bNVGVtE_VDw0OZq8Or_GHnm95huIjgukCRTkoCRvEKwNhlu0I7_OaOJEsoCHJQA/exec

Apps Script에는 아래 코드만 넣어주세요. 기존 코드, myFunction, 마크다운 표시(```javascript)는 모두 삭제하세요.

```javascript
function doPost(e) {
  var sheet = SpreadsheetApp.openById("1BDCLP-LlAEDnWBTzxaZQMx8Kwsq0cK9kwDBt1QgU3Bw").getSheets()[0];

  var name = e.parameter["이름"] || "";
  var phone = e.parameter["연락처"] || "";
  var address = e.parameter["현재 매장주소 혹은 창업예정지역"] || "";

  sheet.appendRow([name, phone, address]);

  return HtmlService.createHtmlOutput("success");
}
```

정상 배포 확인용으로 아래 doGet을 같이 넣어도 됩니다.

```javascript
function doGet(e) {
  return HtmlService.createHtmlOutput("web app is working");
}
```

배포 설정은 반드시 다음과 같아야 합니다.
1. 배포 유형: 웹 앱
2. 실행 사용자: 나
3. 액세스 권한: 모든 사용자

테스트 순서:
1. Apps Script 저장
2. 배포 관리에서 새 버전 배포
3. 웹앱 URL을 브라우저 주소창에 직접 입력
4. 'web app is working'이 보이면 배포는 정상
5. `index_test_prefilled_iframe.html`을 열고 버튼 클릭
6. 구글시트에 '테스트홍길동 / 010-1234-5678 / 서울 성북구 테스트주소'가 들어오는지 확인


## 이메일 접수 방식으로 최종 변경

구글 스프레드시트 자동 저장 방식 대신, 신청폼 입력 내용이 이메일로 전송되도록 변경했습니다.

수신 이메일:
adsh86@naver.com

작동 방식:
1. 홈페이지 하단 신청폼에 이름, 연락처, 현재 매장주소 혹은 창업예정지역 입력
2. `가족점 문의 신청하기` 버튼 클릭
3. 사용자의 PC/휴대폰에 설정된 이메일 앱이 열림
4. 내용을 확인 후 보내기를 누르면 adsh86@naver.com로 전송됨

주의:
- mailto 방식은 사용자의 기기에 이메일 앱이 설정되어 있어야 합니다.
- 별도 서버 없이 HTML 파일만으로 이메일 접수를 받을 때 가장 간단한 방식입니다.


## 자동 이메일 접수 방식으로 변경

요청하신 대로, 사용자가 이메일 앱을 열 필요 없이 기존 신청폼에 입력하고 버튼을 누르면
입력 내용이 아래 이메일로 자동 전송되도록 수정했습니다.

수신 이메일:
adsh86@naver.com

적용 방식:
FormSubmit 서비스를 이용한 정적 HTML 자동 이메일 발송 방식입니다.
폼 action:
https://formsubmit.co/adsh86@naver.com

중요:
처음 1회 사용 시 FormSubmit에서 adsh86@naver.com로 확인 메일이 갈 수 있습니다.
그 확인 메일에서 Activate / Confirm 버튼을 눌러야 이후부터 자동 이메일 수신이 정상 작동합니다.

별도 서버 없이 HTML 홈페이지에서 자동 이메일을 보내려면 외부 폼 발송 서비스나 서버가 필요합니다.
현재 파일은 FormSubmit 방식으로 세팅되어 있습니다.
