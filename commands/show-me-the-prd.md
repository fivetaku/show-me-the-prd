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
  - Task
  - Glob
  - Grep
  - Bash
  - Skill
---

# /show-me-the-prd Command

인터뷰 기반으로 바이브코더의 아이디어를 4종 디자인 문서(PRD + 데이터 모델 + Phase 분리 + 프로젝트 스펙)로 변환한다.

## Parse Arguments

Inspect `$ARGUMENTS` to determine the action:

| Argument Pattern | Action | Skill |
|-----------------|--------|-------|
| `[아이디어 설명]` | 인터뷰 시작하여 PRD 생성 | show-me-the-prd |
| (no argument) | 인터랙티브 메뉴 표시 | See below |

## No Argument Provided

When no argument is provided, present the following interactive menu using `AskUserQuestion`:

```json
{
  "questions": [
    {
      "question": "어떤 걸 도와드릴까요?",
      "header": "PRD",
      "options": [
        {
          "label": "새 프로젝트 기획 (추천)",
          "description": "아이디어 하나만 말해주세요. 인터뷰를 통해 PRD, 데이터 모델, Phase 분리, 프로젝트 스펙 4종 문서를 만들어드려요."
        },
        {
          "label": "기존 기획 보강",
          "description": "이미 기획한 내용이 있다면, 부족한 부분을 찾아서 보완해드려요. 기획 문서나 메모를 공유해주세요."
        },
        {
          "label": "사용법 안내",
          "description": "Show Me The PRD가 뭔지, 어떻게 쓰는지 알려드려요."
        }
      ],
      "multiSelect": false
    }
  ]
}
```

After user selection:
- **새 프로젝트 기획** -> Ask for one-sentence idea if not provided, then invoke show-me-the-prd skill
- **기존 기획 보강** -> Ask user to share their existing document, then invoke show-me-the-prd skill in enhancement mode (skip Step 1, start from gap analysis)
- **사용법 안내** -> Explain in plain Korean:
  1. 한 문장으로 아이디어를 말하면 인터뷰가 시작됨
  2. 5-6번의 질문에 답하면 4종 문서가 완성됨
  3. 모든 질문에 설명과 장단점이 있으니 읽고 고르면 됨
  4. 모르겠으면 (추천) 표시된 걸 고르면 됨

## Execute

Once the action is determined, follow the show-me-the-prd skill's execution flow.

Skill content is located at:
- `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/SKILL.md` -- 7단계 인터뷰 기반 PRD 생성 워크플로우
