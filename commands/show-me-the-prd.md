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

## 규칙

- 모든 질문은 반드시 AskUserQuestion 도구로 호출한다. 텍스트로 질문하지 않는다.
- 리서치 결과를 텍스트로 설명하지 않는다. 결과는 즉시 다음 AskUserQuestion의 옵션에 녹인다.
- 옵션 description에 "뭔지 + 장점 + 단점". 추천 옵션은 (추천) + 첫 번째. 기술 용어 대신 일상 비유.

## Turn 0: 사전 확인 (자동)

Glob으로 플러그인 의존성을 확인한다 (유저에게 묻지 않음):
- `~/.claude/plugins/cache/*/docs-guide` → 있으면 기술 문서 조사에 활용
- `~/.claude/plugins/cache/*/deep-research` → 있으면 종합 리서치에 활용
- 둘 다 없으면 WebSearch 폴백

## Turn 1: 아이디어 분석 + 동적 질문

$ARGUMENTS를 깊이 분석하여 PRD 작성에 필요하지만 아직 불명확한 정보를 파악한다.

### 분석 체크리스트 (내부용 — 유저에게 보여주지 않음)

아래 항목을 $ARGUMENTS에서 추출 시도한다. 파악 안 되는 항목만 질문 대상:

| 카테고리 | 파악할 정보 | 질문이 필요한 경우 |
|----------|-----------|-----------------|
| 핵심 문제 | 어떤 문제/불편을 해결하는가 | $ARGUMENTS가 모호하거나 기능만 나열할 때 |
| 사용자 | 누가 쓰는가, 몇 명 정도 | 언급 없을 때 |
| 도메인 맥락 | 비슷한 서비스 경험, 차별점 | 경쟁 서비스 파악이 중요할 때 |
| 플랫폼 | 웹/모바일/둘다 | 언급 없을 때 |
| 규모/제약 | 예산, 기한, 기술 수준 | 규모가 클 수 있을 때 |

### 질문 생성 규칙

- $ARGUMENTS에서 이미 파악된 항목은 절대 질문하지 않는다.
- 질문은 최대 3개. PRD 품질에 가장 큰 영향을 미치는 순서로.
- 도메인에 따라 질문이 완전히 달라질 수 있다 (이커머스→결제, 소셜→피드 등).
- 각 옵션 description에 "뭔지 + 장점 + 단점". 추천은 (추천) + 첫 번째.
- 아이디어가 충분히 구체적이면 질문 1-2개만으로도 충분하다.

$ARGUMENTS가 없으면 "어떤 문제를 해결하는 앱/서비스인가요?" 한 질문만 한다. 답변 받으면 위 체크리스트로 재분석하여 추가 질문.

Use AskUserQuestion to ask only about unclear items.

## Turn 1 → Turn 2 전환

답변 받으면 WebSearch로 "{아이디어} app features best practices 2026" 리서치.
텍스트로 설명하지 말고 즉시 Turn 2의 AskUserQuestion 호출.

## Turn 2: 기능 선택

리서치 결과 기반으로 즉시 AskUserQuestion을 호출한다. Do not summarize findings in text.

Use AskUserQuestion to ask the user:
- (multiSelect: true) 핵심 기능 4개를 옵션으로 구성. 각 옵션에 복잡도(간단/보통/복잡) + 한 줄 설명.
- 첫 버전(MVP) 기능 조합 선택. 3가지 조합 제안 (최소/중간/풀).

## Turn 2 → Turn 3 전환

답변 받으면 선택된 기능에서 데이터 엔티티 추출.
텍스트로 설명하지 말고 즉시 Turn 3의 AskUserQuestion 호출.

## Turn 3: 데이터 모델 확인

추출된 데이터 모델을 AskUserQuestion의 markdown preview로 보여준다. Do not explain in text.

Use AskUserQuestion to ask the user (markdown preview 사용):
- 옵션의 markdown 필드에 ASCII 관계도 + 필드 테이블 포함
- 좋아요 (추천) / 수정할 부분 있어요 / 잘 모르겠어요

## Turn 3 → Turn 4 전환

답변 받으면 기능 복잡도/의존성 기반으로 Phase 자동 분리.
텍스트로 설명하지 말고 즉시 Turn 4의 AskUserQuestion 호출.

## Turn 4: Phase 분리 확인

Phase 분리 결과를 AskUserQuestion의 markdown preview로 보여준다. Do not explain in text.

Use AskUserQuestion to ask the user (markdown preview 사용):
- 옵션의 markdown 필드에 Phase별 기능 체크리스트 포함
- Phase 1 = MVP, Phase 2 = 확장, Phase 3 = 고도화
- 좋아요 (추천) / 순서 변경 / Phase 합치기/나누기

## Turn 4 → Turn 5 전환

답변 받으면 WebSearch로 "best tech stack for {플랫폼} {도메인} app 2026" 리서치.
텍스트로 설명하지 말고 즉시 Turn 5의 AskUserQuestion 호출.

## Turn 5: 기술 스택 선택

리서치 결과 기반으로 즉시 AskUserQuestion을 호출한다. Do not summarize findings in text.

Use AskUserQuestion to ask the user:
- (markdown preview 사용) 기술 스택 3개를 비교 테이블(무료여부/AI코딩 호환/커뮤니티/배포 난이도)로 보여줌
- 로그인 방식: 소셜 로그인 (추천) / 이메일+비밀번호 / 매직링크 / 불필요

## Turn 6: 문서 생성

모든 인터뷰 완료. PRD/ 폴더에 4종 문서를 Write:
- `PRD/01_PRD.md` — Product Requirements Document
- `PRD/02_DATA_MODEL.md` — 데이터 모델
- `PRD/03_PHASES.md` — Phase 분리 계획
- `PRD/04_PROJECT_SPEC.md` — 프로젝트 스펙 (AI 행동 규칙)
- `PRD/README.md` — 네비게이션

문서 템플릿: Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/document-templates.md`

## Turn 7: 완료 안내

완성도 X/10 + 개선 포인트 + 문서 위치 + 다음 단계 가이드.
