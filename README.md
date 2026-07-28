# project-init

프로젝트명 · 기술스택 · 개요서(기획서)를 받아, 그 프로젝트에 맞는 **개발 가이드라인과
협업/개인 규율 뼈대**를 리포지토리에 생성하는 [Claude Code](https://claude.com/claude-code) 스킬입니다.

산출물은 규칙 문서 몇 개가 아니라 **서로 맞물려 자동으로 규율을 집행하는 체계**입니다.

## 무엇을 만드나

```
리포 루트/
├── CLAUDE.md                     ← 협업 헌법 (사람·AI가 최우선으로 읽음)
├── {area}/CLAUDE.md              ← 영역별 규칙 (스택·계층 책임)
├── .github/CODEOWNERS 또는 .gitlab/CODEOWNERS
├── .github/workflows/ci.yml 또는 .gitlab-ci.yml   ← 경로 기반 품질 게이트
├── .pre-commit-config.yaml       ← 커밋 순간 보안·포맷 차단
├── .github/pull_request_template.md 또는 .gitlab/merge_request_templates/default.md
└── docs/
    ├── specs/spec.template.md    ← 설계서 스켈레톤
    └── plans/plan.template.md    ← 작업지시서 스켈레톤
```

## 핵심 철학

> **규율을 사람의 기억이 아니라 파일과 자동화에 걸어라.**
> 그러면 팀이 셋이든 하나든 방법론이 그대로 작동한다.

5대 원리(Governance-as-Code · 구조 경계 · 다층 게이트 · 계획 우선 · 계약 Lockstep)는
[`references/methodology.md`](references/methodology.md) 참조.

## 특징

- **호스팅 자동 감지** — git remote가 GitLab이면 GitLab 세트, GitHub이면 GitHub 세트만 생성 (둘 다 X)
- **협업/개인 모드** — 협업이면 CODEOWNERS 경로별 배정, 개인이면 `* @owner` 한 줄 + 협업 규칙 주석 보존
- **과분할 안 함** — 단일 영역이면 영역 CLAUDE.md·CI job을 억지로 늘리지 않음
- **계약 lockstep 조건부** — DB 스키마·API 계약이 있으면 배포 순서 규칙 반영, 없으면 생략
- **스택 무관** — React/FastAPI/Go/Rust/Next.js 등 스택별 명령을 레시피로 자동 매핑

## 설치

Claude Code 개인 스킬 디렉토리에 클론합니다.

```bash
git clone https://github.com/leemonta9482/project-init.git ~/.claude/skills/project-init
```

이후 Claude Code에서 `/project-init` 또는 "프로젝트 뼈대 잡아줘", "이 스택으로 프로젝트 구조 세팅해줘"
같은 말로 호출하면 발동합니다.

## 구성

```
project-init/
├── SKILL.md                     ← 워크플로 (입력수집 → 분석 → 모드 → 호스팅감지 → 템플릿채움 → 생성)
├── assets/templates/            ← 채워 넣을 템플릿 9종
└── references/
    ├── methodology.md           ← 5대 원리
    ├── collaboration-mode.md    ← 협업 모드 상세
    ├── solo-mode.md             ← 개인 모드 상세
    └── stack-recipes.md         ← 스택 → 명령·계층·계약물 매핑표
```

## 사용 흐름

1. `/project-init` 실행
2. 프로젝트명 · 기술스택 · 개요서 입력
3. 협업/개인 모드 선택 (협업이면 영역별 담당자)
4. 스킬이 placeholder 매핑표를 만들고 → 호스팅에 맞는 파일 세트를 리포에 생성
5. 사용자가 브랜치 protected 설정 + `pre-commit install` 등 후속 조치

> 스킬은 **파일 생성까지만** 하고, 커밋·push는 사용자가 결정합니다.
