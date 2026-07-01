
---

## 자유 주제: KRX 장마감 주가 다중 종목 Discord 알림

**반복 업무 정의:** 매일 장 마감 후 관심 종목(삼성전자, SK하이닉스)의 종가·등락률·거래량을 KRX에서 직접 확인하고 정리하는 작업

**선정 도구:** Make (시각적 Router 분기 설계가 직관적이고, 무료 Ops 범위 내 다중 종목 처리 가능)

**워크플로우 흐름:**
1) **Trigger: Scheduler** – 평일(Weekdays) 오후 3:40 자동 실행
2) **Action 1: HTTP** – KRX OpenAPI 호출 (`basDd`에 동적 날짜 `formatDate(now)` 적용)
3) **Iterator** – 약 800개 종목 응답(OutBlock_1)을 항목별로 분리
4) **Router** – 종목별 2개 경로로 분기 (삼성전자 / SK하이닉스)
5) **Filter** – 각 경로에서 `ISU_CD`(종목코드)로 대상 종목만 통과
6) **Action 2: Discord** – Webhook으로 종가·등락률·거래량 메시지 전송

---

## 📝 보충 설명

### **1) 동적 날짜 처리**
`basDd` 파라미터에 `formatDate(now; "YYYYMMDD")`를 적용해, 매일 실행될 때마다 자동으로 당일 날짜의 데이터를 조회한다. 날짜를 수동으로 바꿀 필요가 없어 완전 자동화가 가능하다.

### **2) Iterator로 배열 분리**
KRX API는 약 800개 종목을 하나의 배열(OutBlock_1)로 응답한다. Iterator를 사용해 이 배열을 개별 종목 단위로 나눠야 이후 필터링과 분기가 가능하다.

### **3) Router + Filter 조합**
Router로 경로를 2개로 나눈 뒤, 각 경로의 Filter에서 종목코드(`ISU_CD`)로 원하는 종목만 통과시킨다. 이 구조 덕분에 종목을 추가할 때 경로만 늘리면 되어 확장이 쉽다.

### **4) 데이터 필드 매핑**
KRX 응답값 중 `TDD_CLSPRC`(종가), `FLUC_RT`(등락률), `ACC_TRDVOL`(거래량)을 Discord 메시지에 매핑해, 핵심 지표를 한눈에 확인할 수 있도록 구성했다.

### **5) Discord Webhook 알림**
Slack 대신 Discord Webhook을 활용해, 별도 앱 연동 없이 HTTP 모듈만으로 각 종목별 알림을 채널에 전송한다.

---
