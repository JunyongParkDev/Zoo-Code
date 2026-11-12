# upstream에서 작게 구현할 만한 이슈 — 2026-11-12

## 조사 기준

- 대상: [Zoo-Code-Org/Zoo-Code의 열린 이슈](https://github.com/Zoo-Code-Org/Zoo-Code/issues).
- 확인일: 2026-11-12, 한국 시간. 최신 이슈와 과거 이슈 등 200여 개의 목록을 훑고, 소규모 후보의 본문·댓글·담당자·관련 PR을 확인했다.
- 코드 기준: 로컬 `main`과 GitHub의 `upstream/main`이 모두 [`efc22ed9f6f75975106f170cf29cc9bcc0b86682`](https://github.com/Zoo-Code-Org/Zoo-Code/commit/efc22ed9f6f75975106f170cf29cc9bcc0b86682)임을 확인했다. 아래 코드 링크는 이 커밋에 고정했다.
- 판단 기준: 변경 범위가 작고, 현재 코드에 미해결 부분이 남아 있으며, 좁은 테스트로 검증할 수 있는가. 열린 PR이나 담당자가 있는 항목은 우선순위를 낮췄다.
- 이 문서는 정적 코드 조사 결과다. 제품 실행 재현이나 테스트 실행은 하지 않았다. 시간은 구현과 집중 검증의 추정치이며 리뷰 대기 시간은 제외한다.
- “직접 PR 미발견”은 이슈 번호·관련 파일명·제목 키워드로 검색한 범위의 결과다. GitHub의 개발 연결 전체나 모든 작업 브랜치의 부재를 보장하는 표현은 아니다.

## 추천 후보 6개

**첫 작업으로는 #643 또는 #296을 추천한다.** #643은 사용자에게 보이는 문구 개선이고, #296은 외부 API 없이 테스트 한 파일에서 끝낼 수 있다. 제품 버그를 고치고 싶다면 #769가 다음 후보다.

| 순위 | 이슈                                                                                                   | 예상 시간 / 난이도  | 최소 범위                  | 추천 이유와 착수 조건                                                                    |
| ---- | ------------------------------------------------------------------------------------------------------ | ------------------- | -------------------------- | ---------------------------------------------------------------------------------------- |
| 1    | [#643 모델 선택 안내의 오래된 추천 문구 제거](https://github.com/Zoo-Code-Org/Zoo-Code/issues/643)     | 1–2시간 / 쉬움      | React 1개 + 번역 JSON 18개 | 기능 변경이 작고 요구가 구체적. 미할당, 직접 대응 열린 PR 미발견                         |
| 2    | [#296 ripgrep 경로·우선순위 테스트 보강](https://github.com/Zoo-Code-Org/Zoo-Code/issues/296)          | 1–2시간 / 쉬움      | 테스트 1개                 | 운영 코드를 바꾸지 않고 빠른 단위 테스트로 기여. 미할당, 댓글 없음, 직접 PR 미발견       |
| 3    | [#769 채팅 입력창·사용자 메시지에 글자 크기 적용](https://github.com/Zoo-Code-Org/Zoo-Code/issues/769) | 3–6시간 / 쉬움~보통 | UI 2–3개 + 테스트          | 기존 설정을 재사용하는 실용적 버그 수정. 미할당, 직접 대응 열린 PR 미발견                |
| 4    | [#1224 중복 이슈 템플릿 제거](https://github.com/Zoo-Code-Org/Zoo-Code/issues/1224)                    | 30–60분 / 매우 쉬움 | 구형 템플릿 2개            | 코드 동작에 영향 없는 작은 정리. 미할당이나 작성자의 과거 PR 의사 댓글 확인 필요         |
| 5    | [#297 tree-sitter 테스트 스타일 정리](https://github.com/Zoo-Code-Org/Zoo-Code/issues/297)             | 30–60분 / 매우 쉬움 | 테스트 2개                 | 가장 작은 변경으로 시작 가능. 사용자 체감 효과는 작음. 미할당, 댓글 없음, 직접 PR 미발견 |
| 6    | [#2410 Windows mutation runner 실행 실패](https://github.com/Zoo-Code-Org/Zoo-Code/issues/2410)        | 4–8시간 / 쉬움~보통 | 실행 스크립트 + 테스트     | 원인과 실행 실패 근거가 구체적. 미할당, 직접 수정 PR 미발견. Windows 검증 환경 필요      |

### 1. #643 — 모델 선택 안내 문구 정리

**현재 코드 근거:** [ModelPicker.tsx:319](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/webview-ui/src/components/settings/ModelPicker.tsx#L319)의 `Trans`가 `defaultModelLink`와 `defaultModelId`를 보간한다. [영문 settings.json:1033](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/webview-ui/src/i18n/locales/en/settings.json#L1033) 및 나머지 17개 로케일의 `modelPicker.automaticFetch`에는 특정 모델 추천과 무료 모델 검색 안내가 남아 있다.

**최소 구현:** 자동으로 모델을 가져온다는 설명과 `serviceLink`는 유지하고, 추천·무료 모델 검색 문장 및 해당 보간 값만 제거한다. `defaultModelId`는 같은 컴포넌트의 초기 선택 fallback에서도 쓰이므로 prop 자체를 삭제하지 않는다.

**검증:** 기존 `ModelPicker.spec.tsx`에서 자동 조회 설명·서비스 링크 유지, 추천 링크 부재를 확인한다. 로케일별 키와 보간 값도 검사한다. 특정 모델의 현행 가격이나 판매 여부를 새로 판단할 필요는 없다.

```sh
pnpm --dir webview-ui exec vitest run src/components/settings/__tests__/ModelPicker.spec.tsx
pnpm --dir webview-ui run check-types
node scripts/find-missing-translations.js --area=webview --file=settings.json
```

### 2. #296 — ripgrep legacy 경로와 선택 순서 테스트

**현재 코드 근거:** [ripgrep/index.ts:92](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/src/services/ripgrep/index.ts#L92)의 `ripgrepCandidatePaths()`는 현재 8개 후보를 만든다. 이슈 작성 당시의 6개보다 늘었다. [기존 테스트:62](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/src/services/ripgrep/__tests__/index.spec.ts#L62)는 classic·universal·플랫폼 패키지 등을 다루지만, legacy 3개와 여러 후보가 함께 존재할 때의 우선순위 검증은 빠져 있다.

**최소 구현:** 기존 파일 존재 mock을 재사용하고 `it.each`로 다음 레이아웃을 검증한다.

- `node_modules/vscode-ripgrep/bin`
- `node_modules.asar.unpacked/vscode-ripgrep/bin`
- `node_modules.asar.unpacked/@vscode/ripgrep/bin`

여러 후보가 동시에 존재하면 classic 경로가 선택되는 경우와, 앞선 후보가 없으면 다음 후보로 넘어가는 경우도 추가한다. 기대 순서를 운영 함수의 반환 배열에서 그대로 만들면 순서 회귀를 잡지 못하므로 독립적으로 지정한다. 플랫폼별 `rg.exe`/`rg`와 기존 아키텍처 테스트를 유지한다.

**검증:** 실제 ripgrep 설치나 다운로드 없이 단위 테스트로 충분하다.

```sh
pnpm --dir src exec vitest run services/ripgrep/__tests__/index.spec.ts
pnpm --dir src run check-types
pnpm --dir src exec eslint --prune-suppressions --max-warnings=0 services/ripgrep/__tests__/index.spec.ts
```

### 3. #769 — 기존 Chat Font Size를 입력창에도 적용

**현재 코드 근거:** [ExtensionStateContext.tsx:642](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/webview-ui/src/context/ExtensionStateContext.tsx#L642)는 이미 `--zoo-chat-font-size`를 설정한다. 하지만 [ChatTextArea.tsx:1068](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/webview-ui/src/components/chat/ChatTextArea.tsx#L1068)의 멘션 강조 레이어와 같은 파일 1130행의 textarea는 편집기 글자 크기 클래스를 쓴다. [ChatRow.tsx:1299](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/webview-ui/src/components/chat/ChatRow.tsx#L1299)의 사용자 메시지 본문에도 채팅 글자 크기가 적용되지 않는다.

**최소 구현:** 새 설정을 추가하지 않고 기존 CSS 변수를 입력창·강조 레이어·사용자 메시지 본문에 일관되게 적용한다. 필요하면 공용 CSS utility를 만든다. 미설정 시 기본값을 보존한다.

**주의:** textarea만 바꾸면 멘션 강조 위치가 어긋날 수 있다. 글자 크기뿐 아니라 줄 높이, 자동 높이 재계산, slash-command 표시, 메시지 수정 화면의 정렬도 확인해야 한다. 관련 appearance PR [#2224](https://github.com/Zoo-Code-Org/Zoo-Code/pull/2224)는 2026-11-11에 병합 없이 닫혔으므로 해결 근거가 아니다.

**검증:** 기존 `ChatTextArea.spec.tsx`와 `ChatTextArea.visual.tsx`를 활용한다. 기본·작은·큰 글자, 긴 멘션, 줄바꿈, 수정 모드를 확인한다. 실제 겹침과 배치는 component visual 테스트에서 검증한다.

```sh
pnpm --dir webview-ui exec vitest run src/components/chat/__tests__/ChatTextArea.spec.tsx
pnpm --dir webview-ui run test:visual -- ChatTextArea.visual.tsx
pnpm --dir webview-ui run check-types
```

### 4. #1224 — Markdown/YAML 이슈 템플릿 중복 제거

**현재 코드 근거:** [ISSUE_TEMPLATE 디렉터리](https://github.com/Zoo-Code-Org/Zoo-Code/tree/efc22ed9f6f75975106f170cf29cc9bcc0b86682/.github/ISSUE_TEMPLATE)에 `bug_report.md`와 `bug_report.yml`, `feature_request.md`와 `feature_request.yml`이 함께 있다. [CONTRIBUTING.md:77](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/CONTRIBUTING.md#L77)은 YAML 양식의 Bug Report / Enhancement Request 사용을 안내한다.

**최소 구현:** 대체된 `bug_report.md`, `feature_request.md` 2개를 제거한다. 삭제한 양식을 직접 참조하는 링크가 있다면 함께 수정한다. 모델 추가·마켓플레이스 양식과 `blank_issues_enabled: false`는 유지한다.

**검증:** 남은 양식의 이름·필수 항목·라벨·링크와 `git diff --check`를 확인한다. 런타임 테스트 추가는 불필요하다. GitHub의 실제 이슈 선택 화면은 기본 브랜치 반영 후 확인한다.

**착수 조건:** 작성자가 PR 작성 의사를 밝힌 댓글이 있다. 대응 열린 PR은 템플릿 제목·파일명 검색에서 발견하지 못했으나, 작업 상태를 확인한 뒤 진행하는 편이 좋다.

### 5. #297 — tree-sitter 테스트의 작은 스타일 수정

**현재 코드 근거:** [TOML 테스트:46](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/src/services/tree-sitter/__tests__/parseSourceCodeDefinitions.toml.spec.ts#L46)은 유연한 공백 패턴과 고정 공백 `{2}`를 함께 쓴다. [Solidity 테스트:40](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/src/services/tree-sitter/__tests__/parseSourceCodeDefinitions.solidity.spec.ts#L40)에는 인접 `it()` 블록 사이 빈 줄이 없다.

**최소 구현:** TOML의 고정 두 칸 공백을 검증하는 의도를 짧은 주석으로 설명하고, Solidity 테스트 사이에 빈 줄을 추가한다. `{2}`를 `\s+`로 바꾸면 검증 조건이 느슨해지므로 주석 방식이 보수적이다.

**검증:** 기존 두 테스트와 파일별 lint를 실행한다. 새 테스트는 필요 없다. 변경은 매우 쉽지만 제품 개선 효과가 작다는 점을 감안한다.

```sh
pnpm --dir src exec vitest run services/tree-sitter/__tests__/parseSourceCodeDefinitions.toml.spec.ts services/tree-sitter/__tests__/parseSourceCodeDefinitions.solidity.spec.ts
```

### 6. #2410 — Windows에서 Stryker/Vitest 실행 경로 수정

**현재 코드 근거:** [stryker-diff.mjs:421](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/scripts/stryker-diff.mjs#L421)은 확장자 없는 `node_modules/.bin/stryker`를 `spawnSync`로 직접 실행한다. [같은 파일:354](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/scripts/stryker-diff.mjs#L354)의 Vitest resolver와 373행의 discovery 실행도 같은 형태다.

**이슈의 실행 근거:** [추가 댓글](https://github.com/Zoo-Code-Org/Zoo-Code/issues/2410#issuecomment-6776454879)은 Windows와 저장소 지정 Node 22.23.4에서 shim 직접 실행의 `ENOENT`, 실제 Stryker JS 진입점을 Node로 실행했을 때의 성공을 보고한다. 이 결과는 이슈 작성자의 측정이며 이번 조사에서 재실행한 결과는 아니다.

**최소 구현:** `process.execPath`와 실제 Stryker/Vitest JS 진입점을 사용하도록 실행 경로를 정리한다. 단순히 Stryker 한 줄만 고치면 Vitest discovery가 여전히 실패할 수 있다. 기존 timeout, env, 인자 배열, 버퍼 크기는 보존한다.

**검증:** [stryker-diff.test.mjs:412](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/scripts/stryker-diff.test.mjs#L412)의 resolver 테스트와 discovery·preflight 오류 테스트를 확장한다. 경로에 공백이 있는 경우도 검사한다. Windows에서 실제 변경 패키지가 최소 하나 선택되는 preflight를 확인해야 하며, 패키지 0개로 끝나는 성공은 충분하지 않다.

```sh
pnpm test:mutation-ci
```

**범위 제한:** CI의 advisory 정책까지 변경하는 것은 별도 결정이다. 이 후보는 실행 이식성 수정으로 한정한다. 현재 macOS 환경만으로 Windows 성공을 확증할 수 없어 즉시 착수 우선순위는 낮췄다.

## 조건부 후보

| 이슈                                                                                                  | 예상 시간                | 확인된 부분과 먼저 해결할 조건                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ----------------------------------------------------------------------------------------------------- | ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [#2207 Context Management의 빈 Select 값](https://github.com/Zoo-Code-Org/Zoo-Code/issues/2207)       | 1–3시간 + 재현           | [ContextManagementSettings.tsx:540](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/webview-ui/src/components/settings/ContextManagementSettings.tsx#L540)는 `config.id`를 검증 없이 `SelectItem`에 전달하고, `ProviderSettingsManager.listConfig()`는 ID 누락 시 빈 문자열을 반환한다. 다만 정상 초기화는 누락 ID를 생성하고 이슈 원문은 Roo Code 3.54.0이다. 현재 Zoo에서 유입 경로를 재현한 뒤 UI 방어를 추가할 것. 기존 mock은 실제 Radix 예외를 숨기므로 정상/빈 ID/빈 목록을 실제 Select로 검증해야 함 |
| [#2318 LiteLLM Reasoning Effort 선택](https://github.com/Zoo-Code-Org/Zoo-Code/issues/2318)           | 0.5–1일                  | 공통 `ThinkingBudget` UI와 provider의 effort 전달은 이미 있다. [fetchers/litellm.ts:71](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/src/api/providers/fetchers/litellm.ts#L71)의 모델 capability mapping이 실제 원인인지 먼저 확인할 것. 단순 dropdown 추가로 볼 수 없으며 관련 열린 refactor [#2299](https://github.com/Zoo-Code-Org/Zoo-Code/pull/2299)와 겹칠 수 있음                                                                                                                                 |
| [#1730 slash command 목록·실행 우선순위 불일치](https://github.com/Zoo-Code-Org/Zoo-Code/issues/1730) | 0.5–1일                  | [commands.ts:332](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/src/services/command/commands.ts#L332)의 global-over-built-in 차단은 남아 있다. 직접 수정 PR [#1736](https://github.com/Zoo-Code-Org/Zoo-Code/pull/1736)은 closed/unmerged이고 대응 열린 PR은 미발견. 과거 리뷰에서 디렉터리 symlink의 목록/실행 불일치도 지적되어 조건문 한 줄 제거만으로 끝내면 안 됨. 이전 리뷰와 종료 이력을 확인하고 두 public 경로의 일관성 테스트 필요                                                              |
| [#1302 OpenRouter telemetry의 provider 표기](https://github.com/Zoo-Code-Org/Zoo-Code/issues/1302)    | 코드 2–4시간 + 정책 결정 | UI 표시명 `OpenRouter`와 telemetry 식별자 `openrouter`가 현재 구분되어 있다. 직접 PR [#1439](https://github.com/Zoo-Code-Org/Zoo-Code/pull/1439)는 closed/unmerged. 표시명/식별자의 차이 자체를 버그라고 가정해 일괄 변경하면 안 되며, 직렬화 값과 기존 대시보드 호환 요구를 먼저 정해야 함                                                                                                                                                                                                                                                         |

## 쉬워 보여도 신규 구현 대상으로 제외한 항목

| 이슈                                                                                          | 제외 근거                                                                                                                                                                                                                                                                                                                                            |
| --------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [#2030 Requesty API 키 전달 누락](https://github.com/Zoo-Code-Org/Zoo-Code/issues/2030)       | 이슈는 열려 있지만 병합된 [#2191](https://github.com/Zoo-Code-Org/Zoo-Code/pull/2191)에 수정이 있다. 현행 [requesty.ts:107](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/src/api/providers/requesty.ts#L107)은 API 키를 전달하며 key 전달·캐시 격리 테스트도 존재. 이전 10월 보고서의 추천을 이번에는 제외 |
| [#2171 Azure 404의 deployment 안내](https://github.com/Zoo-Code-Org/Zoo-Code/issues/2171)     | 직접 수정 PR [#2172](https://github.com/Zoo-Code-Org/Zoo-Code/pull/2172)가 열려 있음                                                                                                                                                                                                                                                                 |
| [#2425 하드코딩 영어 UI 라벨](https://github.com/Zoo-Code-Org/Zoo-Code/issues/2425)           | 작은 수정은 가능하지만 rrewll에게 할당되어 있고 Claiming 댓글이 있음                                                                                                                                                                                                                                                                                 |
| [#2418 복수형 번역 오류](https://github.com/Zoo-Code-Org/Zoo-Code/issues/2418)                | 열린 PR [#2424](https://github.com/Zoo-Code-Org/Zoo-Code/pull/2424) 및 작업 의사 댓글이 있음                                                                                                                                                                                                                                                         |
| [#2185 언어 변경 후 구 언어 유지](https://github.com/Zoo-Code-Org/Zoo-Code/issues/2185)       | rrewll 할당 및 열린 PR [#2194](https://github.com/Zoo-Code-Org/Zoo-Code/pull/2194)가 있음                                                                                                                                                                                                                                                            |
| [#2230 max output tokens 기본값 불일치](https://github.com/Zoo-Code-Org/Zoo-Code/issues/2230) | 직접 수정 PR [#2231](https://github.com/Zoo-Code-Org/Zoo-Code/pull/2231)가 열려 있음                                                                                                                                                                                                                                                                 |
| [#2429 Zoo Gateway completePrompt 취소](https://github.com/Zoo-Code-Org/Zoo-Code/issues/2429) | 열린 직접 구현 PR [#2430](https://github.com/Zoo-Code-Org/Zoo-Code/pull/2430)이 있음                                                                                                                                                                                                                                                                 |
| [#2338 MCP 도구명의 점 제거](https://github.com/Zoo-Code-Org/Zoo-Code/issues/2338)            | 관련 열린 PR [#2118](https://github.com/Zoo-Code-Org/Zoo-Code/pull/2118)에 sanitized 이름을 원래 도구명으로 복구하는 경로가 있어 중복 검토가 선행되어야 함. 단순 정규식 변경은 provider의 함수 이름 제약과 충돌할 수 있음                                                                                                                            |

파일 쓰기 안전성·프로필 동시성·작업 생명주기 이슈는 작은 코드 조각으로 보여도 여러 상태와 재시작 계약을 검증해야 하므로 이번 쉬운 후보에서 제외했다. `good first issue` 라벨이 있는 모델 메타데이터 추가도 가격·한도·지원 기능의 공식 근거가 필요해 라벨만으로 추천하지 않았다.

## 착수 및 검증 메모

- 문구 개선: **#643**. 테스트로 작게 기여: **#296**. 사용자 체감 버그: **#769**. 최소 변경 연습: **#297**. 설정 파일 정리: 작업 상태 확인 후 **#1224**. Windows 환경이 있으면 **#2410**.
- 실제 구현 시 관련 패키지의 타입 검사와 변경 파일 lint를 실행한다. `src` 파일은 `pnpm --dir src exec eslint --prune-suppressions --max-warnings=0 <relative-file>` 후 suppression 수가 늘지 않았는지 확인한다. 새 테스트는 typed double과 `it.each`를 사용한다.
- UI 작업에는 `webview-ui/AGENTS.md`를 적용한다. 새 설정이 필요해지면 `cachedState` 편집 → Save payload → ContextProxy 저장 → `getState()` → `getStateToPostToWebview()`까지 왕복을 검증한다. #769의 최소 범위는 기존 설정 재사용이다.
- Changeset과 CHANGELOG는 개별 작업에서 만들거나 수정하지 않는다.
- [기여 가이드의 Issue-First 절차](https://github.com/Zoo-Code-Org/Zoo-Code/blob/efc22ed9f6f75975106f170cf29cc9bcc0b86682/CONTRIBUTING.md#L70)는 Claiming 댓글과 코어 팀의 할당 확인을 안내한다. 이 조사에서는 GitHub 댓글·담당자 변경·PR 생성은 수행하지 않았다.
