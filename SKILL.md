---
name: project-init
description: >-
  프로젝트명·기술스택·개요서(기획서)를 받아 그 프로젝트에 맞는 개발 가이드라인과
  협업/개인 규율 뼈대를 리포에 생성한다. CLAUDE.md(루트·영역), CODEOWNERS, CI
  파이프라인, pre-commit 훅, MR/PR 템플릿, spec·plan 템플릿을 스택에 맞게 채워
  넣는다. 새 프로젝트를 시작하거나 기존 프로젝트에 개발 규칙·협업 규율·CI·품질
  게이트·스캐폴드를 세팅할 때 사용한다. 사용자가 "프로젝트 뼈대 잡아줘", "개발
  가이드라인 만들어줘", "협업 규칙 세팅", "프로젝트 스캐폴드", "새 프로젝트
  셋업", "CLAUDE.md 만들어줘", "이 스택으로 프로젝트 구조 잡아줘", "governance
  세팅", "CI랑 규칙 좀 깔아줘" 등을 말하거나, 개요서/기획서를 주며 프로젝트를
  시작하려 할 때 반드시 사용한다. 규칙 문서 하나가 아니라 프로젝트 전체 규율
  체계를 세우는 작업이면 이 스킬을 쓴다.
---

# Project Scaffold — 프로젝트 규율 뼈대 생성기

프로젝트명 + 기술스택 + 개요서(기획서)를 입력받아, 그 프로젝트에 맞는
**개발 방법론 뼈대**를 리포지토리에 생성한다. 산출물은 규칙 문서 몇 개가 아니라
**서로 맞물려 자동으로 규율을 집행하는 체계**다.

## 이 스킬이 세우는 것 (한눈에)

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

## 핵심 철학 (왜 이렇게 하는가)

`references/methodology.md`에 5대 원리가 있다. **작업 시작 전에 반드시 읽는다.**
한 줄 요약: **규율을 사람의 기억이 아니라 파일과 자동화에 걸어라.** 그러면
팀이 셋이든 하나든 방법론이 그대로 작동한다. 생성하는 모든 파일이 이 원리의
구현체이므로, 원리를 이해해야 프로젝트에 맞게 변형할 수 있다.

---

## 워크플로

### 0단계 — 원리·레시피 로드

먼저 `references/methodology.md`(5대 원리)를 읽는다. 그 다음
`references/stack-recipes.md`(스택→명령·계층 매핑)를 열어둔다. 협업/개인 모드
세부는 각각 `references/collaboration-mode.md`·`references/solo-mode.md`에 있다.

### 1단계 — 입력 수집

세 가지를 확보한다. **없으면 사용자에게 묻는다.**

1. **프로젝트명** (`{{PROJECT_NAME}}`)
2. **기술스택** — 프론트/백/DB/모바일 등. 계열만 알아도 레시피로 명령 유추 가능.
3. **개요서/기획서** — 파일 경로나 붙여넣은 텍스트. 여기서 목적·영역·계약물을 읽는다.

개요서가 길면 훑어서 **영역·데이터 계약·핵심 기능 경계**만 추출한다(기능 상세는 불필요 — 우리가 만드는 건 규율 뼈대이지 기능 구현이 아니다).

### 2단계 — 대상 리포·호스팅 감지

- 대상 디렉토리를 확인한다(현재 작업 디렉토리가 기본, 아니면 사용자에게 확인).
- `git remote -v`로 호스팅 감지: **gitlab → `.gitlab-ci.yml` + `.gitlab/`**, **github → `.github/workflows/` + `.github/`**. 감지 실패·remote 없음이면 사용자에게 GitLab/GitHub 중 물어본다.
- 기존 리포면 이미 있는 `CLAUDE.md`·CI 파일을 먼저 읽고 **덮어쓰기 전에 사용자에게 확인**한다. 절대 조용히 기존 규칙을 밀어내지 않는다.

### 3단계 — 모드 결정 (협업 vs 개인) — 반드시 물어본다

개요서에 팀 구성이 명시돼 있어도, **실행 시마다 사용자에게 확인한다**:

> "이 프로젝트는 여러 명이 함께 진행하나요(협업), 혼자 진행하나요(개인)?
>  협업이면 영역별 담당자 계정을 알려주세요."

- **협업**: 영역별 담당자·리뷰어를 받아 `CODEOWNERS` 경로별 배정 + 리뷰 매트릭스 구성. 세부는 `references/collaboration-mode.md`.
- **개인**: `CODEOWNERS`는 `* {{OWNER_HANDLE}}` 한 줄, 협업 규칙은 주석 블록으로 보존(나중에 팀 합류 시 스위칭). 계층 규율은 자기 규율로 유지. 세부는 `references/solo-mode.md`.

