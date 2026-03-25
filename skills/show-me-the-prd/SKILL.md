---
name: show-me-the-prd
description: 인터뷰 기반 PRD 생성 스킬. "/show-me-the-prd", "PRD 만들어줘", "기획서 만들어줘", "쇼미더피알디", "앱 기획해줘", "서비스 기획" 같은 요청에 사용됩니다. 바이브코더를 위해 한 문장 아이디어에서 4종 디자인 문서(PRD, 데이터 모델, Phase 분리, 프로젝트 스펙)를 생성합니다.
---

# Show Me The PRD -- 인터뷰 기반 PRD 생성

> 한 문장 아이디어 -> 5-6번 인터뷰 -> 4종 디자인 문서 완성

## 핵심 원칙

1. **바이브코더 눈높이** -- 기술 용어 금지. 모든 선택지에 설명 + 장단점 필수.
2. **AI가 리드, 유저가 결정** -- 유저가 아는 것(목적, 대상)은 질문으로 끌어내고, 모르는 것(기술, 구조)은 AI가 조사해서 선택지로 제안.
3. **리서치 기반** -- 실시간 웹 리서치 기반 근거 제시.
4. **"진짜 제품" 지향** -- 실제 배포 가능한 수준의 기획.

## 질문 설계 규칙 (모든 AskUserQuestion 호출 시 준수)

| 규칙 | 설명 |
|------|------|
| 설명 필수 | 모든 옵션의 description에 "뭔지 + 장점 + 단점" 포함 |
| 추천 표시 | 가장 적합한 옵션 label에 "(추천)" 붙이고 첫 번째 배치 |
| 비유 사용 | 기술 용어 대신 일상 비유 ("OAuth" -> "소셜 로그인") |
| 복잡도 힌트 | 설명에 난이도 표시 ("간단해요", "좀 복잡해요", "많이 복잡해요") |
| 열린 질문 최소화 | 첫 질문만 열린 질문, 나머지 전부 선택형 |

상세 인터뷰 방법론: Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/interview-guide.md`

---

## WHEN TRIGGERED - EXECUTE IMMEDIATELY

**이 문서는 참고 문서가 아니라 실행 지시서다.**
- 첫 번째 action: Phase 0 확인 후 즉시 1차 AskUserQuestion 도구 호출
- 텍스트 출력 후 질문하지 않는다. 도구를 먼저 호출한다.
- 모든 질문은 AskUserQuestion 도구 호출로만 진행한다.

---

## Phase 0: 사전 확인 (자동)

1. **docs-guide / deep-research 확인**: 스킬 목록에서 존재 여부 확인 (유저에게 묻지 않음)
   - 있음 -> 리서치에 활용 | 없음 -> WebSearch로 폴백

---

## 7-Step Workflow

### Step 1: 아이디어 분석 + 동적 질문

**Step 1a — 갭 분석 (내부 처리, 유저에게 출력하지 않음):**

$ARGUMENTS에서 추출 시도: (1) 핵심 문제/목적, (2) 대상 사용자, (3) 도메인 맥락, (4) 플랫폼, (5) 규모/제약. 파악된 항목은 메모, 불명확한 항목만 질문 리스트에 추가.

**Step 1b — 도메인 감지:** 도메인 인식 시 특화 질문 추가 고려 (이커머스: 결제/배송, 소셜: 팔로우/피드, 교육: 진도/퀴즈, 금융: PG/환불 등)

**Step 1c — AskUserQuestion 호출:**

- **$ARGUMENTS 있음**: 불명확한 항목만 1~3개 질문, 각 2~4개 옵션 + description
- **$ARGUMENTS 없음**: 카테고리 선택 질문 호출 -> 답변 후 조건 A로 재진입

JSON 템플릿: Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/step-templates.md` > "Step 1b"

---

### [Research Batch 1] 아이디어 직후

유저 답변 직후 병렬 리서치. 결과를 Step 2 기능 목록에 반영.

리서치 쿼리/도구 선택: Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/research-strategy.md` > "Batch 1"

---

### Step 2: 기능 정리 + 복잡도 안내

리서치 결과로 기능 목록을 AskUserQuestion 옵션으로 직접 제시. 텍스트로 먼저 안내하지 않는다.

**EXECUTE:** (동적) 필드를 리서치 결과로 채운 후 AskUserQuestion 즉시 호출. Q1: 핵심 기능 (multiSelect), Q2: MVP 조합 선택.

JSON 템플릿: Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/step-templates.md` > "Step 2"

