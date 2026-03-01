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

# /show-me-the-prd — 실행 지시서

**이 문서는 참고 문서가 아니라 실행 지시서다.**
- 모든 질문은 AskUserQuestion 도구 호출로만 진행한다.
- 텍스트로 질문하지 않는다. 도구를 먼저 호출한다.
- 답변을 받으면 즉시 다음 Step의 AskUserQuestion을 호출한다.
- 모든 옵션 description에 "뭔지 + 장점 + 단점". 추천 옵션은 "(추천)" + 첫 번째. 기술 용어 대신 일상 비유.

아이디어: `$ARGUMENTS`

---

## Step 1: 아이디어 탐색

**조건 A — $ARGUMENTS에 아이디어가 있는 경우:**

아래 도구를 즉시 호출한다 (Q1 스킵):

AskUserQuestion({
  "questions": [
    {
      "question": "이 앱을 누가 쓸 건가요?",
      "header": "사용자",
      "multiSelect": false,
      "options": [
        {"label": "나만 쓸 거예요 (추천)", "description": "가장 간단하고 빠르게 완성 가능해요. 다만 나중에 다른 사람이 쓰려면 수정이 필요해요."},
        {"label": "주변 사람들도 쓸 거예요", "description": "여러 사람 의견 반영 가능해요. 로그인, 권한 관리가 추가돼서 좀 더 복잡해요."},
        {"label": "불특정 다수에게 공개할 거예요", "description": "사업/포트폴리오 활용 가능해요. 보안, 성능, 개인정보보호 등 신경 쓸 게 많아 시간이 오래 걸려요."}
      ]
    },
    {
      "question": "비슷한 앱을 써본 적 있나요?",
      "header": "경험",
      "multiSelect": false,
      "options": [
        {"label": "있어요 — 불만이 있었어요", "description": "어떤 앱인지, 뭐가 불편했는지 알려주세요."},
        {"label": "있어요 — 괜찮았지만 부족했어요", "description": "어떤 부분이 아쉬웠는지 알려주세요."},
        {"label": "없어요 / 잘 모르겠어요", "description": "제가 비슷한 서비스를 조사해볼게요."}
      ]
    },
    {
      "question": "웹앱이에요, 모바일앱이에요?",
      "header": "플랫폼",
      "multiSelect": false,
      "options": [
        {"label": "웹사이트 (추천)", "description": "브라우저에서 쓰는 앱. 가장 빠르게 만들 수 있고 누구나 접근 가능. 앱스토어 등록 불필요."},
        {"label": "모바일 앱", "description": "스마트폰 전용 앱. 카메라, GPS 등 기기 기능 활용 가능. 앱스토어 등록 필요해서 시간/비용 추가."},
        {"label": "둘 다", "description": "웹 + 모바일. 가장 많은 사람이 쓸 수 있지만, 만드는 시간이 2배 가까이."},
        {"label": "잘 모르겠어요", "description": "추천대로 웹사이트로 시작할게요."}
      ]
    }
  ]
})

**조건 B — $ARGUMENTS가 없는 경우:**

아래 도구를 즉시 호출한다:

