---
name: show-me-the-prd
description: 인터뷰 기반 PRD 생성 스킬. "/show-me-the-prd", "PRD 만들어줘", "기획서 만들어줘", "쇼미더피알디", "앱 기획해줘", "서비스 기획" 같은 요청에 사용됩니다. 바이브코더를 위해 한 문장 아이디어에서 4종 디자인 문서(PRD, 데이터 모델, Phase 분리, 프로젝트 스펙)를 생성합니다.
---

# Show Me The PRD -- 인터뷰 기반 PRD 생성

> 한 문장 아이디어 -> 5-6번 인터뷰 -> 4종 디자인 문서 완성
> 바이브코더가 개발 지식 없이도 "진짜 제품"을 만들 수 있는 기획 문서를 생성한다.

## 핵심 원칙

1. **바이브코더 눈높이** -- 기술 용어 금지. 모든 선택지에 설명 + 장단점 필수.
2. **AI가 리드, 유저가 결정** -- 유저가 아는 것(목적, 대상)은 질문으로 끌어내고, 모르는 것(기술, 구조)은 AI가 조사해서 선택지로 제안.
3. **리서치 기반** -- 하드코딩된 추천이 아닌, 실시간 웹 리서치 기반 근거 제시.
4. **"진짜 제품" 지향** -- 목업/로컬 데모가 아닌 실제 배포 가능한 수준의 기획.

## 질문 설계 규칙 (모든 AskUserQuestion 호출 시 준수)

| 규칙 | 설명 |
|------|------|
| 설명 필수 | 모든 옵션의 description에 "뭔지 + 장점 + 단점" 포함 |
| 추천 표시 | 가장 적합한 옵션 label에 "(추천)" 붙이고 첫 번째 배치 |
| 비유 사용 | 기술 용어 대신 일상 비유 ("OAuth" -> "소셜 로그인") |
| 복잡도 힌트 | 설명에 난이도 표시 ("간단해요", "좀 복잡해요", "많이 복잡해요") |
| 열린 질문 최소화 | 첫 질문(뭘 만들고 싶어?) 하나만 열린 질문, 나머지 전부 선택형 |
| 기타 옵션 불필요 | AskUserQuestion이 자동으로 "Other" 제공 |

상세 인터뷰 방법론: `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/interview-guide.md`

---

## WHEN TRIGGERED - EXECUTE IMMEDIATELY

**이 문서는 참고 문서가 아니라 실행 지시서다.**
- 첫 번째 action: Phase 0 확인 후 즉시 1차 AskUserQuestion 도구 호출
- 텍스트 출력 후 질문하지 않는다. 도구를 먼저 호출한다.
- 모든 질문은 AskUserQuestion 도구 호출로만 진행한다.

---

## Phase 0: 사전 확인 (자동)

플러그인 의존성을 확인한다. 없어도 동작하지만, 있으면 리서치 품질이 올라간다.

1. **docs-guide 확인**: Glob으로 `~/.claude/plugins/docs-guide` 또는 프로젝트 내 docs-guide 존재 여부 확인
2. **deep-research 확인**: 동일하게 확인

확인 결과를 내부적으로 기억한다 (유저에게 묻지 않음):
- docs-guide 있음 -> 기술 문서 조사에 활용
- deep-research 있음 -> 종합 리서치에 활용
- 둘 다 없음 -> WebSearch로 폴백 (기본 동작에 문제 없음)

---

## 7-Step Workflow

### Step 1: 아이디어 분석 + 동적 질문

$ARGUMENTS를 PRD 관점에서 분석하여 부족한 정보만 질문한다.

**Step 1a — 갭 분석 (내부 처리, 유저에게 출력하지 않음):**

$ARGUMENTS에서 다음을 추출 시도한다:
1. 핵심 문제/목적 — 어떤 불편/문제를 해결하는가
2. 대상 사용자 — 누가, 몇 명 정도
3. 도메인 맥락 — 비슷한 서비스, 차별점
4. 플랫폼 — 웹/모바일/둘다
5. 규모/제약 — 예산, 기한, 기술 수준

