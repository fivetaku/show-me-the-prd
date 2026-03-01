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

# 실행 순서 — 반드시 이 순서대로

1. **Read 도구로 SKILL.md를 읽는다** (아래 경로):
   `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/SKILL.md`

2. SKILL.md에 적힌 7-Step 워크플로우를 그대로 실행한다.

아이디어: `$ARGUMENTS`

## 절대 규칙

- SKILL.md를 Read하기 전에는 어떤 질문도, 어떤 텍스트 출력도 하지 않는다.
- 모든 질문은 AskUserQuestion 도구 호출로만 한다. 텍스트로 질문하지 않는다.
- 각 Step의 JSON 템플릿을 그대로 사용한다.