### 4단계 — placeholder 매핑표 작성

`references/stack-recipes.md`를 참조해 아래를 채운 **매핑표를 먼저 만든다**(사용자에게 보여주고 확정받으면 좋다):

- `{{PROJECT_NAME}}`, `{{DEFAULT_BRANCH}}`(보통 main), `{{OWNER_HANDLE}}`
- 영역별: `{{AREA}}` / `{{AREA_PATH}}` / `{{STACK}}` / `{{PKG_MANAGER}}` / `{{LINT_CMD}}` / `{{FORMAT_CHECK_CMD}}` / `{{TEST_CMD}}` / `{{BUILD_CMD}}`
- `{{PROTECTED_LAYERS}}` — 레시피 3절로 판별한 고비용 계층
- `{{CONTRACT_ARTIFACT}}` + 경로 — 레시피 4절로 판별(DB 스키마·OpenAPI·proto 등). 없으면 "없음"으로 두고 lockstep 관련 문구를 생략.
- 협업이면 `{{AREA_OWNER_x}}`

> 영역이 하나뿐이면 `CLAUDE.area.md`를 만들지 않고 루트에 스택 규칙을 흡수한다(레시피 2절).

### 5단계 — 템플릿 채워 생성

`assets/templates/`의 각 파일을 읽어 매핑표로 **모든 `{{...}}`를 치환**한 뒤,
2단계에서 정한 호스팅에 맞는 경로로 리포에 **쓴다**. 치환 후 파일에
`{{`가 하나라도 남으면 안 된다(누락 = 미완성).

| 템플릿 | 쓸 위치 |
|--------|---------|
| `CLAUDE.root.md` | `<repo>/CLAUDE.md` |
| `CLAUDE.area.md` | `<repo>/{{AREA_PATH}}/CLAUDE.md` (영역마다, 다영역일 때만) |
| `CODEOWNERS` | github: `.github/CODEOWNERS` / gitlab: `.gitlab/CODEOWNERS` |
| `gitlab-ci.template.yml` 또는 `github-actions.template.yml` | 감지된 호스팅에 맞는 하나만 |
| `pre-commit-config.template.yaml` | `.pre-commit-config.yaml` |
| `merge_request_template.md` | github: `.github/pull_request_template.md` / gitlab: `.gitlab/merge_request_templates/default.md` |
| `spec.template.md` | `docs/specs/spec.template.md` |
| `plan.template.md` | `docs/plans/plan.template.md` |

- CI 템플릿의 영역별 job은 **실제 영역 수만큼** 복제/삭제한다(2영역이면 2개, 1영역이면 1개).
- pre-commit 포맷터 훅은 스택에 맞는 실제 훅으로 바꾼다(레시피 1절). 보안 훅은 그대로.
- 계약물이 "없음"이면 CI의 `{{CONTRACT_PATH}}` 트리거와 MR 템플릿의 lockstep 체크 항목을 제거한다.

### 6단계 — 요약 보고

생성한 파일 목록과, 사용자가 직접 해야 하는 **수동 후속 조치**를 알려준다:

- 호스팅에서 `{{DEFAULT_BRANCH}}` **Protected** + "파이프라인 통과 필수" 켜기 (스킬이 대신 못 함)
- `pre-commit install` 실행
- 협업이면 CODEOWNERS 승인 정책 켜기
- 첫 기능부터 spec→plan→구현 흐름 사용

절대 커밋·push까지 하지 않는다 — 파일 생성까지만. 커밋 여부는 사용자가 결정.

---

## 원칙

- **채움은 완전해야 한다.** placeholder가 남은 파일을 내보내지 않는다.
- **억지로 늘리지 않는다.** 단일 영역·계약물 없음이면 관련 파일·문구를 생략한다(원리: 있어야 할 것만).
- **기존 파일을 조용히 덮지 않는다.** 항상 읽고, 확인받고, 병합/교체를 명시한다.
- **기능이 아니라 규율을 만든다.** 개요서의 기능 상세에 끌려가지 말고, 영역·계약·경계만 뽑아 뼈대로 옮긴다.
- **커밋은 사용자 몫.** 생성까지만.

## 산출물 검증 (스스로 확인)

생성 후 아래를 점검한다:

- [ ] 모든 생성 파일에 `{{` 잔여 0건
- [ ] 영역 수 = CI 영역 job 수 = 영역 CLAUDE.md 수 (다영역일 때)
- [ ] 호스팅에 맞는 CI·CODEOWNERS·MR 템플릿 **한 세트만** 생성(둘 다 아님)
- [ ] 개인 모드면 CODEOWNERS가 `* {{OWNER_HANDLE}}` 형태 + 협업 블록 주석 보존
- [ ] 계약물 없으면 lockstep 관련 문구 제거됨
