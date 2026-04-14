# 🏗️ 하자 예방 관리 현황판
### 건축사업본부 하자 예방 통합 관리 시스템

---

## 📁 파일 구성

```
├── index.html   ← 대시보드 (GitHub Pages 배포 / 직접 실행 가능)
├── Code.gs      ← Google Apps Script 백엔드
└── README.md    ← 이 파일
```

---

## ✅ 설치 단계 (총 4단계, 약 10분)

---

### STEP 1 · Google Spreadsheet 생성

1. [https://sheets.google.com](https://sheets.google.com) 접속
2. **새 스프레드시트** 만들기
3. 제목: `하자 예방 관리 현황판`
4. 스프레드시트 URL의 ID 부분을 메모
   - 예: `https://docs.google.com/spreadsheets/d/`**`1aBcDeFgHiJkLmNoPqRsTuVwXyZ`**`/edit`

---

### STEP 2 · Apps Script 배포

1. 스프레드시트 상단 메뉴: **확장 프로그램 → Apps Script**
2. 기존 코드(`myFunction` 등) 전체 삭제
3. `Code.gs` 파일 내용 전체 **붙여넣기**
4. 💾 **저장** (Ctrl+S / Cmd+S)
5. 상단 함수 목록에서 `initSystem` 선택 → ▶️ **실행**
6. 권한 요청 창 → **검토** → **고급** → **이동(안전하지 않음)** → **허용**
7. 스프레드시트로 돌아가면 **7개 시트** 자동 생성됨

> ✅ `현장관리`, `공종관리`, `하자유형`, `체크리스트`, `점검기록`, `알림관리`, `시스템메타`

8. 웹 앱으로 배포:
   - Apps Script 편집기 우상단 **배포** → **새 배포**
   - 유형: **웹 앱**
   - 실행 사용자: **나**
   - 액세스: **모든 사용자** (또는 조직 내 사용자)
   - **배포** → **웹 앱 URL 복사**

---

### STEP 3 · index.html URL 연결

`index.html` 파일 상단의 `CFG` 부분을 수정합니다.

```javascript
const CFG = {
  APPS_SCRIPT_URL: 'https://script.google.com/macros/s/여기에_URL_입력/exec',
  SPREADSHEET_URL: 'https://docs.google.com/spreadsheets/d/여기에_ID_입력/edit'
};
```

> 텍스트 편집기(메모장, VS Code 등)로 `index.html`을 열어 수정하세요.

---

### STEP 4 · GitHub Pages 배포

```bash
# 1. GitHub 저장소 생성 (github.com → New repository)

# 2. 로컬에서 업로드
git init
git add index.html
git commit -m "하자 예방 관리 현황판 v3"
git branch -M main
git remote add origin https://github.com/계정명/저장소명.git
git push -u origin main

# 3. GitHub → Settings → Pages
#    Source: Deploy from a branch → main / (root) → Save
```

> 🌍 약 2분 후 접속 가능:  
> `https://계정명.github.io/저장소명/`

---

## 🔧 URL 없이 바로 사용하기 (오프라인 모드)

`index.html`을 브라우저에서 직접 열어도 **샘플 데이터**로 모든 기능을 
확인할 수 있습니다. (Google Sheets 동기화 기능만 비활성화)

> 파일 탐색기에서 `index.html` 더블클릭 → 브라우저에서 바로 실행

---

## 📊 주요 기능

| 기능 | 설명 |
|------|------|
| 대시보드 | 통계 카드 클릭 시 해당 화면으로 이동 + 필터 자동 적용 |
| 위험 공종 현황 | 간트 차트 기반 자동 생성, 진행중/1주내/2주내 자동 분류 |
| 공정 간트 차트 | 드래그&드롭 순서 변경, 공종 추가/수정/삭제, CSV 내보내기 |
| 체크리스트 관리 | 공종별 항목 추가/수정/삭제, 점검 완료 처리, 시트 동기화 |
| 하자유형 관리 | 20개 기본 등록, 카테고리 필터, CRUD, 시트 동기화 |
| 현장 관리 | 현장 등록/수정/삭제, 공정 진행률 자동 계산 |
| 알림 현황 | 2주 전 자동 알림, 클릭 시 체크리스트 연동, 확인 처리 |
| 통계 분석 | 위험등급, 진행현황, 하자 분류, 보수비용 차트 |
| 위험기준 설정 | 고소작업/중장비/동시작업 등 토글로 분류기준 관리 |
| 시트 동기화 | 불러오기/내보내기, 오류 로그, 재시도 기능 |

---

## 🗄️ 스프레드시트 시트 구성

| 시트명 | 용도 | 주요 컬럼 |
|--------|------|-----------|
| 현장관리 | 공사 현장 정보 | id, name, location, client, startDate, endDate, amount, status |
| 공종관리 | 공종별 일정 | id, siteId, name, code, startDate, endDate, progress, riskLevel |
| 하자유형 | 20개+ 하자 정보 | id, name, category, riskLevel, mainCause, litigationFreq, avgCost |
| 체크리스트 | 공종별 점검항목 | id, code, did, item, std, method, pass, req |
| 점검기록 | 점검 이력 로그 | id, siteId, processId, item, done, officer, checkedAt |
| 알림관리 | 자동 생성 알림 | id, processId, siteId, alertType, content, riskLevel, read |
| 시스템메타 | 시스템 설정값 | key, value, updatedAt |

---

## ⚙️ 자동화 트리거

- **매일 오전 8시**: `dailyCheck()` 실행
  - 2주 이내 시작 고위험 공종 → 알림 자동 생성
  - 진행중 고위험 공종 → 일일 알림 생성
  - (선택) 이메일 발송: `sendAlertEmails()` 주석 해제

---

## ❓ 문제 해결

**화면이 안 열린다**
- `index.html`을 Chrome 브라우저로 직접 열어보세요.

**동기화가 안 된다**
- Apps Script 재배포 후 새 URL로 교체
- 배포 시 액세스 권한을 "모든 사용자"로 설정했는지 확인

**CORS 오류**
- Apps Script 웹 앱이 "모든 사용자"로 배포됐는지 확인

**Apps Script URL 적용 방법**
```
배포 → 배포 관리 → 연필 아이콘 → 버전: 새 버전 → 배포
→ 새 URL로 index.html CFG 업데이트
```

---

*하자 예방 관리 현황판 v3 | 건축사업본부*