---

### [Research Batch 2] 기능 선택 직후

기능별 기술 난이도 + 데이터 모델 패턴 리서치.

리서치 쿼리: Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/research-strategy.md` > "Batch 2"

---

### Step 3: 데이터 모델 확인

선택된 기능에서 핵심 데이터를 자동 추출, AskUserQuestion의 markdown preview로 직접 보여준다. 텍스트로 먼저 설명하지 않는다.

**EXECUTE:** 추출된 데이터 모델로 (동적) 필드를 채운 후 AskUserQuestion 즉시 호출. markdown에 ASCII 관계도 + 필드 테이블 포함. multiSelect: false.

JSON 템플릿: Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/step-templates.md` > "Step 3"

---

### Step 4: Phase 분리 확인

기능 복잡도와 의존성 기반 자동 Phase 분리 후 markdown preview로 보여준다. 텍스트로 먼저 설명하지 않는다.

**EXECUTE:** Phase 분리 결과로 (동적) 필드를 채운 후 AskUserQuestion 즉시 호출. markdown에 Phase별 기능 체크리스트 포함. multiSelect: false.

JSON 템플릿: Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/step-templates.md` > "Step 4"

---

### [Research Batch 3] 기술 스택 선택 전

기술 스택 비교 리서치.

리서치 쿼리: Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/research-strategy.md` > "Batch 3"

---

### Step 5: 기술 스택 선택

리서치 기반으로 2-3개 스택 추천 + 인증 방식 선택.

**EXECUTE:** (동적) 필드를 리서치 결과로 채운 후 AskUserQuestion 즉시 호출. Q1: 스택 비교 (markdown preview 포함), Q2: 인증 방식 (정적 JSON).

JSON 템플릿: Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/step-templates.md` > "Step 5"

---

### [Research Batch 4] 문서 생성 전

프로젝트 구조 + 배포 가이드 리서치.

리서치 쿼리: Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/research-strategy.md` > "Batch 4"

---

### Step 6: 문서 생성

수집된 모든 정보를 종합하여 4종 문서를 프로젝트 루트 `PRD/` 폴더에 생성.

```
PRD/
├── 01_PRD.md              # Product Requirements Document
├── 02_DATA_MODEL.md       # 데이터 모델 (개념적 ERD)
├── 03_PHASES.md           # Phase 분리 계획
├── 04_PROJECT_SPEC.md     # 프로젝트 스펙 (AI 행동 규칙)
└── README.md              # 네비게이션 가이드
```

각 문서의 템플릿: Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/document-templates.md`

**문서 생성 시 규칙**:
- 모든 기술 선택에 "왜 이걸 골랐는지" 근거 포함 (리서치 결과 인용)
- [NEEDS CLARIFICATION] 표시: 아직 정해지지 않은 부분 명시
- Phase별 "진짜 제품" 체크리스트 포함
- 프로젝트 스펙에 "절대 하지 마" 목록 반드시 포함

---

### Step 7: 완료 + 다음 단계 안내

1. **품질 요약**: "완성도: X/10" + 개선 포인트 3가지
2. **문서 위치**: `PRD/` 폴더 내 4종 문서 안내
3. **다음 단계**: Phase 1 시작 프롬프트 제공 / SDD 도구 연계 안내 / 문서 수정 옵션

---

## Enhancement Mode (기존 기획 보강)

유저가 기존 기획 문서를 공유한 경우:

1. 문서를 Read로 읽기
2. 4종 문서 기준으로 갭 분석 (빠진 섹션, 모호한 부분 식별)
3. [NEEDS CLARIFICATION] 항목 목록 생성
4. 부족한 부분만 인터뷰 (Step 2-5 중 필요한 부분만)
5. 보강된 4종 문서 생성

---

## References

Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/` for detailed documentation:

| Reference | Purpose |
|-----------|---------|
| `interview-guide.md` | 인터뷰 방법론, 질문 설계 규칙, 바이브코더 대응 전략 |
| `document-templates.md` | 4종 문서 템플릿과 섹션 구조 |
| `research-strategy.md` | 리서치 오케스트레이션, 도구 라우팅, 배치 전략 |
| `step-templates.md` | Step별 AskUserQuestion JSON 템플릿 |
