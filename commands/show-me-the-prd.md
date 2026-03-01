---
name: show-me-the-prd
description: "인터뷰 기반 PRD 생성 -- 한 문장에서 4종 디자인 문서까지"
argument-hint: "[아이디어 설명]"
allowed-tools:
  - AskUserQuestion
  - Read
  - Write
  - WebSearch
  - WebFetch
  - Glob
  - Grep
  - Bash
---

# /show-me-the-prd Command

인터뷰 기반으로 바이브코더의 아이디어를 4종 디자인 문서로 변환한다.
5번의 AskUserQuestion 호출로 정보를 수집하고, 4종 문서를 생성한다.

아이디어: `$ARGUMENTS`

## 전체 규칙

- 모든 질문은 반드시 AskUserQuestion 도구를 호출하여 진행한다. 텍스트로 질문하지 않는다.
- 리서치 결과를 텍스트로 요약/설명하지 않는다. 결과는 즉시 AskUserQuestion의 옵션에 녹여낸다.
- 모든 옵션의 description에 "뭔지 + 장점 + 단점" 포함. 추천 옵션은 label에 "(추천)" 붙이고 첫 번째 배치.
- 기술 용어 대신 일상 비유 사용.

---

## Turn 1: 아이디어 탐색

$ARGUMENTS에 아이디어가 있으면 아이디어 질문은 스킵한다.

Use AskUserQuestion to ask the user:

**Question 1** (아이디어가 없을 때만): "어떤 문제를 해결하는 앱/서비스인가요?"
- **생산성/할일 관리** — 일정, 메모, 업무 관리 등 효율을 높이는 앱.
- **소셜/커뮤니티** — 사람들이 소통하고 모이는 서비스.
- **콘텐츠/미디어** — 글, 사진, 영상 등을 만들거나 공유하는 서비스.
- **커머스/거래** — 물건이나 서비스를 사고파는 서비스.

**Question 2**: "이 앱을 누가 쓸 건가요?"
- **나만 쓸 거예요 (추천)** — 가장 간단하고 빠르게 완성 가능. 다만 나중에 다른 사람이 쓰려면 수정 필요.
- **주변 사람들도 쓸 거예요** — 여러 사람 의견 반영 가능. 로그인, 권한 관리 추가돼서 좀 더 복잡.
- **불특정 다수에게 공개할 거예요** — 사업/포트폴리오 활용 가능. 보안, 성능 등 신경 쓸 게 많아 시간 오래 걸림.

**Question 3**: "비슷한 앱을 써본 적 있나요?"
- **있어요 — 불만이 있었어요** — 어떤 앱인지, 뭐가 불편했는지 알려달라고 안내.
- **있어요 — 괜찮았지만 부족했어요** — 어떤 부분이 아쉬웠는지 알려달라고 안내.
- **없어요 / 잘 모르겠어요** — 비슷한 서비스를 조사해보겠다고 안내.

**Question 4**: "웹앱이에요, 모바일앱이에요?"
- **웹사이트 (추천)** — 브라우저에서 쓰는 앱. 가장 빠르게 만들 수 있고 앱스토어 등록 불필요.
- **모바일 앱** — 스마트폰 전용. 카메라, GPS 등 기기 기능 활용 가능. 앱스토어 등록 필요.
- **둘 다** — 가장 많은 사람이 쓸 수 있지만 만드는 시간 2배.
- **잘 모르겠어요** — 추천대로 웹사이트로 시작.

---

## Turn 1 → Turn 2 전환

사용자 답변을 받으면, WebSearch로 "{아이디어} app features best practices 2026" 리서치한다.
리서치 결과를 텍스트로 설명하지 말고, 즉시 Turn 2의 AskUserQuestion을 호출한다.

---

## Turn 2: 기능 선택

WebSearch 리서치 결과를 바탕으로 즉시 AskUserQuestion을 호출한다. Do not summarize findings in text.

Use AskUserQuestion to ask the user:

**Question 1** (multiSelect: true): "비슷한 앱들을 조사해봤어요. 꼭 필요한 기능을 골라주세요"
- 리서치에서 발견한 핵심 기능 4개를 옵션으로 구성한다.
- 각 옵션의 label에 기능명, description에 "간단해요/좀 복잡해요/많이 복잡해요" 복잡도 + 한 줄 설명.
- 가장 기본적인 기능에 (추천) 표시.

**Question 2**: "첫 번째 버전에 넣을 핵심 기능 조합을 골라주세요"
- **조합A (추천)** — 가장 간단한 핵심만. 빠르게 완성 가능.
- **조합B** — 기능 더 많지만 시간 더 걸림.
- **조합C** — 풀 기능. 시간 가장 오래.
- **잘 모르겠어요** — 추천대로 가장 간단한 조합으로 시작.

