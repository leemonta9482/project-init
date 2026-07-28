# 스택 레시피 — placeholder 채우기 참조표

프로젝트가 쓰는 기술스택을 아래 표에 대응시켜 placeholder를 채운다.
**표에 없는 스택이면**, 같은 계열의 관례(공식 포맷터·표준 테스트 러너·잠금파일 기반 설치)를
그대로 유추해 채운다. 지어내지 말고, 그 생태계의 표준 도구를 쓴다.

## 목차
1. 언어/프레임워크별 명령
2. 영역(area) 판별
3. 고비용 계층(protected layers) 판별
4. 계약물(contract artifact) 판별
5. CI 이미지·setup

---

## 1. 언어/프레임워크별 명령

| 스택 | `{{PKG_MANAGER}}` | `{{LINT_CMD}}` | `{{FORMAT_CHECK_CMD}}` | `{{TEST_CMD}}` | `{{BUILD_CMD}}` |
|------|------|------|------|------|------|
| React / Vite + TS | pnpm | `pnpm exec eslint .` | `pnpm exec prettier --check .` + `pnpm exec tsc -b` | `pnpm test` (Vitest) | `pnpm build` |
| Next.js + TS | pnpm | `pnpm lint` | `pnpm exec prettier --check .` | `pnpm test` | `pnpm build` |
| Vue + TS | pnpm | `pnpm lint` | `pnpm exec prettier --check .` | `pnpm test` | `pnpm build` |
| FastAPI / Python | uv | `uv run ruff check .` | `uv run ruff format --check .` | `uv run pytest -q` | (없음/컨테이너 빌드) |
| Django / Python | uv 또는 poetry | `ruff check .` | `ruff format --check .` | `pytest -q` 또는 `python manage.py test` | (없음) |
| Node/Express + TS | pnpm | `pnpm lint` | `pnpm exec prettier --check .` | `pnpm test` (Jest/Vitest) | `pnpm build` |
| Go | go (모듈) | `golangci-lint run` | `gofmt -l .` (빈 출력=OK) | `go test ./...` | `go build ./...` |
| Rust | cargo | `cargo clippy -- -D warnings` | `cargo fmt --check` | `cargo test` | `cargo build --release` |
| Spring / Java | gradle 또는 maven | `./gradlew check` | `./gradlew spotlessCheck` | `./gradlew test` | `./gradlew build` |
| Kotlin | gradle | `./gradlew ktlintCheck` | `./gradlew ktlintCheck` | `./gradlew test` | `./gradlew build` |
| Flutter / Dart | (dart/flutter) | `flutter analyze` | `dart format --set-exit-if-changed .` | `flutter test` | `flutter build` |
| Ruby on Rails | bundler | `bundle exec rubocop` | `bundle exec rubocop` | `bundle exec rspec` | (없음) |

> pre-commit 포맷터 훅도 여기서 유추: Python→ruff, JS/TS→prettier, Go→gofmt, Rust→cargo fmt.
> 보안 훅(gitleaks·detect-private-key·.env 차단)은 스택 무관하게 항상 포함.

---

## 2. 영역(area) 판별

개요서/스택에서 **독립 배포·독립 스택 단위**를 area로 나눈다.

| 신호 | area 구성 예 |
|------|-------------|
| 프론트+백 분리 | `frontend/` + `backend/` (2 area) |
| 모바일 포함 | + `mobile/` |
| 마이크로서비스 | `services/<name>/` 각각 |
| 단일 서비스/CLI/라이브러리 | area 구분 없음 — 루트 규칙만, 영역 CLAUDE.md 생략 가능 |
| 모노레포(공유 패키지) | `packages/<name>/` + 앱들 |

> area가 하나뿐이면 `CLAUDE.area.md`를 만들지 말고 루트 `CLAUDE.md`에 스택 규칙을 흡수한다.
> 억지로 나누지 않는다(원리: YAGNI).

---

## 3. 고비용 계층 `{{PROTECTED_LAYERS}}` 판별

"흔들리면 파급이 커 함부로 못 바꾸는 계층"을 스택에서 찾는다.

| 스택/상황 | 보호할 계층 예 | 왜 |
|-----------|---------------|-----|
| 디자인시스템 있는 프론트 | `src/styles/`(토큰), `src/components/ui/`(DS), `src/layout/`(셸) | 흔들리면 화면마다 골격 재발명 |
| 공유 커널/코어 | `src/core/`, `lib/shared/`, `packages/core/` | 전 모듈 의존 |
| 공개 API | `openapi.yaml`, `proto/`, `src/api/schema` | 외부/타 모듈 계약 |
| DB 있는 백엔드 | `migrations/`, `models.py`/`schema.prisma` | 데이터·마이그레이션 위험 |

> 개요서에 디자인시스템·공유 라이브러리 언급이 없으면, DB 스키마와 공개 API만 보호 계층으로 잡아도 충분하다.

---

## 4. 계약물 `{{CONTRACT_ARTIFACT}}` 판별 (lockstep 대상)

배포 순서를 타야 하는 공유 계약:

| 스택 | 계약물 | lockstep: 먼저 배포 |
|------|--------|---------------------|
| SQL DB(Postgres/MySQL) | 마이그레이션 | 스키마 먼저 → 그 컬럼 쓰는 코드 |
| Prisma/TypeORM | 마이그레이션 | 동일 |
| REST API | OpenAPI 스펙 | 서버(additive 필드) 먼저 → 클라이언트 |
| gRPC | `.proto` | 스키마 먼저 → 양측 코드 |
| 메시지 큐 | 메시지 스키마 | consumer(둘 다 수용) 먼저 → producer |
| 없음(정적/CLI) | 없음 | lockstep 규칙 생략 |

---

## 5. CI 이미지·setup 유추

| 스택 | CI 이미지 | setup 명령 |
|------|-----------|-----------|
| Python+uv | `python:3.12-slim` | `pip install uv && uv sync --frozen` |
| Node+pnpm | `node:20-alpine` | `corepack enable && pnpm install --frozen-lockfile` |
| Go | `golang:1.22` | `go mod download` |
| Rust | `rust:1` | `cargo fetch` |
| Java+gradle | `eclipse-temurin:21` | `./gradlew --version` |

> DB 필요한 test job은 서비스 컨테이너(`postgres:16-alpine` 등)를 붙이고 `DATABASE_URL` 주입.
