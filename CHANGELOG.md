# Changelog

## [0.3.3] - 2026-03-02

### Fixed
- AskUserQuestion 도구 호출 보장을 위한 SKILL.md 전면 개선 (7가지 근본 원인 해결)
  - 실행 앵커 강화: "WHEN TRIGGERED" 섹션을 구체적 실행 지시서(한국어)로 교체
  - 모든 AskUserQuestion 블록에 "EXECUTE:" 명령형 키워드 적용 (Step 1-5 전체)
  - Step 1 조건 분기 정리: "먼저 텍스트로 물어본 뒤" → Q0 주입 패턴으로 교체
  - Step 2 "먼저 텍스트로 기능 목록" 제거 → AskUserQuestion 옵션에 포함
  - Step 3 "텍스트로 데이터 구조 설명" 제거 → markdown 프리뷰로 대체
  - Step 4 "텍스트로 Phase 설명" 제거 → markdown 프리뷰로 대체
  - 모든 `{placeholder}` → `(동적: 설명)` 패턴으로 교체 (Step 2-5)
  - 질문 설계 규칙 테이블의 부정형 지시 → 긍정형 "EXECUTE:" 참조로 변경

## [0.3.2] - 2026-03-02

### Fixed
- Command file Execute 섹션: "located at" 정보 제공 → 명시적 Read 지시로 변경
  - SKILL.md + interview-guide.md 파일을 반드시 Read하도록 번호 리스트 추가
  - AskUserQuestion 도구 호출 필수 규칙 명시
  - 이 변경으로 SKILL.md가 로드되지 않아 AskUserQuestion이 텍스트로 출력되던 문제 해결

## [0.3.1] - 2026-03-02

### Fixed
- AskUserQuestion pseudo-code 7개를 JSON 형식으로 전환 — 도구 호출 보장
  - Step 1-5 질문 블록 전부 JSON 변환 + markdown preview 필드 추가

## [0.3.0] - 2026-02-25

### Changed
- CCPS v2.0 플러그인 표준으로 전체 구조 리팩토링
- SKILL.md frontmatter 추가 (name, description + 트리거 키워드)
- README.md를 CCPS v2.0 템플릿에 맞게 재작성

## [0.2.0] - 2026-02-24

### Changed
- 인터뷰 흐름 개선
- 4종 디자인 문서 출력 형식 정리

## [0.1.0] - 2026-02-23

### Added
- 최초 릴리스
- 인터뷰 기반 PRD 생성 스킬
- 데이터 모델, Phase 분리, 프로젝트 스펙 자동 생성
