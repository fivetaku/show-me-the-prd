# Step별 AskUserQuestion JSON 템플릿

> SKILL.md의 각 Step에서 참조. (동적) 필드는 리서치/분석 결과로 채운다.

---

## Step 1b: 아이디어 없을 때 (조건 B)

```json
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
```

답변 후 조건 A(갭 분석 -> 동적 질문)로 재진입.

---

## Step 2: 기능 정리 + MVP 선택

```json
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
```

- (동적) 필드는 리서치 결과로 실제 값을 채운다
- 기능 설명은 각 option의 description에 포함 (별도 텍스트 출력 금지)

---

## Step 3: 데이터 모델 확인

```json
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
          "markdown": "(동적) 아래 형식으로 채운다:\n\n## 핵심 데이터 구조\n\n```\n[엔티티1] ── [일상 용어 설명]\n  └── [엔티티2] ── [일상 용어 설명]\n        └── [엔티티3] ── [일상 용어 설명]\n```\n\n| 데이터 | 설명 | 예시 |\n|--------|------|------|\n| (동적) | (동적) | (동적) |\n\n이 뼈대가 잘 잡혀있으면 나중에 기능 추가가 쉬워요."
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
```

- markdown preview에 ASCII 관계도 + 필드 테이블 포함
- multiSelect: false (markdown preview와 호환 필요)

---

## Step 4: Phase 분리 확인

```json
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
          "markdown": "(동적) 아래 형식으로 채운다:\n\n## Phase 분리 계획\n\n### Phase 1 (MVP) — [핵심 기능 요약]\n이것만으로도 실제로 쓸 수 있는 앱이 돼요.\n- [ ] (동적) 기능 1\n- [ ] (동적) 기능 2\n> 장점: 빠르게 결과물 확인 | 단점: 아직 [부가 기능] 없음\n\n### Phase 2 — [확장 기능 요약]\n- [ ] (동적) 기능 A\n> 장점: (동적) | 단점: Phase 1 완성 필수\n\n### Phase 3 — [고도화 요약]\n- [ ] (동적) 기능 X\n> 장점: (동적) | 단점: 비용/복잡도 높음\n\n---\n* 각 Phase 완료 기준: 실제 서버에 배포 + 실제 데이터 + 실제 로그인"
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
```

- multiSelect: false (markdown preview와 호환 필요)

---

## Step 5: 기술 스택 + 인증

```json
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
```

- Q1: (동적) 필드는 리서치 결과로 실제 값을 채운다. markdown preview에 스택 비교 테이블 포함.
- Q2: 인증 방식은 정적 JSON (그대로 사용)
