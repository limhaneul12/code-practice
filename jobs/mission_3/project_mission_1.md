## 자동화 도구 비교 구현 (Make vs Zapier)

워크플로우: "KRX API 호출 → 다중 종목 추출 → Discord 알림"

### [Make 구현]
- Trigger: Schedule – 평일 15:40 자동 실행
- HTTP: KRX 주가 정보 API 호출 (전체 종목 데이터 수신)
- Iterator: 종목 배열 분리
- Router: 삼성전자(005930) / SK하이닉스(000660) 분기
- Action: Discord – Send a Message (종목별 주가 알림)

### [Zapier 구현]
- Trigger: Schedule – 평일 3:45 PM 자동 실행
- Webhooks: GET으로 KRX 주가 정보 API 호출 (800개 종목 수신)
- Filter/Code: Code by Zapier(Python)로 2종목 추출
- Action: Discord – Send Channel Message (종목별 주가 알림)

### 비교 항목(표로 작성)

| 항목 | Make | Zapier |
|------|------|--------|
| UI/UX | 시각적 노드(모듈) 기반 | 리스트(단계) 기반 |
| 설정 난이도 | 초기 학습 곡선 있음 | 직관적이고 쉬움 |
| 데이터 처리 방식 | Iterator + Router로 분기 처리 | Code by Zapier(Python)로 추출 |
| 연동 서비스 범위 | 두 도구 모두 광범위하나 세부 커넥터 차이 존재 | 두 도구 모두 광범위하나 세부 커넥터 차이 존재 |
| 무료 플랜 | 1,000 Ops/월 | 100 Tasks/월 |
| 실행 로그 | 실행 히스토리에서 단계별 데이터 확인 가능 | Task History에서 확인 |

# Zapier의 주요 한계점 (Make 대비)

## 1. 분기(Router) 처리 방식
- **Make**: Router로 여러 갈래 분기 → 각 경로마다 다른 액션 실행 가능
- **Zapier**: 기본적으로 **Filter(통과/차단)** 방식 → 다중 분기하려면 **Zap을 여러 개** 만들거나 **Paths(유료)** 써야 함

> 💡 우리 프로젝트에서 삼성/SK하이닉스 **2종목 분기**를 Make은 Router로 깔끔하게, Zapier는 Code로 우회해야 했음

## 2. 데이터 반복 처리 (Iterator)
- **Make**: Iterator 모듈로 배열을 자동 반복
- **Zapier**: 별도 Iterator가 약함 → **Code(Python/JS)** 로 직접 처리하거나 **Looping(유료 앱)** 필요

> 💡 800개 종목에서 2개 추출할 때 **Code by Zapier**를 사용해야 했음

## 3. 무료 플랜 제약

| 항목 | Make | Zapier |
|------|------|--------|
| 무료 한도 | 1,000 Ops/월 | 100 Tasks/월 |
| 멀티스텝 | 자유 | 무료는 2단계까지 제한적 |
| 실행 주기 | 더 짧게 가능 | 폴링 간격 김 |

## 4. 데이터 흐름 시각화
- **Make**: 노드 연결로 **전체 흐름이 한눈에** 보임
- **Zapier**: 위→아래 **리스트 방식** → 복잡한 워크플로우일수록 파악이 어려움

## 5. 세밀한 데이터 가공
- **Make**: 함수(formatDate 등), 매핑이 **시각적**으로 풍부
- **Zapier**: 복잡한 가공은 결국 **Code 스텝**에 의존

---

## 📌 한 줄 요약
> **Zapier**는 "단순·직관적인 자동화"엔 빠르고 쉽지만,
> **분기·반복·복잡한 데이터 처리**에선 **Make**이 더 강력하다.


### 결론
- 복잡한 분기/반복 처리가 많은 워크플로우 → Make 추천 (Iterator + Router 강력)
- 빠르고 직관적인 설정이 중요한 경우 → Zapier 추천
- 본 프로젝트(다중 종목 알림)에서는 Make의 Iterator/Router가 종목별 분기에 유리했고,
  Zapier는 Code(Python)로 유연한 데이터 가공이 가능했음


