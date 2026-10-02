# 만족도 조사 응답 수집 (Google Sheets)

`index.html`의 `ENDPOINT`가 비어 있으면 응답은 그 기기 localStorage(`lp_survey_queue`)에만 남습니다.
실제 운영 전에 아래 순서로 구글 시트에 연결하세요.

1. 새 구글 시트 만들기 → **확장 프로그램 → Apps Script**
2. 아래 코드 붙여넣기
3. **배포 → 새 배포 → 웹 앱** (실행: 나 / 액세스: **모든 사용자**) → 웹 앱 URL 복사
4. `index.html` 상단 `const ENDPOINT = '';` 에 URL 붙여넣기

```js
const HEAD = ['submittedAt','lang','country','source','sourceOther','difficulty','story','course','usage','overall','recommend','comment','privacyConsent','eventEntry','name','phone'];
function doPost(e){
  const sh = SpreadsheetApp.getActiveSheet();
  if(sh.getLastRow()===0) sh.appendRow(HEAD);
  const d = JSON.parse(e.postData.contents);
  sh.appendRow(HEAD.map(k => d[k] === undefined ? '' : d[k]));
  return ContentService.createTextOutput('ok');
}
```

## 저장값 규칙
- `difficulty`: 1 매우 쉬움 … 3 적당 … 5 매우 어려움 (만족도와 분리된 척도)
- `story / course / usage / overall / recommend`: 5 가장 긍정 … 1 가장 부정
- `source`: 한국어 = prev_program, portal, instagram, blog_cafe, press, referral, offline, other / 외국어 = sns, search, travel, creator, referral, offline, hotel, other
- `lang`: ko, en, ja, zh-Hans, zh-Hant
- `country`: 외국어 응답자만 ISO 3166-1 alpha-2 코드(JP, US, CN …), 목록에 없으면 OTHER. 한국어 응답자는 null
- `privacyConsent` / `eventEntry`: 한국어 응답자가 개인정보 수집·이용에 동의하면 둘 다 true. 동의하지 않았거나 외국어 응답자는 false (name·phone은 빈 값)
- 이름·연락처는 개인정보입니다. 경품 발송 후 1개월 이내 시트에서 삭제하세요.

## 언어별 QR
`survey/?lang=ko` 는 언어 선택을 건너뜁니다. 외국어(`?lang=en` 등)는 언어가 미리 선택된 채 국가 선택 화면에서 시작합니다.