파악된 항목은 메모하고, 불명확한 항목만 질문 리스트에 추가한다.

**Step 1b — 도메인 감지:**

$ARGUMENTS에서 도메인을 감지하면 해당 분야 특화 질문을 추가 고려:
- 이커머스: 결제 수단? 배송? 재고?
- 소셜: 팔로우? 피드? 신고?
- 교육: 진도 추적? 퀴즈? 인증?
- 금융: PG사? 환불? 세금?
- 생활/헬스: 기록 주기? 알림? 공유?
(이 외 도메인도 자유롭게 판단)

**Step 1c — AskUserQuestion 호출:**

**조건 A — $ARGUMENTS가 있는 경우:**

**EXECUTE:** 갭 분석에서 불명확한 항목만 골라 AskUserQuestion 도구를 즉시 호출한다.
- 질문 수: 1~3개 (불명확한 항목 수에 따라)
- 각 질문은 2~4개 옵션 + description(뭔지/장점/단점)
- 추천 옵션은 (추천) + 첫 번째 배치

질문 예시 (참고용 — 실제 질문은 갭 분석 결과에 따라 동적 생성):
- "이 앱을 누가 쓸 건가요?" → 나만/주변/불특정다수
- "웹이에요, 모바일이에요?" → 웹/모바일/둘다
- "결제 기능이 필요한가요?" → 도메인 특화 질문

**조건 B — $ARGUMENTS가 없는 경우:**

**EXECUTE:** 아래 JSON으로 AskUserQuestion 도구를 즉시 호출한다:

AskUserQuestion({
  "questions": [
    {
      "question": "어떤 문제를 해결하는 앱/서비스를 만들고 싶으세요?",
      "header": "아이디어",
      "multiSelect": false,
      "options": [
        {"label": "생산성/할일 관리", "description": "일정, 메모, 업무 관리 등 효율을 높이는 앱."},
        {"label": "소셜/커뮤니티", "description": "사람들이 소통하고 모이는 서비스."},
        {"label": "콘텐츠/미디어", "description": "글, 사진, 영상 등을 만들거나 공유하는 서비스."},
        {"label": "커머스/거래", "description": "물건이나 서비스를 사고파는 서비스."}
      ]
    }
  ]
})

답변 받은 후 조건 A 로직(갭 분석 → 동적 질문)으로 재진입한다.

---

### [Research Batch 1] 아이디어 직후 리서치

유저가 Step 1에 답변한 직후, 다음 질문을 준비하면서 병렬로 실행:

```
WebSearch: "{도메인} app features 2026"
WebSearch: "{유사 서비스명} 불만 사항 대안"
WebSearch: "{도메인} 앱 필수 기능"
```

리서치 결과를 Step 2의 기능 목록 선택지에 반영한다.

리서치 도구 선택 전략: `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/research-strategy.md`

---

### Step 2: 기능 정리 + 복잡도 안내

리서치 결과를 바탕으로 기능 목록을 AskUserQuestion의 옵션으로 직접 제시한다.
텍스트로 먼저 안내하지 않는다.

**EXECUTE:** 아래 JSON의 (동적) 필드를 리서치 결과로 채운 후 AskUserQuestion 도구를 즉시 호출한다:

