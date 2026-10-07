# Cypress 저장소 전수조사 분석 보고서 (한국어)

> **저장소 주소**: https://github.com/bmshin94/cypress
> **원본(Upstream)**: https://github.com/cypress-io/cypress
> **공식 사이트**: https://www.cypress.io
> **작성일**: 2026-10-07
> **분석 브랜치**: `claude/tender-lovelace-8q90fi`

---

## 목차

1. [저장소 정체 — 이게 뭐야?](#1-저장소-정체--이게-뭐야)
2. [폴더 전수조사](#2-폴더-전수조사)
3. [Cypress가 하는 일 / 언제 쓰나](#3-cypress가-하는-일--언제-쓰나)
4. [나에게 주는 가치](#4-나에게-주는-가치)
5. [설치 및 사용법](#5-설치-및-사용법)
6. [플러그인 / 스킬 / MCP 구분](#6-플러그인--스킬--mcp-구분)
7. [API 토큰 필요 여부](#7-api-토큰-필요-여부)
8. [AI 에이전트 구축에 주는 도움](#8-ai-에이전트-구축에-주는-도움)
9. [React / PHP 로 만들 수 있나](#9-react--php-로-만들-수-있나)
10. [유튜브 강의 제작 가능성](#10-유튜브-강의-제작-가능성)
11. [수익화 아이디어 상세](#11-수익화-아이디어-상세)
12. [6개월 실행 로드맵](#12-6개월-실행-로드맵)
13. [참고 링크](#13-참고-링크)

---

## 1. 저장소 정체 — 이게 뭐야?

받은 저장소는 **Cypress(사이프레스)의 제품 소스코드 전체를 담은 모노레포**다.
단일 라이브러리가 아니라, Cypress라는 테스트 자동화 제품을 만드는 "공장" 전체다.

| 항목 | 내용 |
|---|---|
| 프로젝트명 | `cypress` (root `package.json` version: `0.0.0-development`) |
| 정체 | E2E / 컴포넌트 테스트 프레임워크 **본체 소스** |
| 라이선스 | **MIT** (상업적 이용·수정·재배포 전부 허용) |
| 규모 | 소스 파일 약 3,000개 이상, 총 100MB+ |
| 구조 | Yarn 1 (`yarn@1.22.22`) + Lerna 기반 모노레포 |
| 필요 Node (개발) | `24.15.0` (`.node-version`) |
| 필요 Node (사용자 CLI) | `^22.0.0 || ^24.0.0 || >=26.0.0` (`cli/package.json` `engines.node`) |
| 베이스 브랜치 | `develop` |
| 원격 | `origin → https://github.com/bmshin94/cypress` |

### 이 Fork에 추가된 커밋

```
7d85e47 Merge pull request #1 from bmshin94/feat/claude-guide
77856d8 docs: appended CLAUDE.md persona guide   ← CLAUDE.md 에 페르소나 26줄 추가
```

upstream 대비 변경점은 `CLAUDE.md` 하단의 페르소나 가이드 추가뿐이다.
즉, 이 Fork는 **"잘 만들어진 대규모 오픈소스에 AI 에이전트를 붙여 실험하는 교보재"** 로 쓰이고 있다.

---

## 2. 폴더 전수조사

### 2.1 `cli/` — 사용자가 `npm install cypress` 로 받는 패키지 (3.6MB)

| 경로 | 역할 |
|---|---|
| `cli/lib/cli.ts` | `cypress open` / `run` / `install` 명령어 파서 |
| `cli/lib/exec/run.ts` | 실제 실행 로직. `CYPRESS_RECORD_KEY` 환경변수를 읽는 지점 |
| `cli/lib/cypress-sessions/` | 실행 중인 세션 탐색 → CDP 연결 (`store.ts`, `liveness.ts`, `record.ts`, `index.ts`) |
| `cli/CHANGELOG.md` | 릴리즈 변경 이력 (semantic-release 로 관리) |
| `cli/types/` | 공개 TypeScript 타입 정의 |

### 2.2 `packages/` — 핵심 내부 패키지 37개 (64MB)

| 패키지 | 역할 |
|---|---|
| `driver` | **핵심 엔진.** 브라우저 안에서 `cy.get()`, `cy.click()` 등 모든 명령어 실행 |
| `app` | Vue 3 로 만든 Cypress GUI |
| `launchpad` | 프로젝트 선택 / 온보딩 / 스캐폴딩 UI |
| `reporter` | 테스트 통과·실패 트리 UI |
| `runner` | AUT(테스트 대상 앱) iframe 호스팅 + 드라이버 통신 |
| `frontend-shared` | `app` / `launchpad` 공용 Vue 컴포넌트 + 디자인 토큰 |
| `server` | HTTP 서버. 스펙 서빙, 브라우저 실행, 소켓 통신, 실행 오케스트레이션 |
| `proxy` | 브라우저 전체 트래픽 가로채는 프록시 |
| `https-proxy` | TLS 인터셉트용 HTTPS 프록시 |
| `net-stubbing` | `cy.intercept()` 표면 (드라이버 커맨드 + 서버 글루) |
| `network-interception` | `cy.intercept` 의 전송 비의존 코어 (라우트 매칭, 핸들러 병합) |
| `network`, `network-tools` | 저수준 / 고수준 네트워크 유틸 |
| `launcher` | Chrome / Firefox / Edge / WebKit / Electron 자동 탐지·실행 |
| `extension` | 브라우저에 주입되는 WebExtension |
| `electron` | Electron 런타임 래퍼 + 바이너리 빌드 + 자동 업데이트 |
| `config` | 설정 타입·기본값·검증 + 공개 `defineConfig` API |
| `data-context` | 앱용 GraphQL 데이터 액세스 레이어 |
| `scaffold-config` | 프레임워크 감지 + 설정 파일 생성 |
| `errors` | 에러 정의·템플릿·유틸 |
| `types` | 전역 공용 타입 |
| `socket` | 드라이버 ↔ 서버 WebSocket |
| `telemetry` | OpenTelemetry 래퍼 |
| `agent-info` | **AI 에이전트 탐지기** (아래 2.7 참고) |
| `cypress-sessions` | **세션 ↔ CDP 연결 규약** (아래 2.7 참고) |
| `example` | 연습용 kitchensink 샘플 프로젝트 |
| 기타 | `icons`, `stderr-filtering`, `resolve-dist`, `ts`, `eslint-config`, `web-config`, `root`, `v8-snapshot-require`, `packherd-require` |

### 2.3 `npm/` — npm 에 따로 공개되는 패키지 16개 (13MB)

- **컴포넌트 테스트 어댑터**: `@cypress/react`, `@cypress/vue`, `@cypress/angular`, `@cypress/svelte`, `@cypress/mount-utils`
- **번들러 연동**: `@cypress/webpack-dev-server`, `@cypress/vite-dev-server`, `@cypress/webpack-preprocessor`, `@cypress/webpack-batteries-included-preprocessor`, `@cypress/vite-plugin-cypress-esm`
- **플러그인 / 도구**: `@cypress/grep`(태그 필터), `@cypress/puppeteer`, `@cypress/schematic`(Angular CLI), `@cypress/xpath`, `@cypress/eslint-plugin-dev`

### 2.4 `tooling/` — 빌드 최적화 (1.7MB)

| 패키지 | 역할 |
|---|---|
| `@tooling/v8-snapshot` | Electron 시작 속도 최적화용 V8 스냅샷 생성 |
| `@tooling/packherd` | 엔트리포인트에서 도달 가능한 의존성을 단일 아티팩트로 번들 |
| `@tooling/electron-mksnapshot` | 대상 Electron 버전용 `mksnapshot` 래퍼 |

### 2.5 `system-tests/` (27MB) · `scripts/` (752KB)

- `system-tests/` — 실제 **빌드된 바이너리**로 돌리는 풀 E2E 스위트
- `scripts/` — 빌드·릴리즈·CI 자동화 (`binary.js`, `npm-release.js`, `semantic-commits/` 등)

### 2.6 AI 에이전트용 문서 계층 (가장 배울 가치가 큰 부분)

```
AGENTS.md                                 ← AI 가 읽는 프로젝트 지도 (모든 패키지마다 존재)
CLAUDE.md                                 ← Claude Code 전용 규칙 (@AGENTS.md 로 체이닝)
guides/ (24개 문서)                        ← 사람 + AI 공용 작업 매뉴얼 = 정본(source of truth)
.claude/skills/building-cypress-binary/    ← 바이너리 빌드 "실행" 노하우
.claude/skills/debugging-cypress-artifacts/← 패키징된 산출물 디버깅
.claude/rules/*.md (5개, paths: glob)      ← 매칭 파일 열 때만 자동 로드
.claude/settings.json                      ← 플러그인 2개 활성화 + 읽기 금지 경로
.cursor/skills/server-mocha-to-vitest/     ← Cursor 용 스킬
.cursor/BUGBOT.md, Dockerfile, environment.json
```

**4단 계층 설계 철학** (CONTRIBUTING.md 에 명문화):

| 계층 | 언제 쓰나 |
|---|---|
| `AGENTS.md` | 항상 읽히는 구조 설명 |
| `guides/` | **기본값.** 작업을 "제대로 하는 법" |
| `.claude/skills/` | 작업을 "실행하는 법"일 때만 (권한, 긴 명령, 호스트 특이사항) |
| `.claude/rules/` | 특정 경로 밖에서는 **틀린** 사실일 때만 |

`.claude/settings.json` 실제 내용:

```json
{
  "extraKnownMarketplaces": {
    "cypress": { "source": { "source": "github", "repo": "cypress-io/ai-toolkit" } },
    "claude-plugins-official": { "source": { "source": "github", "repo": "anthropics/claude-plugins-official" } }
  },
  "enabledPlugins": {
    "cypress@cypress": true,
    "typescript-lsp@claude-plugins-official": true
  },
  "permissions": { "deny": ["Read(./yarn.lock)", "..."] }
}
```

### 2.7 AI 관련 "실제 기능" 패키지 3종

#### (1) `packages/agent-info` — AI 에이전트 탐지기

- 질문 하나에만 답한다: **"이 Cypress 프로세스를 AI 코딩 에이전트가 띄웠나? 어떤 에이전트인가?"**
- 환경변수 블록의 지문을 마커 테이블과 대조해 **닫힌 집합의 고정된 이름**만 반환
- 의도적으로 **순수·무의존성 TypeScript** (`fs`/`http` 없음) → Node 측 패키지와 `cypress` CLI 둘 다 번들 가능
- 전체 구현이 `lib/index.ts` 한 파일

설계 교훈:
- **테이블 순서가 중요** — IDE 를 맨 뒤에 둬서, IDE 안의 에이전트가 "IDE"가 아닌 "에이전트"로 잡히게
- **고정된 이름만 머신 밖으로** — `AI_AGENT` 는 자유 형식이라 그대로 전달하지 않고 알려진 이름 또는 `'other'` 로 좁힘
- **TTY 게이트는 "사람" 쪽으로 편향** — stdin/stdout 둘 다 검사 (`cypress run | tee log.txt` 처럼 하나만 리다이렉트되는 경우 대비). *사람을 에이전트로 오인하는 게 에이전트를 놓치는 것보다 나쁘다*
- **경로 전체 매칭보다 구체적 마커 선호** — 홈 디렉터리 이름이 우연히 에이전트명인 경우 방지

#### (2) `packages/cypress-sessions` — 세션 ↔ CDP 연결 규약

외부 프로세스가 돌아가는 `cypress open` 세션을 찾아 **CDP로 붙는** 크로스 프로세스 계약.

```
생산자: @packages/server (lib/cypress-sessions.ts)
        → sessions/<pid>.json 기록 + open 모드에서 프로브 라우트 서빙
소비자: cli/lib/cypress-sessions/*
        → 레코드 읽기 → pid 생존 확인 → 프로브로 세션 확인 + 라이브 CDP 엔드포인트 획득
```

- `CypressSession` / `LiveSessionState` / `ReadySessionState` 인터페이스
- `isCompatibleRecord` 검증기, 스키마 버전 상수
- `SESSIONS_DIRNAME`, `<pid>.json` 파일명 헬퍼
- `SESSIONS_ROUTE_PREFIX` / `sessionProbePath` 라우트 헬퍼
- 역시 **순수·무의존성** — `fs`/`http` 는 생산자/소비자 쪽에 둔다

#### (3) `cy.prompt()` — 자연어 → 테스트

- 구현: `packages/driver/src/cy/commands/prompt/index.ts`
- `@module-federation/runtime` 의 `init` / `loadRemote` 로 **AI 번들을 클라우드에서 런타임 동적 로드**
- `Cypress.backend('wait:for:prompt:ready')` 로 번들 준비 대기
- 프로덕션에서는 번들을 Cloud 에서 받아옴 → **AI 로직을 앱에 박지 않고 서버에서 교체 가능**
- 개발 가이드: `guides/cy-prompt-development.md` (`CYPRESS_LOCAL_CY_PROMPT_PATH`, `CYPRESS_INTERNAL_ENV`)
- 타입은 `yarn gulp downloadPromptTypes` 로 받아옴

> **MCP 서버는 이 저장소에 없다.** `grep -ril "modelcontextprotocol|mcp server"` 결과 0건.
> 다만 `.claude/settings.json` 이 참조하는 `cypress-io/ai-toolkit` 마켓플레이스 쪽에 있을 수 있다.

---

## 3. Cypress가 하는 일 / 언제 쓰나

### 3.1 한 줄 정의

> **웹사이트를 사람 대신 클릭·입력·검증해주는 자동화 로봇.**

```ts
describe('로그인', () => {
  it('정상 로그인된다', () => {
    cy.visit('/login')
    cy.get('[data-cy=id]').type('karina')
    cy.get('[data-cy=pw]').type('1234')
    cy.get('[data-cy=submit]').click()
    cy.contains('환영합니다').should('be.visible')
  })
})
```

### 3.2 실제 저장소 안의 스펙 예시 (`packages/driver/cypress/e2e/commands/aliasing.cy.ts`)

```ts
import { assertLogLength } from '../../support/utils'
const { _ } = Cypress

describe('src/cy/commands/aliasing', () => {
  beforeEach(() => {
    cy.visit('/fixtures/dom.html')
  })

  context('#as', () => {
    it('does not change the subject', () => {
      const body = cy.$$('body')

      cy.get('body').as('b').then(($body) => {
        expect($body.get(0)).to.eq(body.get(0))
      })
    })
  })
})
```

### 3.3 언제 쓰나

| 상황 | 이유 |
|---|---|
| 회원가입 / 로그인 / 결제 같은 핵심 플로우 | 깨지면 매출 직결. 매번 수동 검증 불가능 |
| 배포 전 자동 검증 (CI/CD) | GitHub Actions 등에 연결해 PR 마다 자동 검사 |
| 리팩토링 | "안 깨졌나?" 를 테스트가 보증 |
| React / Vue 컴포넌트 단위 검증 | 컴포넌트 하나만 띄워 실제 브라우저에서 테스트 |
| 백엔드 없이 에러 화면 테스트 | `cy.intercept()` 로 응답 스텁 |
| 크로스 브라우저 확인 | Chrome / Firefox / Edge / WebKit / Electron |

---

## 4. 나에게 주는 가치

### 4.1 "그냥 쓰는 사람" 이라면 → 이 저장소는 필요 없다

```bash
npm install cypress --save-dev
```

사용만 할 거면 거대한 소스 대신 npm 패키지를 받으면 된다.

### 4.2 이 저장소를 받은 가치

1. **대규모 모노레포 교과서** — Lerna + Yarn Workspaces 로 37개 패키지 운영, Electron 앱 빌드, V8 스냅샷 최적화, semantic-release, CircleCI 멀티플랫폼 매트릭스
2. **AI 에이전트 문서 설계의 정석** — `AGENTS.md` + `guides/` + `.claude/skills/` + `.claude/rules/` 4단 계층. 그대로 자기 프로젝트에 이식 가능
3. **CDP 실전 코드** — `cypress-sessions` + `server/lib/browsers/cdp-protocol/`. AI 가 브라우저를 조작하는 도구 만들 때 바로 참고
4. **오픈소스 기여 포트폴리오** — MIT + CONTRIBUTING 완비
5. **수익화 소재** — 강의 / 컨설팅 / 플러그인 / SaaS (11장)

---

## 5. 설치 및 사용법

### 5.1 케이스 A — Cypress 를 "쓰는" 경우

```bash
# 설치
npm install cypress --save-dev          # 또는 yarn add cypress --dev

# GUI 실행
npx cypress open

# 헤드리스 실행 (CI)
npx cypress run
npx cypress run --browser chrome --spec "cypress/e2e/login.cy.ts"
```

필요 Node: `^22.0.0 || ^24.0.0 || >=26.0.0`

```
내프로젝트/
├── cypress.config.ts
└── cypress/
    ├── e2e/login.cy.ts
    ├── fixtures/
    └── support/
```

### 5.2 케이스 B — 이 저장소를 "개발" 하는 경우

```bash
# 0) Node 버전 맞추기 (안 맞으면 yarn 이 스크립트 실행을 거부)
nvm use            # .nvmrc → 24.15.0
node -v

# 1) 설치 — 반드시 저장소 루트에서. postinstall 이 빌드 + V8 스냅샷까지 수행 (4~5분)
yarn

# 2) 개발 모드 GUI (Electron, GraphQL: http://localhost:4444/__launchpad/graphql)
yarn dev
yarn start         # cypress open --dev --global

# 3) 테스트 — 패키지 단위로 좁혀서
yarn workspace @packages/config test
yarn workspace @packages/config test -- <spec 경로>
yarn workspace @packages/server test-unit -- <spec 경로>
yarn workspace @packages/server test-unit -- --grep "<패턴>"
yarn workspace @packages/data-context test-unit -- <spec 경로>   # 이 패키지만 jest

# 4) 타입체크 / 린트
yarn type-check
yarn check-ts
yarn lint
yarn lint --scope @packages/<name>

# 5) 빌드 / 바이너리
yarn build
yarn binary-build
yarn binary-package
yarn clean
```

#### 반드시 알아야 할 함정

| 함정 | 설명 |
|---|---|
| `yarn test --scope <pkg>` **금지** | 루트 `test` 스크립트가 이미 ~20개 `--scope` 를 하드코딩. lerna 는 scope 를 **합집합**으로 처리 → 전체 스위트 + 해당 패키지가 돌아감. (`yarn lint --scope`, `yarn check-ts --scope` 는 정상 동작) |
| "녹색인데 아무것도 증명 못 하는" 테스트 | `@packages/app` 의 `test` 는 그냥 `echo 'ok'`. 실제 검증은 `cypress:run:ct` / `cypress:run:e2e`. `@packages/driver` 의 `test` 는 vitest 유닛 몇 개뿐이고 본 커버리지는 `cypress/e2e` |
| 서브패키지에서 `yarn` 실행 금지 | 항상 루트에서 |
| `@packages/network` EACCES | 443 포트 필요. 비권한 컨테이너에서 실패하는 게 정상 |
| `@packages/config` 2개 테스트 실패 | `cypressBinaryRoot` 가 `'cypress'` 포함인지 단정. 워크스페이스 디렉터리명이 다르면 실패 (알려진 경로 의존 이슈) |
| 커밋 전 필수 | `yarn check-ts`, `yarn lint`, 관련 유닛 테스트 통과 (CLAUDE.md 규칙) |
| 포매터 | **Prettier 사용 안 함.** 전부 ESLint 로 강제 |
| 컨테이너 환경 | 브라우저·디스플레이 없음 → E2E / system-tests 는 로컬에서 실행해야 함 |

#### 코드 컨벤션 요약

싱글 쿼트, 세미콜론 없음, 2-스페이스, 멀티라인 trailing comma, `var` 금지, 템플릿 리터럴 선호, 객체 shorthand, `console` 금지, 신규 코드는 전부 TypeScript, 타입 전용 import 는 `import type`, 미사용 변수는 `_` 프리픽스, 테스트에 `.only` 금지, `.skip` 은 `NOTE:`/`TODO:`/`FIXME:` 주석 필수, `return` 앞 빈 줄.

---

## 6. 플러그인 / 스킬 / MCP 구분

**Cypress 본체는 플러그인도 스킬도 MCP도 아니다.** 독립 실행형 테스트 프레임워크 + Electron 데스크톱 앱이다.
다만 저장소 안에 그 세 가지가 **함께 들어있다**.

| 대상 | 정체 | 비고 |
|---|---|---|
| Cypress 본체 | ❌ 아님 | Node + Electron + CDP + 프록시 기반 완전한 제품 |
| `.claude/skills/` 2개 | ✅ **Claude Code 스킬** | `building-cypress-binary`, `debugging-cypress-artifacts` |
| `.claude/rules/` 5개 | ✅ **경로 기반 자동 규칙** | `paths:` glob 매칭 시 자동 로드 |
| `.claude/settings.json` | ✅ **Claude Code 플러그인 활성화** | `cypress@cypress`(from `cypress-io/ai-toolkit`), `typescript-lsp@claude-plugins-official` |
| `.cursor/skills/` | ✅ **Cursor 스킬** | `server-mocha-to-vitest` |
| `npm/` 내 패키지 | ✅ **Cypress 생태계 플러그인** | `@cypress/grep`, `@cypress/puppeteer` 등 |
| MCP 서버 | ❌ **저장소에 없음** | grep 결과 0건 |

```
Cypress (제품)  ←─ 여기에 붙는 것 ─→  @cypress/grep 등 "Cypress 플러그인"
     │
     └─ 이 저장소를 "개발"할 때 ←─ .claude/skills (스킬) + 활성 플러그인이 보조
                                    ↑ Cypress 기능이 아니라 개발자/AI 보조 도구
```

---

## 7. API 토큰 필요 여부

**기본 사용은 토큰이 전혀 필요 없다.**

| 기능 | 필요한 것 | 비용 |
|---|---|---|
| 로컬 `cypress open` / `cypress run` | 없음 | 무료 |
| 자체 CI(GitHub Actions 등)에서 실행 | 없음 | 무료 |
| **Cypress Cloud** (대시보드, 병렬 실행, 영상 저장) | `CYPRESS_RECORD_KEY` | 유료 (무료 티어 존재) |
| **`cy.prompt()`** (자연어 테스트) | Cypress Cloud 로그인 | 유료 플랜 |
| 저장소 changelog 검증 스크립트 | `GH_TOKEN` | 무료 |
| Claude Code 사용 | Anthropic API 키 / Claude 구독 | 별도 결제 |

실제 코드 근거 (`cli/lib/exec/run.ts:100`):

```ts
debug('--key is not set, looking up environment variable CYPRESS_RECORD_KEY')
options.key = util.getEnv('CYPRESS_RECORD_KEY')
```

```bash
export CYPRESS_RECORD_KEY="xxxxx"
npx cypress run --record
```

changelog 검증:

```bash
GH_TOKEN="$(gh auth token)" node ./scripts/semantic-commits/validate-binary-changelog.js
```

---

## 8. AI 에이전트 구축에 주는 도움

결론: **매우 크다.** 네 가지 자산이 있다.

### 8.1 `agent-info` — 에이전트 탐지 로직 (바로 이식 가능)

파일 1개, 의존성 0개, 순수 TypeScript. 설계 교훈은 2.7 (1) 참고.
에이전트 추가는 `lib/index.ts` 3곳 + 스펙 1곳만 수정하는 구조.

### 8.2 `cypress-sessions` + CDP — AI 가 실행 중 브라우저에 붙는 패턴

```
[cypress open 실행 중]  → sessions/<pid>.json 기록 (CDP 엔드포인트 포함)
                              ↓
[외부 프로세스 = AI 에이전트] → json 읽기 → pid 생존 확인 → 프로브 → CDP 연결
```

프로덕션 검증된 설계. "브라우저를 조작하는 AI 에이전트" 를 만들 때 그대로 차용 가능.
관련 코드: `packages/server/lib/browsers/cdp-protocol/cri-client.ts`, `browser-cri-client.ts`.

### 8.3 `cy.prompt()` — AI 로직 원격 교체 패턴

Module Federation 으로 AI 번들을 런타임에 내려받아 실행 → **앱 재배포 없이 AI 로직 갱신**.
AI 기능을 자주 바꿔야 하는 제품에서 필수적인 설계.

### 8.4 ⭐ AI 문서 4단 계층 (가치 최상)

2.6 의 계층 + CONTRIBUTING.md 의 "어디에 쓸지" 판단 기준.
핵심 원칙: **중요한 건 항상 / 자주 쓰는 건 찾기 쉽게 / 특수한 건 그때만.**
이걸 그대로 자기 프로젝트에 적용하면 AI 생산성이 즉시 올라간다.

---

## 9. React / PHP 로 만들 수 있나

### 9.1 "Cypress 자체를 재구현" → 현실적으로 불가능

| Cypress 핵심 | 필요 기술 | React | PHP |
|---|---|---|---|
| 브라우저 내부 명령 실행 | 브라우저 JS 런타임 | △ (React 역할 아님) | ❌ |
| 브라우저 자동 실행·제어 | CDP / WebDriver BiDi | ❌ | ❌ |
| 전체 트래픽 가로채기 | HTTP/HTTPS 프록시 + TLS 인터셉트 | ❌ | △ (매우 어려움) |
| 데스크톱 앱 | Electron | △ (UI 만) | ❌ |
| 브라우저 확장 | WebExtension API | ❌ | ❌ |
| 시작 속도 최적화 | V8 스냅샷 | ❌ | ❌ |

소스 3,000+ 파일, 10년 개발, 수십 명 팀 규모. 재구현은 비현실적.

### 9.2 대신 가능한 것 (훨씬 합리적인 길)

#### React

1. **React 앱을 Cypress 로 테스트** — `@cypress/react` + `@cypress/vite-dev-server`

   ```tsx
   import { mount } from 'cypress/react'
   import Button from './Button'

   it('버튼이 클릭된다', () => {
     mount(<Button label="확인" />)
     cy.contains('확인').click()
   })
   ```

2. **커스텀 테스트 리포트 대시보드 (React)** — Cypress 결과 JSON/JUnit 을 시각화. Cypress Cloud 유료 기능을 자체 구축 (수익화 가능)
3. **테스트 코드 생성기 UI** — 클릭으로 시나리오 구성 → `.cy.ts` 출력
4. **Cypress 플러그인** — 로직은 Node, UI 는 React (`@cypress/grep` 참고)

#### PHP

1. **PHP 앱(Laravel / WordPress)을 Cypress 로 테스트**

   ```ts
   cy.visit('/login')
   cy.get('input[name=email]').type('test@test.com')
   cy.get('input[name=password]').type('password')
   cy.get('button[type=submit]').click()
   cy.url().should('include', '/dashboard')
   ```

2. **PHP 백엔드 테스트 리포트 서버** — `cypress run --reporter json` 결과를 Laravel 로 수집 → MySQL 저장 → 통계·그래프·슬랙 알림 (자체 Cypress Cloud)
3. **WordPress 플러그인** — 관리자 화면에서 버튼 클릭 → PHP 가 Node/Cypress 호출 → 결제·문의·로그인 플로우 점검 결과 표시
4. **CI 연동 / DB 시딩 API** — 테스트 전 데이터 초기화 엔드포인트

> **핵심 인사이트**: "Cypress 를 만들기" 보다 **"Cypress 를 둘러싼 도구를 만들기"** 가 훨씬 쉽고 수익성이 높다.
> 본체는 MIT 로 무료이고, 주변 생태계(특히 한국어권)가 비어 있다.

---

## 10. 유튜브 강의 제작 가능성

### 10.1 법적 가능 여부 → 가능

```
LICENSE: MIT License (Copyright (c) 2023 Cypress.io)
```

MIT 는 상업적 이용·수정·재배포·2차 저작물을 모두 허용. 유료 강의 제작·판매 전부 합법.

주의사항:
- Cypress **로고 / 상표**는 저작권과 별개 → "Cypress 공식" 같은 사칭 표현 금지
- 소스 코드를 그대로 재배포할 경우 LICENSE 파일 포함 필요
- 강의 영상 자체는 제작자 저작물 → 수익화 자유

### 10.2 시장성

| 지표 | 평가 |
|---|---|
| 한국어 Cypress 콘텐츠 | 매우 부족 (영어권은 풍부, 한국어는 빈약) |
| 검색 수요 | "E2E 테스트", "테스트 자동화" 꾸준히 상승 |
| 시청자 구매력 | 높음 (현업 개발자 → 유료 강의 전환율 양호) |
| 경쟁 강도 | 낮음 (Playwright 쪽은 증가 추세, Cypress 한국어는 여전히 공백) |
| 차별 포인트 | **"AI + Cypress"** 조합은 한국어 콘텐츠가 사실상 없음 |

### 10.3 커리큘럼 (15편 시리즈)

**입문 (조회수 담당)**
1. 테스트 자동화가 뭔데? 10분만에 이해하기
2. Cypress 설치하고 첫 테스트 돌려보기
3. `cy.get()` / `cy.click()` — 핵심 명령어 7개
4. 셀렉터 전쟁 끝내기: `data-cy` 를 쓰세요
5. GitHub Actions 에 붙여 자동 배포 검증

**실전 (구독 담당)**
6. `cy.intercept()` 로 백엔드 없이 에러 화면 테스트
7. 세션 재사용으로 테스트 10배 빠르게 (`cy.session`)
8. React 컴포넌트 테스트 (`@cypress/react`)
9. **Flaky 테스트 원인과 박멸법** ← 수요 최상
10. **Cypress vs Playwright 솔직 비교** ← 알고리즘 친화

**고급 / 차별화 (수익화 담당)**
11. **Claude Code 로 Cypress 테스트 자동 생성하기** ← 한국어 최초급
12. **`AGENTS.md` 작성법 — AI 가 내 코드베이스를 이해하게 만들기**
13. **Cypress 소스코드 뜯어보기: 3,000 파일 모노레포 구조 분석**
14. **`cy.prompt()` — 자연어로 테스트 짜는 미래**
15. **CDP 로 AI 가 브라우저를 조작하게 만들기 (`cypress-sessions` 분석)**

### 10.4 제작 팁

- 화면 녹화 + 라이브 코딩 중심 (얼굴 노출 불필요)
- 1편당 8~15분
- 첫 15초에 "테스트가 자동으로 클릭하는 화면" 먼저 노출 → 이탈률 급감
- 예제 코드는 GitHub 공개 + 설명란 링크 → 구독 전환
- **이 컨테이너에는 브라우저·디스플레이가 없으므로 실습 녹화는 로컬에서 진행**

### 10.5 수익 구조

```
유튜브 광고(소액) → 인프런/유데미 유료 강의(본 수익) → 기업 컨설팅(최고 단가) → 전자책·템플릿(패시브)
```

---

## 11. 수익화 아이디어 상세

### 전체 지형도

```
                  수익 규모 ↑
                       │
  SaaS 제품            │   B2B 컨설팅
  (AI 테스트 생성)      │   (구축 대행)
                       │
 ─────────────────────────────────────→ 실행 난이도
                       │
  온라인 강의           │   유료 플러그인
  (인프런/유데미)       │   (WP / 대시보드)
                       │
```

---

### TIER 1 — 지금 바로 시작 가능 (난이도 ★★☆☆☆)

#### 1. 한국어 Cypress 온라인 강의

| 항목 | 내용 |
|---|---|
| 예상 수익 | 인프런 강의 1개당 월 50~300만원 (상위 강의는 월 1,000만+) |
| 초기 비용 | 사실상 0원 (OBS 무료 + 마이크 5만원) |
| 소요 기간 | 기획 1주 + 녹화 3주 ≒ 1개월 |
| 차별화 | **"AI(Claude Code) + Cypress"** — 한국어 콘텐츠 공백 |

실행 순서:
1. 유튜브 무료 5편으로 수요 검증 (조회수 판단)
2. 반응 좋으면 인프런 "Cypress + AI 로 테스트 자동화 완전정복" (3~5만원대)
3. 수강생 질문을 모아 심화편 제작 (업셀)

> 강의 제목에 "AI" 와 "실무" 를 포함시킬 것 — 현재 검색 트래픽이 집중된 키워드.

#### 2. 전자책 / 노션 템플릿

| 항목 | 내용 |
|---|---|
| 예상 수익 | 크몽·부크크 기준 월 20~100만원 (패시브) |
| 초기 비용 | 0원 |
| 소요 기간 | 2주 |

상품 아이디어:
- "Cypress 실전 레시피 100" — 로그인/결제/파일 업로드/드래그앤드롭 복붙 코드 모음
- **"Flaky 테스트 박멸 가이드"** — 현업 최대 고통 지점
- **"AGENTS.md 작성 템플릿 팩"** — 이 저장소의 4단 계층을 일반 프로젝트용으로 변환. 현재 수요 급증 중
- "Cypress 보일러플레이트" — TS + CI + ESLint 세팅 완료본

#### 3. 유튜브 채널 운영

| 항목 | 내용 |
|---|---|
| 예상 수익 | 광고 월 10~50만원 + **강의·컨설팅 유입이 본 수익** |
| 소요 기간 | 수익화 조건(구독 1천 / 시청 4천시간)까지 3~6개월 |

유튜브는 직접 수익보다 **신뢰 자산 + 유입 파이프**로 간주해야 한다.

---

### TIER 2 — 가장 수익성 높은 경로 (난이도 ★★★☆☆)

#### 4. 테스트 자동화 구축 외주 / 컨설팅 ← 단가 최상

| 항목 | 내용 |
|---|---|
| 예상 수익 | 프로젝트당 300~2,000만원 / 리테이너 월 200~500만원 |
| 초기 비용 | 0원 (실력 + 포트폴리오) |
| 타깃 | QA 팀 없는 스타트업, 이커머스, SI 업체 |

성립 근거: 국내 중소기업 다수가 "테스트 자동화를 하고 싶으나 할 사람이 없는" 상태.
QA 채용은 비싸고 Cypress 를 세팅할 시니어는 희소하다.

패키지 구성 예시:

```
베이직 (300만원 / 2주)
  - 핵심 플로우 10개 테스트 작성 (로그인/가입/결제/검색)
  - GitHub Actions CI 연동
  - 팀 교육 2시간

스탠다드 (800만원 / 1개월)
  - 베이직 + 테스트 30개
  - 커스텀 커맨드 / Page Object 아키텍처 설계
  - 테스트 리포트 대시보드 구축
  - Flaky 테스트 안정화

프리미엄 (1,500만원+ / 2개월)
  - 스탠다드 + 멀티브라우저 매트릭스
  - AI 기반 테스트 생성 파이프라인 구축
  - 3개월 유지보수 포함
```

영업 전술: 타깃 기업 사이트에 테스트 3개를 미리 작성해
**"귀사 결제 플로우에서 버그 2건을 발견했습니다"** 로 접근 → 수주율 급상승.

#### 5. 유료 Cypress 플러그인 / 도구 판매

| 항목 | 내용 |
|---|---|
| 예상 수익 | 월 50~500만원 (구독형) |
| 소요 기간 | 1~3개월 |

**(a) 자체 호스팅 테스트 대시보드** (React + Node/PHP) ← 추천

```
Cypress 결과 JSON → DB 저장 → 대시보드
├─ 테스트 성공률 추이 그래프
├─ Flaky 테스트 자동 탐지 (3회 중 1회 실패 감지)
├─ 실패 시 슬랙 / 카카오톡 알림
└─ 실행 영상 / 스크린샷 아카이브
```

포지셔닝: *"Cypress Cloud 는 비싸고 데이터가 해외로 나갑니다. 사내 서버에 설치하세요."*
→ 금융·공공·의료는 데이터 외부 유출이 금지되어 수요 확실. 연 라이선스 500~2,000만원 가능.

**(b) WordPress 플러그인: 원클릭 사이트 헬스체크** (PHP)

```
WP 관리자 → [사이트 점검] 클릭
  → PHP 가 Node/Cypress 호출
  → 결제 / 문의 / 로그인 플로우 자동 점검
  → 결과 리포트 표시
```

WordPress 는 전 세계 웹사이트의 약 40%. 쇼핑몰 운영자는 "결제창이 안 깨졌는지" 에 지불 의사가 있다.
Freemium (무료 3개 체크 / Pro 월 $9).

**(c) 테스트 코드 생성기 (브라우저 확장)**
클릭으로 시나리오 녹화 → `.cy.ts` 자동 생성. 무료 + Pro 모델.

---

### TIER 3 — 큰 그림 (난이도 ★★★★★)

#### 6. AI 테스트 생성 SaaS

| 항목 | 내용 |
|---|---|
| 예상 수익 | 월 500만~1억+ (성공 시) |
| 초기 비용 | 개발 + LLM API (월 50~300만원) |
| 소요 기간 | MVP 3~6개월 |

```
사용자가 URL 입력
  → 사이트 크롤링 + DOM 분석
  → LLM 으로 "핵심 테스트 시나리오 20개" 생성
  → Cypress 코드로 변환
  → 실행 후 리포트 제공
  → GitHub PR 자동 생성
```

이 저장소에서 차용할 기술:
- `agent-info` → 호출자 식별
- `cypress-sessions` + CDP → 실행 중 브라우저 제어
- `cy.prompt` 의 Module Federation 패턴 → AI 로직 원격 교체

가격: Free (3 URL) / Pro $29월 / Team $99월 / Enterprise 협의

#### 7. 기업 교육 / 사내 워크샵

| 항목 | 내용 |
|---|---|
| 예상 수익 | 1일 워크샵 150~500만원 |
| 경로 | 패스트캠퍼스 / 인프런 기업교육 / 사내 직무교육 예산 |

기업 교육은 B2C 대비 단가가 약 10배. 자료 1회 제작 후 반복 재활용 가능.
특히 "AI 활용 개발 생산성" 주제에 기업 교육 예산이 집중되고 있다.

---

### 최종 추천

> **"한국어 Cypress 강의 → 그 신뢰로 컨설팅 수주"** 조합.

- 초기 비용 0원, 실패 리스크 없음
- 강의 제작 과정에서 본인 실력도 상승
- 강의가 자동 영업 채널로 작동 → 컨설팅 문의 유입
- "AI + 테스트 자동화" 는 현재 국내 경쟁자가 거의 없음

---

## 12. 6개월 실행 로드맵

| 기간 | 할 일 | 목표 수익 |
|---|---|---|
| **Month 1** | 유튜브 5편 업로드 (무료, 수요 검증) + 전자책 "Flaky 테스트 박멸 가이드" 출간 | 월 30만원 |
| **Month 2–3** | 인프런 강의 출간 ("Cypress + AI 완전정복") + 이 저장소에 PR 기여 (포트폴리오) | 월 150만원 |
| **Month 3–4** | 컨설팅 영업 시작 (강의 수강생 중 기업 담당자 공략) + 리드 확보용 "무료 사이트 진단" 제공 | 월 400만원 |
| **Month 5–6** | 대시보드 제품 MVP 출시 (React + Node/PHP) + 기업 교육 제안서 발송 | 월 800만원+ |

---

## 13. 참고 링크

### 저장소 / 공식

| 항목 | 주소 |
|---|---|
| **이 저장소 (Fork)** | https://github.com/bmshin94/cypress |
| **원본 (Upstream)** | https://github.com/cypress-io/cypress |
| 공식 사이트 | https://www.cypress.io |
| 공식 문서 | https://docs.cypress.io |
| 변경 이력 | https://on.cypress.io/changelog |
| 로드맵 | https://on.cypress.io/roadmap |
| Discord | https://on.cypress.io/discord |
| npm 패키지 | https://www.npmjs.com/package/cypress |
| Cypress AI Toolkit (플러그인 마켓) | https://github.com/cypress-io/ai-toolkit |
| Claude 공식 플러그인 마켓 | https://github.com/anthropics/claude-plugins-official |

### 저장소 내부 주요 문서

| 문서 | 내용 |
|---|---|
| `README.md` | 제품 소개 |
| `AGENTS.md` | AI 에이전트용 모노레포 지도 (각 패키지에도 존재) |
| `CLAUDE.md` | Claude Code 전용 규칙 |
| `CONTRIBUTING.md` | PR 컨벤션 / 가이드 배치 기준 (정본) |
| `guides/README.md` | 24개 작업 런북 색인 |
| `guides/release-process.md` | 릴리즈 절차 |
| `guides/writing-the-cypress-changelog.md` | 변경 이력 작성 규칙 |
| `guides/cy-prompt-development.md` | `cy.prompt` 개발 환경 |
| `guides/v8-snapshots.md` | V8 스냅샷 |
| `guides/eslint-migration.md` | ESLint 마이그레이션 |
| `.claude/skills/building-cypress-binary/SKILL.md` | 바이너리 빌드 실행 노하우 |
| `.claude/skills/debugging-cypress-artifacts/SKILL.md` | 패키징 산출물 디버깅 |
| `.claude/rules/test-runner.md` | 패키지별 올바른 테스트 명령 |
| `.circleci/AGENTS.md` | CI 설정 가이드 |

---

*이 문서는 저장소 전수조사(폴더 구조, `package.json`, `AGENTS.md` 계층, `agent-info`/`cypress-sessions`/`cy.prompt` 소스, `.claude` 설정, git 이력)를 근거로 작성되었습니다.*
