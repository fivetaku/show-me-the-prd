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

**규칙: 모든 질문은 반드시 AskUserQuestion 도구로 호출한다. 텍스트로 질문하지 않는다.**
**규칙: 답변을 받으면 즉시 다음 Step의 AskUserQuestion을 호출한다. 멈추지 않는다.**
**규칙: 모든 옵션 description에 "뭔지 + 장점 + 단점". 추천 옵션은 "(추천)" + 첫 번째. 기술 용어 대신 일상 비유.**

아이디어: `$ARGUMENTS`

인수 없으면 아래 메뉴를 AskUserQuestion으로 표시:
- "새 프로젝트 기획 (추천)" / "기존 기획 보강" / "사용법 안내"
- 새 프로젝트 → 아이디어 한 문장 받고 Step 1로
- 기존 기획 → 문서 받아서 갭 분석 후 필요한 Step만
- 사용법 → 한국어로 간단 설명

---

## Step 1: 아이디어 탐색 → AskUserQuestion 호출

$ARGUMENTS 있으면 Q1 스킵. 최대 4개 질문 묶어서 AskUserQuestion 한 번에 호출:
- Q1 (아이디어 없을 때만): "어떤 문제를 해결하는 앱인가요?" 자유입력
- Q2 (header: 사용자): 누가 쓸 건지 — 나만(추천)/주변/불특정 다수
- Q3 (header: 경험): 비슷한 앱 써봤는지 — 불만있었음/아쉬웠음/없음
- Q4 (header: 플랫폼): 웹(추천)/모바일/둘다/모르겠어요

답변 받으면 → WebSearch "{도메인} app features 2026" 리서치 → 즉시 Step 2 AskUserQuestion 호출.

## Step 2: 기능 정리 → AskUserQuestion 호출

리서치 결과로 기능 3-5개를 옵션으로 AskUserQuestion 호출:
- Q1 (header: 핵심 기능, multiSelect: true): 필요한 기능 — 각 옵션에 복잡도 표시
- Q2 (header: MVP): Q1 기반 조합 — 추천 조합 첫 번째

답변 받으면 → 즉시 Step 3 AskUserQuestion 호출.

## Step 3: 데이터 구조 → AskUserQuestion 호출

선택된 기능에서 핵심 엔티티 추출. AskUserQuestion 호출:
- Q1 (header: 데이터, multiSelect: false): 데이터 구조 확인 — markdown에 ASCII 관계도 + 필드 테이블 표시. 좋아요(추천)/수정/모르겠어요

답변 받으면 → 즉시 Step 4 AskUserQuestion 호출.

## Step 4: Phase 분리 → AskUserQuestion 호출

기능 복잡도 기반 Phase 자동 분리. AskUserQuestion 호출:
- Q1 (header: Phase, multiSelect: false): Phase 분리 확인 — markdown에 Phase별 기능 체크리스트 표시. 좋아요(추천)/순서변경/합치기나누기

답변 받으면 → WebSearch 기술 스택 리서치 → 즉시 Step 5 AskUserQuestion 호출.

## Step 5: 기술 스택 → AskUserQuestion 호출

리서치 기반 2-3개 스택 추천. AskUserQuestion 호출:
- Q1 (header: 기술 스택): 도구 선택 — 무료여부/AI호환/커뮤니티/배포난이도
- Q2 (header: 인증): 로그인 방식 — 소셜(추천)/이메일/매직링크/불필요

답변 받으면 → Step 6으로.

## Step 6: 문서 생성

PRD/ 폴더에 4종 문서 Write:
- 01_PRD.md (제품 요구사항), 02_DATA_MODEL.md (데이터 모델), 03_PHASES.md (Phase 계획), 04_PROJECT_SPEC.md (프로젝트 스펙), README.md
- 모든 선택에 "왜 이걸 골랐는지" 근거 포함
- Phase별 체크리스트 포함
- 프로젝트 스펙에 "절대 하지 마" 목록 포함

## Step 7: 완료 안내

문서 위치 + 다음 단계(Phase 1 시작 / SDD 도구 연계 / 문서 수정) 안내.