AskUserQuestion({
  "questions": [
    {
      "question": "어떤 문제를 해결하는 앱/서비스인가요?",
      "header": "아이디어",
      "multiSelect": false,
      "options": [
        {"label": "생산성/할일 관리", "description": "일정, 메모, 업무 관리 등 효율을 높이는 앱."},
        {"label": "소셜/커뮤니티", "description": "사람들이 소통하고 모이는 서비스."},
        {"label": "콘텐츠/미디어", "description": "글, 사진, 영상 등을 만들거나 공유하는 서비스."},
        {"label": "커머스/거래", "description": "물건이나 서비스를 사고파는 서비스."}
      ]
    },
    {
      "question": "이 앱을 누가 쓸 건가요?",
      "header": "사용자",
      "multiSelect": false,
      "options": [
        {"label": "나만 쓸 거예요 (추천)", "description": "가장 간단하고 빠르게 완성 가능해요. 다만 나중에 다른 사람이 쓰려면 수정이 필요해요."},
        {"label": "주변 사람들도 쓸 거예요", "description": "여러 사람 의견 반영 가능해요. 로그인, 권한 관리가 추가돼서 좀 더 복잡해요."},
        {"label": "불특정 다수에게 공개할 거예요", "description": "사업/포트폴리오 활용 가능해요. 보안, 성능, 개인정보보호 등 신경 쓸 게 많아 시간이 오래 걸려요."}
      ]
    },
    {
      "question": "비슷한 앱을 써본 적 있나요?",
      "header": "경험",
      "multiSelect": false,
      "options": [
        {"label": "있어요 — 불만이 있었어요", "description": "어떤 앱인지, 뭐가 불편했는지 알려주세요."},
        {"label": "있어요 — 괜찮았지만 부족했어요", "description": "어떤 부분이 아쉬웠는지 알려주세요."},
        {"label": "없어요 / 잘 모르겠어요", "description": "제가 비슷한 서비스를 조사해볼게요."}
      ]
    },
    {
      "question": "웹앱이에요, 모바일앱이에요?",
      "header": "플랫폼",
      "multiSelect": false,
      "options": [
        {"label": "웹사이트 (추천)", "description": "브라우저에서 쓰는 앱. 가장 빠르게 만들 수 있고 누구나 접근 가능. 앱스토어 등록 불필요."},
        {"label": "모바일 앱", "description": "스마트폰 전용 앱. 카메라, GPS 등 기기 기능 활용 가능. 앱스토어 등록 필요해서 시간/비용 추가."},
        {"label": "둘 다", "description": "웹 + 모바일. 가장 많은 사람이 쓸 수 있지만, 만드는 시간이 2배 가까이."},
        {"label": "잘 모르겠어요", "description": "추천대로 웹사이트로 시작할게요."}
      ]
    }
  ]
})

Step 1 답변 받으면 → WebSearch "{아이디어 도메인} app features 2026" 리서치 → 즉시 Step 2로.

---

## Step 2: 기능 정리 + 복잡도 안내

리서치 결과를 바탕으로 기능 목록을 AskUserQuestion의 옵션으로 직접 제시한다.
텍스트로 먼저 안내하지 않는다.

아래 도구의 (동적) 필드를 리서치 결과로 채운 후 즉시 호출한다:

AskUserQuestion({
  "questions": [
    {
      "question": "비슷한 앱들을 조사해봤어요. 꼭 필요한 기능을 골라주세요 (여러 개 선택 가능)",
      "header": "핵심 기능",
      "multiSelect": true,
      "options": [
        {"label": "(동적) 기능A (추천)", "description": "(동적) 간단해요 — [한 줄 기능 설명]. 대부분의 프레임워크에서 바로 구현 가능."},
        {"label": "(동적) 기능B", "description": "(동적) 좀 복잡해요 — [한 줄 기능 설명]. 외부 서비스 연동 필요."},
        {"label": "(동적) 기능C", "description": "(동적) 많이 복잡해요 — [한 줄 기능 설명]. 첫 버전에서는 빼는 걸 추천."},
        {"label": "(동적) 기능D", "description": "(동적) [복잡도] — [한 줄 기능 설명]."}
      ]
    },
    {
      "question": "첫 번째 버전에 넣을 핵심 기능 조합을 골라주세요",
      "header": "MVP",
      "multiSelect": false,
      "options": [
        {"label": "(동적) 조합A (추천)", "description": "(동적) 가장 간단한 핵심 조합. 빠르게 완성 가능."},
        {"label": "(동적) 조합B", "description": "(동적) 기능 더 많지만 시간이 더 걸려요."},
        {"label": "(동적) 조합C", "description": "(동적) 풀 기능 버전. 시간이 가장 오래 걸려요."},
        {"label": "잘 모르겠어요", "description": "추천대로 갈게요. 가장 간단한 핵심 조합으로 시작합니다."}
      ]
    }
  ]
})