AskUserQuestion({
  "questions": [
    {
      "question": "비슷한 앱들을 조사해봤어요. 꼭 필요한 기능을 골라주세요 (여러 개 선택 가능)",
      "header": "핵심 기능",
      "multiSelect": true,
      "options": [
        {"label": "(동적) 기능A (추천)", "description": "(동적) 간단해요 — [한 줄 기능 설명]. 대부분의 프레임워크에서 바로 구현 가능."},
        {"label": "(동적) 기능B", "description": "(동적) 좀 복잡해요 — [한 줄 기능 설명]. 외부 서비스 연동 필요, 비용 발생 가능."},
        {"label": "(동적) 기능C", "description": "(동적) 많이 복잡해요 — [한 줄 기능 설명]. 첫 버전에서는 빼고 나중에 추가하는 걸 추천."},
        {"label": "(동적) 기능D", "description": "(동적) [복잡도] — [한 줄 기능 설명]."}
      ]
    },
    {
      "question": "첫 번째 버전에 넣을 핵심 기능 조합을 골라주세요",
      "header": "MVP",
      "multiSelect": false,
      "options": [
        {"label": "(동적) 조합A (추천)", "description": "(동적) 가장 간단한 핵심 조합. [포함 기능 나열]. 빠르게 완성 가능."},
        {"label": "(동적) 조합B", "description": "(동적) [포함 기능 나열]. 조합A보다 기능이 많지만 시간이 더 걸려요."},
        {"label": "(동적) 조합C", "description": "(동적) [포함 기능 나열]. 풀 기능 버전. 시간이 가장 오래 걸려요."},
        {"label": "잘 모르겠어요", "description": "추천대로 갈게요. 가장 간단한 핵심 조합으로 시작합니다."}
      ]
    }
  ]
})

- (동적) 표시된 필드는 리서치 결과로 실제 값을 채운다
- 기능 설명은 각 option의 description에 포함 (별도 텍스트 출력 금지)

---

### [Research Batch 2] 기능 선택 직후

유저가 기능을 선택한 직후, AI가 데이터 모델과 Phase를 자동 생성하는 동안 병렬로:

```
WebSearch/docs-guide: "{선택된 핵심 기능} implementation best practice"
WebSearch: "{도메인} data model design"
```

---

### Step 3: 데이터 모델 확인

AI가 선택된 기능에서 핵심 데이터를 자동 추출하여 AskUserQuestion의 markdown preview로 직접 보여준다.
텍스트로 먼저 설명하지 않는다.

**EXECUTE:** 아래 JSON의 (동적) 필드를 추출된 데이터 모델로 채운 후 AskUserQuestion 도구를 즉시 호출한다:

AskUserQuestion({
  "questions": [
    {
      "question": "데이터 구조를 이렇게 잡았는데 괜찮아요? 오른쪽 미리보기에서 구조를 확인하세요.",
      "header": "데이터",
      "multiSelect": false,
      "options": [
        {
          "label": "좋아요, 이대로 갈게요 (추천)",
          "description": "일반적인 구조라 확장하기 좋아요.",
          "markdown": "(동적) 아래 형식으로 채운다:\n\n## 핵심 데이터 구조\n\n```\n[엔티티1] ── [일상 용어 설명]\n  └── [엔티티2] ── [일상 용어 설명]\n        └── [엔티티3] ── [일상 용어 설명]\n```\n\n| 데이터 | 설명 | 예시 |\n|--------|------|------|\n| (동적) | (동적) | (동적) |\n| (동적) | (동적) | (동적) |\n\n이 뼈대가 잘 잡혀있으면 나중에 기능 추가가 쉬워요."
        },
        {
          "label": "수정할 부분이 있어요",
          "description": "어떤 부분을 바꾸고 싶은지 알려주세요.",
          "markdown": "(동적) 위와 동일한 구조도 표시"
        },
        {
          "label": "잘 모르겠어요",
          "description": "추천대로 갈게요.",
          "markdown": "(동적) 위와 동일한 구조도 표시"
        }
      ]
    }
  ]
})

- (동적) 표시된 필드는 추출된 데이터 모델로 실제 값을 채운다
- markdown preview에 ASCII 관계도 + 필드 테이블을 포함한다
- multiSelect: false (markdown preview와 호환 필요)

---

### Step 4: Phase 분리 확인

AI가 기능 복잡도와 의존성을 기반으로 자동 Phase 분리 후 AskUserQuestion의 markdown preview로 직접 보여준다.
텍스트로 먼저 설명하지 않는다.

**EXECUTE:** 아래 JSON의 (동적) 필드를 Phase 분리 결과로 채운 후 AskUserQuestion 도구를 즉시 호출한다:

AskUserQuestion({
  "questions": [
    {
      "question": "이렇게 나눠서 하나씩 완성하는 걸 추천해요. 오른쪽 미리보기에서 확인하세요.",
      "header": "Phase",
      "multiSelect": false,
      "options": [
        {
          "label": "좋아요, 이대로 갈게요 (추천)",
          "description": "보편적인 순서예요. 각 Phase를 완성한 뒤 다음으로 넘어갑니다.",
          "markdown": "(동적) 아래 형식으로 채운다:\n\n## Phase 분리 계획\n\n### Phase 1 (MVP) — [핵심 기능 요약]\n이것만으로도 실제로 쓸 수 있는 앱이 돼요.\n- [ ] (동적) 기능 1\n- [ ] (동적) 기능 2\n- [ ] (동적) 기능 3\n> 장점: 빠르게 결과물 확인 | 단점: 아직 [부가 기능] 없음\n\n### Phase 2 — [확장 기능 요약]\nPhase 1이 안정적으로 돌아간 후에 추가.\n- [ ] (동적) 기능 A\n- [ ] (동적) 기능 B\n> 장점: (동적) | 단점: Phase 1 완성 필수\n\n### Phase 3 — [고도화 요약]\n고급 기능. 없어도 앱은 충분히 쓸 만함.\n- [ ] (동적) 기능 X\n- [ ] (동적) 기능 Y\n> 장점: (동적) | 단점: 비용/복잡도 높음\n\n---\n* 각 Phase 완료 기준: 실제 서버에 배포 + 실제 데이터 + 실제 로그인"
        },
        {
          "label": "순서를 바꾸고 싶어요",
          "description": "어떤 기능을 먼저 만들고 싶은지 알려주세요.",
          "markdown": "(동적) 위와 동일한 Phase 체크리스트 표시"
        },
        {
          "label": "Phase를 합치거나 나누고 싶어요",
          "description": "어떻게 바꾸고 싶은지 알려주세요.",
          "markdown": "(동적) 위와 동일한 Phase 체크리스트 표시"
        }
      ]
    }
  ]
})

- (동적) 표시된 필드는 Phase 분리 결과로 실제 값을 채운다
- markdown preview에 Phase별 기능 체크리스트를 포함한다
- multiSelect: false (markdown preview와 호환 필요)

---

### [Research Batch 3] 기술 스택 선택 전

Phase 확인 후, 기술 스택 질문 전에 리서치:

```
WebSearch: "best tech stack for {플랫폼} {도메인} app 2026"
WebSearch: "{후보 프레임워크1} vs {후보 프레임워크2} 2026 comparison"
docs-guide (있으면): "{후보 프레임워크} getting started"
WebSearch: "{도메인} app deployment cost free tier 2026"
```

---

### Step 5: 기술 스택 선택

리서치 기반으로 프로젝트에 맞는 기술 스택 2-3개를 추천한다.

**EXECUTE:** 아래 JSON의 (동적) 필드를 리서치 결과로 채운 후 AskUserQuestion 도구를 즉시 호출한다:

AskUserQuestion({
  "questions": [
    {
      "question": "코드를 만들 때 어떤 도구를 쓸지 정해야 해요. 오른쪽 미리보기에서 비교해보세요.",
      "header": "기술 스택",
      "multiSelect": false,
      "options": [
        {
          "label": "(동적) 스택A (추천)",
          "description": "(동적) 한 줄 요약. 무료 시작 가능, AI 코딩 호환성 높음.",
          "markdown": "(동적) 아래 형식으로 채운다:\n\n## 기술 스택 비교\n\n| 항목 | (동적) 스택A | (동적) 스택B | (동적) 스택C |\n|------|---|---|---|\n| 무료 시작 | (동적) | (동적) | (동적) |\n| AI 코딩 호환 | (동적) | (동적) | (동적) |\n| 커뮤니티 크기 | (동적) | (동적) | (동적) |\n| 배포 난이도 | (동적) | (동적) | (동적) |\n| 확장성 | (동적) | (동적) | (동적) |\n\n**추천 이유:** (동적) 이 프로젝트에는 스택A가 가장 적합해요."
        },
        {
          "label": "(동적) 스택B",
          "description": "(동적) 한 줄 요약.",
          "markdown": "(동적) 위와 동일한 비교 테이블 표시"
        },
        {
          "label": "(동적) 스택C",
          "description": "(동적) 한 줄 요약.",
          "markdown": "(동적) 위와 동일한 비교 테이블 표시"
        }
      ]
    },
    {
      "question": "로그인 방식을 골라주세요",
      "header": "인증",
      "multiSelect": false,
      "options": [
        {"label": "소셜 로그인 (추천)", "description": "구글/카카오로 로그인. 비밀번호 관리 불필요. 다만 구글/카카오에 의존."},
        {"label": "이메일+비밀번호", "description": "전통적 방식. 자유도 높지만, 비밀번호 찾기/변경 기능도 만들어야 해서 시간 더 걸림."},
        {"label": "매직링크", "description": "비밀번호 없이 이메일 링크로 로그인. 간편하지만 매번 이메일 열어야 함."},
        {"label": "로그인 필요 없어요", "description": "나만 쓰는 앱이면 생략 가능. 가장 간단."}
      ]
    }
  ]
})

