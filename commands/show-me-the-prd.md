---
name: show-me-the-prd
description: "선초안 기반 PRD 생성 -- 핵심만 묻고 바로 초안, 가정은 일괄 확인 (한 문장에서 4종 디자인 문서까지)"
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

<!-- first-run setup: idempotent, non-blocking, self-skips after first run -->
**Step 0 — run once at the very start, before anything else:** run `bash "${CLAUDE_PLUGIN_ROOT}/setup/setup.sh" ask`. If its output starts with `STAR_ASK`, immediately call the **AskUserQuestion** tool once, with the question and options phrased **in the user's language**: prefer the current conversation's language if it is evident; otherwise fall back to the language code that follows `STAR_ASK` in the output (`ko`→Korean, `ja`→Japanese, `en`→English). Never default to Korean blindly.
- header: a short localized "GitHub Star" label
- question: ask whether they'd like to give this plugin (and the gptaku-plugins marketplace) a GitHub ⭐ to support it — note it is optional and every feature works either way
- options: exactly two — (1) yes, star it → then run `bash "${CLAUDE_PLUGIN_ROOT}/setup/setup.sh" star yes`; (2) no thanks → then run `bash "${CLAUDE_PLUGIN_ROOT}/setup/setup.sh" star no`

If the output is empty, just continue silently. (AskUserQuestion must NOT be in frontmatter allowed-tools.) Do not narrate beyond the question itself.

# /show-me-the-prd Command

**선초안(Draft-First)** 방식으로 바이브코더의 아이디어를 4종 디자인 문서로 변환한다.
질문으로 사양을 다 채운 뒤 쓰는 게 아니라, **핵심 문제만 깊게 묻고 → 방향 결정 1콜 → 바로 초안 → 가정 일괄 확인**으로 간다. 초안이 가장 좋은 질문 생성기다.

아이디어: `$ARGUMENTS`

## 규칙 (정책: shared/questioning-policy.md §1·§2·§6)

- **질문 예산: 열린 탐침 1회(§2a 재탐침 +1) + AskUserQuestion 카드 2콜(콜당 ≤4문항).** 예산 초과가 필요해 보이면 질문 설계 실패다 — 기본값+가정 원장으로 돌려라. 예외: 사용자가 명시적으로 깊은 인터뷰를 요청한 경우만 해제.
- **사실 vs 결정 (§6b)**: 조사(WebSearch/코드)로 알 수 있는 *사실*은 묻지 않는다 — 조사해서 카드 옵션에 녹인다. *결정*(목표·범위·트레이드오프·원하는 동작)만 사용자에게. 애매하면 결정으로 취급.
- 카드 질문은 반드시 AskUserQuestion으로 호출한다. **단 Turn 1의 핵심 문제 발굴은 열린 텍스트 질문**으로 한다(과거 행동을 묻는 Mom Test식 — 객관식으로 가두지 않는다).
- 리서치 결과를 텍스트로 설명하지 않는다. 결과는 즉시 다음 카드의 옵션에 녹인다.
- 옵션 description에 "뭔지 + 장점 + 단점". 추천 옵션은 (추천) + 첫 번째. 기술 용어 대신 일상 비유.
- **중단 신호 존중 (§2c)**: 사용자가 "알아서 해줘"류로 답하면 남은 문항을 기본값+원장으로 돌리고 즉시 Turn 3 초안으로 직행한다.

## Turn 0: 사전 확인 (자동)

Glob으로 플러그인 의존성을 확인한다 (유저에게 묻지 않음):
- `$HOME/.claude/plugins/cache/*/docs-guide` → 있으면 기술 문서 조사에 활용
- `$HOME/.claude/plugins/cache/*/insane-research` → 있으면 종합 리서치에 활용
- 둘 다 없으면 WebSearch 폴백

## Turn 1: 핵심 문제 발굴 (Mom Test식 열린 질문)

$ARGUMENTS를 분석해 표면 기능 뒤의 **"진짜 문제"**를 끌어낸다. 이 Turn만큼은 깊게 — 선초안이 대체하지 못하는 유일한 단계다(A/B 검증에서 가장 깊은 잠재 니즈는 2차 탐침에서만 나왔다).

- **핵심 문제는 열린 텍스트 질문으로, 과거 행동을 묻는다:**
  - "지금은 이 문제를 어떻게 해결하고 계세요?" · "최근에 가장 불편했던 순간을 한 장면으로 들려주세요."
  - 가정형("이런 기능 쓰실래요?") 금지. "왜" 대신 "무엇/어떻게".
- **조기 종료 금지 (§2a):** 첫 답이 표면적이면("그냥 기록 앱이요") 결론으로 받지 말고, 한 문장 **반영**한 뒤 **한 번 더 구체적으로 탐침**한다 — "그 방식에서 제일 막히거나 그만두게 된 지점이 언제였어요?". 기존 방식이 왜 안 되는지(진짜 문제)가 드러날 때까지.
- **과잉질문 가드 (§2c):** $ARGUMENTS가 이미 문제를 구체적으로 설명했으면 탐침을 생략하고 바로 Turn 2로.
- 핵심 문제 + 원하는 결과가 잡히면 한 문장으로 반영·확정하고 Turn 2로.

