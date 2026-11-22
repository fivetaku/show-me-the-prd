# 리서치 전략 -- 도구 라우팅과 배치 오케스트레이션

## 리서치 엔진: 기존 gptaku 플러그인 활용

바퀴를 새로 만들지 않는다. gptaku에 이미 있는 플러그인을 활용한다.

| 용도 | 사용 도구 | 사용 조건 | 예시 |
|------|----------|----------|------|
| 공식 문서 기반 기술 조사 | docs-guide 스킬 | 플러그인 설치됨 | "Next.js App Router 캐싱" |
| 종합 리서치 (시장, 트렌드) | insane-research 스킬 | 플러그인 설치됨 | "인보이싱 앱 시장 조사" |
| 빠른 검색 (가격, 비교, 팩트) | WebSearch (빌트인) | 항상 사용 가능 | "Supabase 무료 용량 {현재 연도}" |
| 페이지 내용 추출 | WebFetch (빌트인) | 항상 사용 가능 | 특정 URL의 가격표 확인 |

---

## 플러그인 의존성 확인 방법

Turn 0에서 자동 확인. 유저에게 묻지 않고 내부적으로 기억.

```
확인 방법:
사용 가능한 스킬 목록에 docs-guide, insane-research가 있는지 확인.
있으면 Skill 도구로 호출, 없거나 호출 실패 시 WebSearch로 폴백.

결과:
- 있음 -> Skill(skill="docs-guide:docs-guide", args="...") / Skill(skill="insane-research:insane-research", args="...") 활용
- 없음 -> WebSearch로 폴백 (기본 동작에 문제 없음)
```

**없어도 동작한다** -- docs-guide/insane-research가 없으면 빌트인 WebSearch로 폴백.
있으면 더 정확하고 깊이 있는 리서치가 가능.

---

## 리서치 라우팅 -- 질문 종류별 도구 선택

```
"Next.js에서 이미지 최적화 어떻게 해?"
  -> docs-guide (공식문서 기반 정확한 답)
  -> 폴백: WebSearch("{framework} image optimization guide")

"사진 앱 시장 규모와 트렌드"
  -> insane-research (멀티소스 종합 리서치)
  -> 폴백: WebSearch("{domain} market size trends {현재 연도}")

"Supabase Storage 무료 용량"
  -> WebSearch (빠른 팩트 체크)

"Vercel 배포 가격"
  -> docs-guide (공식문서) 또는 WebSearch
```

### 라우팅 규칙

| 질문 유형 | 1순위 | 2순위 (폴백) |
|----------|-------|-------------|
| 프레임워크/라이브러리 사용법 | docs-guide | WebSearch + WebFetch |
| 시장 조사, 경쟁 분석 | insane-research | WebSearch (여러 쿼리) |
| 가격, 용량, 스펙 비교 | WebSearch | WebFetch (공식 페이지) |
| 베스트 프랙티스, 구조 | docs-guide | WebSearch |
| 최신 트렌드 | WebSearch | insane-research |

---

## 리서치 배치 오케스트레이션

리서치를 아무 때나 하면 사용자가 기다린다. 명령(`commands/show-me-the-prd.md`)의 Turn 사이 빈 시간에 배치한다.

### 배치 타이밍

| 배치 | 타이밍 | 목적 | 도구 |
|------|--------|------|------|
| Batch 1 | Turn 1 답변 직후 (Turn 2 카드 전) | 시장·경쟁, 흔한 기능, 스택 후보 — Turn 2 카드 옵션의 근거 | WebSearch |
| Batch 2 | Turn 2 카드 답 직후 (Turn 3 초안 전) | 선택한 스택의 현재 버전·공식 스캐폴드 구조·검증 명령·배포, 도메인 보안 주의 | docs-guide + WebSearch/WebFetch |
| (UI 있을 때) | Turn 2.5 | 디자인 레퍼런스 수집 | insane-search 등 (`design-reference-guide.md`) |
| (선택) | 사용자 요청 시 | 깊은 시장 조사 | insane-research |

