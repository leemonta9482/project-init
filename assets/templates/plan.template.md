# {{FEATURE_NAME}} — 구현 계획 (plan)

<!-- 배치: docs/plans/YYYY-MM-DD-{{feature-slug}}.md -->
<!-- writing-plans 스킬로 작성. spec을 "어떻게"로 옮긴 작업지시서. Task 단위로 쪼갠다. -->
<!-- 각 Task는 그대로 실행 가능해야 한다: 파일·인터페이스·실패테스트·구현·커밋까지 명시. -->

- 생성일시: {{DATE}}
- 기준 spec: `docs/specs/{{feature-slug}}-design.md`

**Goal:** (한 문장)

**Architecture:** (구현 방식 요약. 기존 무엇을 재사용, 무엇이 신규.)

**Tech Stack:** {{STACK}}

## Global Constraints

- (전 Task 공통 제약. 예: 계약 변경 없음 → 마이그레이션 불필요 / 있음 → lockstep.)
- 자기 영역({{AREA_PATH}})만.

---

### Task 1: {{TASK_1_TITLE}}

**Files:**
- Create/Modify: `{{FILE}}`
- Test: `{{TEST_FILE}}`

**Interfaces:**
- Produces: `{{SIGNATURE}}`

- [ ] **Step 1: 실패하는 테스트**

```{{LANG}}
{{FAILING_TEST_CODE}}
```

Run: `{{TEST_CMD}} {{TEST_SELECTOR}}` → FAIL.

- [ ] **Step 2: 구현**

```{{LANG}}
{{IMPL_CODE}}
```

- [ ] **Step 3: 통과 + 커밋**

Run: `{{TEST_CMD}}` → PASS.

```bash
git add {{FILES}}
git commit -m "feat({{AREA}}): {{TASK_1_TITLE}}"
```

---

### Task 2: {{TASK_2_TITLE}}

(Task 1과 동일 구조로 반복)

---

### Task N: 전체 게이트 + 변경 제안

- [ ] **Step 1: full 게이트**

Run: `{{TEST_CMD}} && {{LINT_CMD}} && {{FORMAT_CHECK_CMD}}` → 전부 PASS·clean.

- [ ] **Step 2: 변경 제안**

- 제목: `feat({{AREA}}): {{FEATURE_NAME}}`
- 본문: Task 요약 + 게이트 결과 + (계약 변경 시) **lockstep 필요 명시**.

## 이후

- (배포 순서, 후속 작업, 타 영역 연계.)

## Self-Review (계획 검토)

- 스펙 커버리지: spec의 각 목표 → 어느 Task가 담당하는지. ✅
- Placeholder 없음(실제 코드로 채워짐). ✅
- 함정 선제 회피: (라우트 순서·계약 변경 순서·재사용 헬퍼 실존 등 미리 점검한 것.)
