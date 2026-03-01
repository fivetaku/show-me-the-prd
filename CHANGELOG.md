# Changelog

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