### 배치 원칙

| 원칙 | 설명 |
|------|------|
| 사용자 대기 시간 = 0 | 사용자가 답한 직후, 다음 카드를 만드는 동안 실행 |
| 다음 카드에 반영 | 결과는 설명하지 않고 바로 다음 카드의 옵션·설명에 녹인다 |
| 점진적 구체화 | 처음엔 넓게(WebSearch), 스택이 정해진 뒤엔 깊게(docs-guide) |
| insane-research는 선택적 | 오래 걸린다. 기본 흐름은 WebSearch + docs-guide로 충분 |
| 재활용 | Batch 1 결과를 Batch 2와 초안에서 다시 쓴다. 같은 주제 중복 검색 금지 |

---

## 배치별 검색 쿼리 패턴

`{현재 연도}`는 실행 시점의 연도로 바꾼다. 연도를 문서에 고정해 두면 해가 바뀐 뒤 낡은 결과를 끌어온다.

### Batch 1: Turn 1 직후 (시장·스택 후보)

```
WebSearch: "{도메인} app features best practices {현재 연도}"
WebSearch: "{유사 서비스명} 불만 사항 대안 앱"
WebSearch: "best tech stack for {플랫폼} {도메인} app {현재 연도}"
WebSearch: "{DB 후보} free tier pricing {현재 연도}"
```

목적: Turn 2의 MVP 범위 조합과 기술 방향 비교표를 근거 있게 만든다.

### Batch 2: Turn 2 직후 (초안 보강)

```
WebFetch/레지스트리: "{선택한 패키지}" 현재 안정판 버전
docs-guide: "{선택한 프레임워크} create project / recommended structure"
docs-guide: "{선택한 프레임워크} testing and e2e setup"
WebSearch: "{선택한 스택} deployment guide {현재 연도}"
WebSearch: "{도메인} 앱 보안·개인정보 주의사항"
```

목적: 04_PROJECT_SPEC의 버전(기억이 아닌 확인 값), 스택에서 나온 프로젝트 구조, 검증 명령, 배포 절차, 비기능·보안 항목을 채운다. 버전을 확인하지 못하면 "착수 시 확인"으로 둔다.

---

## 검색 쿼리 규칙

1. **현재 연도 포함**: "React vs Vue" (X) -> "React vs Vue {현재 연도}" (O)
2. **영어 + 한국어 병행**: 영어 쿼리가 결과 품질이 높고, 한국어 쿼리로 로컬 정보를 보완한다.
3. **구체적으로**: "best framework" (X) -> "best framework for photo sharing web app {현재 연도}" (O)
4. **비교**: "X vs Y comparison {현재 연도} pros cons"
5. **가격**: "{서비스} pricing free tier {현재 연도}"

---

## 리서치 결과 활용 방법

리서치 결과는 유저에게 직접 보여주지 않는다. 다음 방식으로 녹여낸다:

1. **선택지 설명에 반영**: "조사해봤는데, 현재 가장 많이 쓰이는 조합이에요"
2. **복잡도 판단 근거**: "이 기능은 {API} 연동이 필요해서 좀 복잡해요"
3. **가격 정보 포함**: "{서비스}는 무료로 시작 가능하고, {용량} 무료"
4. **문서에 근거 삽입**: "기술 스택 선택 이유: {리서치 결과 요약}"

---

## docs-guide / insane-research 스킬 호출 방법

플러그인이 설치되어 있을 때, 해당 스킬을 Skill 도구로 호출한다.

```
# docs-guide 호출
Skill(skill="docs-guide:docs-guide", args="Next.js App Router 프로젝트 구조")

# insane-research 호출
Skill(skill="insane-research:insane-research", args="인보이싱 앱 시장 분석")
```

**주의**: insane-research는 시간이 오래 걸린다. 기본 흐름에서는 WebSearch로 충분하고, 유저가 명시적으로 시장 조사를 원할 때만 사용.