- Q1: (동적) 필드는 리서치 결과로 실제 값을 채운다
- Q1의 markdown preview에 스택 비교 테이블을 포함한다 (무료여부/AI호환/커뮤니티/배포난이도/확장성)
- Q2: 인증 방식은 정적 JSON (그대로 사용)

---

### [Research Batch 4] 문서 생성 전 마지막 보강

```
docs-guide (있으면): 선택된 스택의 프로젝트 구조 참고
WebSearch: "{선택된 스택} project structure best practice 2026"
WebSearch: "{선택된 스택} deployment guide"
```

---

### Step 6: 문서 생성

수집된 모든 정보를 종합하여 4종 문서를 생성한다.

출력 디렉토리: 현재 프로젝트 루트에 `PRD/` 폴더 생성.

```
PRD/
├── 01_PRD.md                    # Product Requirements Document
├── 02_DATA_MODEL.md             # 데이터 모델 (개념적 ERD)
├── 03_PHASES.md                 # Phase 분리 계획
├── 04_PROJECT_SPEC.md           # 프로젝트 스펙 (AI 행동 규칙)
└── README.md                    # 네비게이션 가이드
```

각 문서의 템플릿과 섹션 구조: `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/document-templates.md`

**문서 생성 시 규칙**:
- 모든 기술 선택에 "왜 이걸 골랐는지" 근거 포함 (리서치 결과 인용)
- [NEEDS CLARIFICATION] 표시: 아직 정해지지 않은 부분 명시
- Phase별 "진짜 제품" 체크리스트 포함
- 프로젝트 스펙에 "절대 하지 마" 목록 반드시 포함

---

### Step 7: 완료 + 다음 단계 안내

문서 생성 완료 후 유저에게 안내:

1. **품질 요약**: "완성도: X/10" + 개선 포인트 3가지
2. **문서 위치**: `PRD/` 폴더 내 4종 문서 안내
3. **다음 단계 가이드**:

```
"문서가 완성됐어요! 다음 단계를 골라주세요:

 1. Phase 1 바로 시작하기
    이 PRD를 기반으로 Phase 1 개발을 시작해요.
    아래 프롬프트를 Claude Code에 붙여넣으세요:
    [Phase 1 시작 프롬프트 예시 제공]

 2. SDD 도구로 넘기기
    Spec Kit이나 Deep Trilogy 같은 도구에 이 PRD를 넘겨서
    더 상세한 구현 계획을 만들 수 있어요.

 3. 문서 수정하기
    특정 부분을 바꾸고 싶으면 말해주세요."
```

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

Refer to `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/` for detailed documentation:

| Reference | Purpose |
|-----------|---------|
| `interview-guide.md` | 인터뷰 방법론, 질문 설계 규칙, 바이브코더 대응 전략 |
| `document-templates.md` | 4종 문서 템플릿과 섹션 구조 |
| `research-strategy.md` | 리서치 오케스트레이션, 도구 라우팅, 배치 전략 |
