# {{PROJECT_NAME}} — 협업 가이드 (CLAUDE.md)

> 이 파일은 **이 프로젝트에 참여하는 모든 사람과 모든 AI**가 최우선으로 읽는 협업 헌법입니다.
> 개인 글로벌 설정(`~/.claude/CLAUDE.md` 등)보다 이 파일이 우선합니다.
> 큰 원칙만 여기 두고, 세부는 영역별 `CLAUDE.md`와 `docs/` 하위로 위임합니다(Progressive Disclosure).

## 프로젝트 개요

- 목적: {{PROJECT_PURPOSE}}
- 스택: {{STACK_SUMMARY}}
- 호스팅: {{REPO_URL}}
- 배포: {{DEPLOY_TARGET}}

## 역할 분담

| 사람 | 계정 | 영역 | 책임 |
|------|------|------|------|
| {{OWNER_NAME}} | {{OWNER_HANDLE}} (Owner) | 전 영역 총괄 | 설계·계약·인프라·배포·최종 승인 |
| {{MEMBER_A_NAME}} | {{AREA_OWNER_A}} | {{AREA_PATH_A}} | 구현 |
| {{MEMBER_B_NAME}} | {{AREA_OWNER_B}} | {{AREA_PATH_B}} | 구현 |

> 혼자 진행 시: 위 표를 Owner 한 줄로 축약하고, 경계는 사람이 아니라 **계층**에만 둔다(아래 "계층 규율").

### 계층 규율 (고비용 계층 보호)

아래 경로는 함부로 고치지 않는다. 흔들리면 파급이 커 품질이 무너지는 계층이다.

| 경로 | 내용 | 고칠 때 |
|------|------|---------|
| {{PROTECTED_PATH_1}} | {{PROTECTED_DESC_1}} | {{PROTECTED_PROCEDURE_1}} |
| {{PROTECTED_PATH_2}} | {{PROTECTED_DESC_2}} | {{PROTECTED_PROCEDURE_2}} |

## 폴더 구조 요약

```
{{PROJECT_NAME}}/
├── {{AREA_PATH_A}}/   ← {{AREA_A_DESC}}
├── {{AREA_PATH_B}}/   ← {{AREA_B_DESC}}
├── {{SHARED_PATH}}/   ← 계약·인프라 (Owner 전담)
└── docs/             ← 사람·AI 공용 문서 (specs/·plans/ 포함)
```

## 작업 흐름

### 새 기능

1. brainstorming → `docs/specs/`에 spec 작성·커밋
2. writing-plans → `docs/plans/`에 plan 작성·커밋 (Task 단위)
3. `{{DEFAULT_BRANCH}}`에서 `feature/` 브랜치 생성 → plan대로 구현
4. 매 작업 TDD (실패 테스트 먼저)
5. 변경 제안 전 검증 + 리뷰 요청
6. 리뷰 → Squash merge
7. `{{DEFAULT_BRANCH}}` 머지 → 자동 배포

### 버그 수정

1. 근본 원인 진단(증상 아님) → 진단 결과를 변경 제안 설명에 첨부
2. 실패 재현 테스트 작성 → 수정 → 변경 제안

### Trivial 예외

오타·문서·한 줄 수정은 spec/plan 생략 가능. 단 **변경 제안·리뷰는 반드시 거침**.

## 커밋 규칙 — Conventional Commits

```
feat({{AREA}}): ...     # 기능
fix({{AREA}}): ...      # 버그
feat(api): ...          # 계약 변경(양쪽 영향)
chore(infra): ...       # 인프라·CI
docs: ...               # 문서
```

## 변경 제안(MR/PR) 규칙

1. 변경 제안 1개 = 작은 작업 1개. 거대 변경은 분할.
2. 제목은 Conventional Commits 형식(Squash 시 커밋 메시지).
3. 템플릿 체크리스트를 모두 채운다.
4. CI 통과 필수.
5. 혼자여도 **변경 제안 자체는 유지** — 기본 브랜치가 protected이고, CI 실행점이자 변경 이유를 남기는 곳.

## 절대 하지 말 것

1. 비밀키·`.env`·인증서 커밋 금지 (`.gitignore` + 훅이 차단)
2. `{{DEFAULT_BRANCH}}` 직접 push 금지 (Protected Branches)
3. 계약({{CONTRACT_ARTIFACT}}) 변경과 그 계약에 의존하는 코드를 같은 변경 제안에 섞지 않음 — **계약 먼저 배포 후 코드**(lockstep)
4. 계층 규율 경로를 기준 대조 없이 수정 금지
5. `git push --force`, `git reset --hard` 금지
6. 파괴적 명령 임의 실행 금지 — 막히면 이슈로 남김

## 문서 인덱스

| 문서 | 내용 |
|------|------|
| `docs/ARCHITECTURE.md` | 시스템 구조 |
| `docs/specs/`·`docs/plans/` | 설계서·작업지시서 |
| `{{AREA_PATH_A}}/CLAUDE.md` | {{AREA_A}} 영역 규칙 |
| `{{AREA_PATH_B}}/CLAUDE.md` | {{AREA_B}} 영역 규칙 |