Step 2 답변 받으면 → 즉시 Step 3로.

---

## Step 3: 데이터 모델 확인

선택된 기능에서 핵심 데이터를 자동 추출하여 AskUserQuestion의 markdown preview로 보여준다.
텍스트로 먼저 설명하지 않는다.

아래 도구의 (동적) 필드를 추출된 데이터 모델로 채운 후 즉시 호출한다:

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
          "markdown": "(동적) 아래 형식:\n\n## 핵심 데이터 구조\n\n```\n[엔티티1] ── [설명]\n  └── [엔티티2] ── [설명]\n```\n\n| 데이터 | 설명 | 예시 |\n|--------|------|------|\n| (동적) | (동적) | (동적) |"
        },
        {
          "label": "수정할 부분이 있어요",
          "description": "어떤 부분을 바꾸고 싶은지 알려주세요.",
          "markdown": "(동적) 위와 동일한 구조도"
        },
        {
          "label": "잘 모르겠어요",
          "description": "추천대로 갈게요.",
          "markdown": "(동적) 위와 동일한 구조도"
        }
      ]
    }
  ]
})

Step 3 답변 받으면 → 즉시 Step 4로.

---

## Step 4: Phase 분리 확인

기능 복잡도와 의존성을 기반으로 자동 Phase 분리 후 AskUserQuestion의 markdown preview로 보여준다.
텍스트로 먼저 설명하지 않는다.

아래 도구의 (동적) 필드를 Phase 분리 결과로 채운 후 즉시 호출한다:

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
          "markdown": "(동적) 아래 형식:\n\n## Phase 분리 계획\n\n### Phase 1 (MVP)\n- [ ] 기능1\n- [ ] 기능2\n\n### Phase 2\n- [ ] 기능A\n- [ ] 기능B\n\n### Phase 3\n- [ ] 기능X"
        },
        {
          "label": "순서를 바꾸고 싶어요",
          "description": "어떤 기능을 먼저 만들고 싶은지 알려주세요.",
          "markdown": "(동적) 위와 동일한 Phase 체크리스트"
        },
        {
          "label": "Phase를 합치거나 나누고 싶어요",
          "description": "어떻게 바꾸고 싶은지 알려주세요.",
          "markdown": "(동적) 위와 동일한 Phase 체크리스트"
        }
      ]
    }
  ]
})

Step 4 답변 받으면 → WebSearch "best tech stack for {플랫폼} {도메인} app 2026" 리서치 → 즉시 Step 5로.

---

## Step 5: 기술 스택 선택

리서치 기반으로 프로젝트에 맞는 기술 스택 2-3개를 추천한다.

아래 도구의 (동적) 필드를 리서치 결과로 채운 후 즉시 호출한다:

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
          "markdown": "(동적) 아래 형식:\n\n## 기술 스택 비교\n\n| 항목 | 스택A | 스택B | 스택C |\n|------|---|---|---|\n| 무료 시작 | (동적) | (동적) | (동적) |\n| AI 코딩 호환 | (동적) | (동적) | (동적) |\n| 배포 난이도 | (동적) | (동적) | (동적) |"
        },
        {
          "label": "(동적) 스택B",
          "description": "(동적) 한 줄 요약.",
          "markdown": "(동적) 위와 동일한 비교 테이블"
        },
        {
          "label": "(동적) 스택C",
          "description": "(동적) 한 줄 요약.",
          "markdown": "(동적) 위와 동일한 비교 테이블"
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

Step 5 답변 받으면 → 즉시 Step 6으로.

---

## Step 6: 문서 생성

수집된 모든 정보를 종합하여 PRD/ 폴더에 4종 문서를 Write한다:
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

## Step 7: 완료 + 다음 단계 안내

문서 생성 완료 후:
1. 품질 요약: "완성도: X/10" + 개선 포인트
2. 문서 위치: PRD/ 폴더 안내
3. 다음 단계: Phase 1 시작 / SDD 도구 연계 / 문서 수정