---

## Turn 2 → Turn 3 전환

사용자 답변을 받으면, 선택된 기능에서 핵심 데이터 엔티티를 추출한다.
텍스트로 설명하지 말고 즉시 Turn 3의 AskUserQuestion을 호출한다.

---

## Turn 3: 데이터 모델 확인

추출된 데이터 모델을 AskUserQuestion의 markdown preview로 보여준다. Do not explain in text first.

Use AskUserQuestion to ask the user:

**Question 1** (markdown preview 사용): "데이터 구조를 이렇게 잡았는데 괜찮아요?"
- 각 옵션의 markdown 필드에 ASCII 관계도 + 필드 테이블을 넣는다.
- **좋아요, 이대로 갈게요 (추천)** — 일반적인 구조라 확장하기 좋다고 설명.
- **수정할 부분이 있어요** — 어떤 부분을 바꾸고 싶은지 알려달라고 안내.
- **잘 모르겠어요** — 추천대로 진행.

---

## Turn 3 → Turn 4 전환

사용자 답변을 받으면, 기능 복잡도와 의존성 기반으로 Phase를 자동 분리한다.
텍스트로 설명하지 말고 즉시 Turn 4의 AskUserQuestion을 호출한다.

---

## Turn 4: Phase 분리 확인

Phase 분리 결과를 AskUserQuestion의 markdown preview로 보여준다. Do not explain in text first.

Use AskUserQuestion to ask the user:

**Question 1** (markdown preview 사용): "이렇게 나눠서 하나씩 완성하는 걸 추천해요"
- 각 옵션의 markdown 필드에 Phase별 기능 체크리스트를 넣는다.
- Phase 1 = MVP (이것만으로 쓸 수 있는 앱), Phase 2 = 확장, Phase 3 = 고도화.
- **좋아요, 이대로 갈게요 (추천)** — 보편적인 순서라고 설명.
- **순서를 바꾸고 싶어요** — 어떤 기능을 먼저 만들고 싶은지 안내.
- **Phase를 합치거나 나누고 싶어요** — 어떻게 바꾸고 싶은지 안내.

---

## Turn 4 → Turn 5 전환

사용자 답변을 받으면, WebSearch로 "best tech stack for {플랫폼} {도메인} app 2026" 리서치한다.
리서치 결과를 텍스트로 설명하지 말고, 즉시 Turn 5의 AskUserQuestion을 호출한다.

---

## Turn 5: 기술 스택 선택

WebSearch 리서치 결과를 바탕으로 즉시 AskUserQuestion을 호출한다. Do not summarize findings in text.

Use AskUserQuestion to ask the user:

**Question 1** (markdown preview 사용): "코드를 만들 때 어떤 도구를 쓸지 정해야 해요"
- 리서치에서 발견한 기술 스택 3개를 옵션으로 구성한다.
- 각 옵션의 markdown 필드에 스택 비교 테이블 (무료여부, AI코딩 호환, 커뮤니티, 배포 난이도).
- 가장 적합한 스택에 (추천) 표시.

**Question 2**: "로그인 방식을 골라주세요"
- **소셜 로그인 (추천)** — 구글/카카오로 로그인. 비밀번호 관리 불필요. 다만 구글/카카오에 의존.
- **이메일+비밀번호** — 전통적 방식. 자유도 높지만 비밀번호 찾기/변경도 만들어야 함.
- **매직링크** — 비밀번호 없이 이메일 링크로 로그인. 간편하지만 매번 이메일 열어야 함.
- **로그인 필요 없어요** — 나만 쓰는 앱이면 생략 가능. 가장 간단.

---

## Turn 6: 문서 생성

모든 인터뷰 완료. 수집된 정보를 종합하여 PRD/ 폴더에 4종 문서를 Write한다:
- `PRD/01_PRD.md` — Product Requirements Document
- `PRD/02_DATA_MODEL.md` — 데이터 모델 (개념적 ERD)
- `PRD/03_PHASES.md` — Phase 분리 계획
- `PRD/04_PROJECT_SPEC.md` — 프로젝트 스펙 (AI 행동 규칙)
- `PRD/README.md` — 네비게이션 가이드

문서 생성 규칙:
- 모든 기술 선택에 "왜 이걸 골랐는지" 근거 포함 (리서치 결과 인용)
- Phase별 "진짜 제품" 체크리스트 포함
- 프로젝트 스펙에 "절대 하지 마" 목록 반드시 포함

각 문서의 템플릿: Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/document-templates.md`

---

## Turn 7: 완료 안내

문서 생성 완료 후:
1. 품질 요약: "완성도: X/10" + 개선 포인트
2. 문서 위치: PRD/ 폴더 안내
3. 다음 단계: Phase 1 시작 / SDD 도구 연계 / 문서 수정