$ARGUMENTS가 없으면 "어떤 문제 때문에 이 앱/서비스를 생각하게 되셨어요?" 한 질문으로 시작한다.

## Turn 1 → Turn 2 전환

답변을 받으면 WebSearch로 "{아이디어} app features best practices 2026" + "best tech stack for {플랫폼} {도메인} app 2026" 리서치. 텍스트로 설명하지 말고 즉시 Turn 2의 카드를 호출한다.

## Turn 2: 방향 결정 카드 (AskUserQuestion 1콜, ≤4문항)

초안의 방향을 가르는 **결정**만 한 콜에 배치한다. **추론으로 확정된 항목은 문항에서 뺀다 — 4문항을 채우는 게 목표가 아니다.**

Use AskUserQuestion to ask (한 번의 호출에 questions 배열로):
1. **MVP 범위** — 리서치 기반 기능 조합 3개(최소/중간/풀). 각 조합에 포함 기능 + 복잡도(간단/보통/복잡) 한 줄.
2. **기술 방향** — 스택 후보. preview 필드에 비교표(무료여부/AI코딩 호환/커뮤니티/배포 난이도).
3. **로그인 방식** — 소셜 (추천) / 이메일+비밀번호 / 매직링크 / 불필요.
4. **플랫폼 확인** — 추론 결과를 기본값+확인형으로 ("모바일 웹으로 가정했어요 — 맞나요?"). 추론이 확실하면 이 문항은 빼고 원장에 기록.

## Turn 3: 초안 작성 (질문 없음)

카드 답을 받으면 **즉시** PRD/ 폴더에 5개 파일을 Write한다:
- `PRD/01_PRD.md` — Product Requirements Document
- `PRD/02_DATA_MODEL.md` — 데이터 모델
- `PRD/03_PHASES.md` — Phase 분리 계획 (Phase 1 = MVP, Phase 2 = 확장, Phase 3 = 고도화)
- `PRD/04_PROJECT_SPEC.md` — 프로젝트 스펙 (AI 행동 규칙)
- `PRD/README.md` — 네비게이션

문서 템플릿: Read `${CLAUDE_PLUGIN_ROOT}/skills/show-me-the-prd/references/document-templates.md`

**핵심 규칙:**
- **데이터 모델과 Phase 분리는 묻지 않고 설계해서 초안에 반영한다.** (확인은 Turn 4에서 구체물로 받는다 — 백지 질문보다 초안 교정이 정확하다는 게 A/B 검증 결과다.)
- 미확정 지점 처리 (§6c):
  - 산출물 구조를 갈라놓는 **blocking 결정** → 질문 큐에 적립 (Turn 4 카드에 합류)
  - 그 외 → **시장 일반 관행 기준 기본값** 채택 + 가정 원장에 기록
- **가정 원장 규약**: 가정이 반영된 문서 위치마다 `> ⚠️ 가정: {내용} — 아니라면 알려주세요` 인용 블록을 넣고, `PRD/01_PRD.md` 말미에 `## 가정 원장` 섹션으로 전체 목록(번호 / 가정 내용 / 영향 문서 / 확인 여부)을 모은다.

## Turn 4: 확인 카드 (AskUserQuestion 1콜, ≤4문항)

초안 위치를 한 줄로 알린 뒤 즉시 카드를 호출한다. 텍스트로 문서 내용을 다시 설명하지 않는다.

Use AskUserQuestion to ask (한 번의 호출에 questions 배열로):
1. **데이터 모델** — preview 필드에 ASCII 관계도 + 필드 테이블. 좋아요 (추천) / 수정할 부분 있어요.
2. **Phase 분리** — preview 필드에 Phase별 기능 체크리스트. 좋아요 (추천) / 순서 변경 / 합치기·나누기.
3~4. **가정 원장에서 틀렸을 때 영향이 큰 가정 1~2개** + Turn 3에서 적립된 blocking 큐 항목. 각각 기본값을 첫 옵션(추천)으로.

수정 답변은 해당 문서에 반영하고 원장의 확인 여부를 갱신한다. 수정이 연쇄적으로 커지는 경우에만(구조 변경) 추가 확인 1회를 예외 허용한다.

## Turn 5: 납품

교정 반영 후 마무리:
- 완성도 X/10 + 개선 포인트
- **잔여 가정 목록** — 확인받지 못한 원장 항목을 그대로 노출한다 (사용자가 나중에 훑고 고칠 수 있게. 어떤 인터뷰로도 못 잡는 잠재 항목의 유일한 방어선이다.)
- 문서 위치 + 다음 단계 가이드 (골 기반 실행이 필요하면 `/goaljaby`로 이 PRD 폴더를 넘기라고 안내 — 가정 원장은 goaljaby의 검증 문서가 참조할 수 있다)
