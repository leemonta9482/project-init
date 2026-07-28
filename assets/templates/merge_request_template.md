<!--
변경 제안 템플릿 — 배치:
  GitLab → .gitlab/merge_request_templates/default.md
  GitHub → .github/pull_request_template.md
-->

## 무엇을

(이 변경이 한 일을 한 문장으로)

## 왜

(이슈 링크 또는 spec/plan 링크)

## 어떻게 검증했는지

- [ ] 로컬에서 실제 동작 확인
- [ ] 테스트 추가/수정 (커밋에 반영)
- [ ] 검증 명령 결과 첨부 (아래)

```
{{TEST_CMD}}        → ... passed
{{LINT_CMD}}        → clean
```

## 체크리스트

- [ ] `feature/` 또는 `fix/` 브랜치에서 작업 (기본 브랜치 직접 커밋 안 함)
- [ ] Conventional Commits 형식 (`feat(...)`, `fix(...)` 등)
- [ ] 자기 영역만 수정 — 타 영역 수정 시 이유 명시
- [ ] 비밀키·`.env` 미포함
- [ ] pre-commit 훅 통과
- [ ] 계약({{CONTRACT_ARTIFACT}}) 변경이 있다면: 계약 변경 제안을 **먼저** 배포했는가(lockstep)

## BREAKING CHANGE?

- [ ] 있음 — 무엇이 깨지는지, 어떻게 대응할지 본문에 서술
- [ ] 없음
