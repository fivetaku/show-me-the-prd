---
name: show-me-the-prd
description: "인터뷰 기반 PRD 생성 -- 한 문장에서 4종 디자인 문서까지"
argument-hint: "[아이디어 설명]"
allowed-tools:
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

아이디어: `$ARGUMENTS`

## Execute

Read the skill and reference files, then follow the 7-step workflow:

1. Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/SKILL.md`
2. Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/interview-guide.md`
3. Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/research-strategy.md`
4. Follow SKILL.md's 7-step workflow with user's request: `$ARGUMENTS`
5. Every question MUST use the AskUserQuestion tool — NEVER output questions as text
