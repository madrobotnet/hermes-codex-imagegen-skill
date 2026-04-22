# hermes-codex-imagegen-skill

![Hermes Skill](https://img.shields.io/badge/Hermes-Skill-6f42c1)
![Platform CLI](https://img.shields.io/badge/Platform-CLI-0a66c2)
![Codex CLI](https://img.shields.io/badge/Codex-CLI-10a37f)
![License MIT](https://img.shields.io/badge/License-MIT-green.svg)

<p align="center">
  <a href="#readme-english"><strong>ENGLISH</strong></a> |
  <a href="#readme-korean"><strong>한국어</strong></a>
</p>

---

<a id="readme-english"></a>

## English

### Overview

A **Hermes Agent skill** for **CLI-based** image generation through a separate **Codex CLI** process authenticated with **ChatGPT / OpenAI Codex OAuth**.

This skill exists for the case where Hermes' native image backend is unavailable, but `codex` is installed and already logged in.

### TL;DR

- a CLI-only fallback for Hermes users who already have a working Codex login
- runs `codex exec` in a separate process instead of touching Hermes' native image backend
- verifies the real PNG output from `~/.codex/generated_images/`
- useful when you want a practical Codex-based imagegen workaround, not an API-native integration

### Why this is CLI-based

This workflow depends on:

- the local `codex` executable
- `codex login status`
- `codex exec --skip-git-repo-check --ephemeral ...`
- verifying generated PNG files under `~/.codex/generated_images/`

So this is not a generic OpenAI Images API wrapper. It is a **Codex CLI workaround workflow** packaged as a Hermes skill.

### What it does

- launches a separate Codex CLI instance
- asks Codex to generate an image using its built-in `imagegen` skill
- captures the Codex session id
- re-verifies the actual output file from the Hermes side
- warns that `bwrap` or sandbox inspection errors do not always mean the image generation failed

### What it does not do

- it does **not** rewire Hermes' native `image_generate` tool
- it does **not** directly call the OpenAI Images API
- it does **not** remove the need for a working Codex login

### Requirements

- Hermes Agent
- Codex CLI installed and on `PATH`
- active ChatGPT / OpenAI Codex OAuth login
- a Linux or macOS shell environment where `codex exec` can run

### Repository contents

- `SKILL.md` — the Hermes skill itself
- `README.md` — public overview
- `LICENSE` — MIT license
- `.gitignore` — minimal repo hygiene

### Published names

- **Repository name:** `hermes-codex-imagegen-skill`
- **Skill slug:** `codex-imagegen-via-chatgpt-oauth`

The repository name stays short and readable. The skill slug stays more explicit because the auth path matters for execution.

### Quick publish flow

```bash
git init
git add .
git commit -m "Prepare the Hermes Codex image generation skill for publication"
gh repo create hermes-codex-imagegen-skill --public --source . --push
```

### Notes

Generated image files normally land under:

```text
~/.codex/generated_images/<session_id>/...
```

If you want to use a generated file in a real project, copy it into the project after verification instead of leaving it only under `.codex`.

<p align="right"><a href="#hermes-codex-imagegen-skill">↑ Back to top</a></p>

---

<a id="readme-korean"></a>

## 한국어

### 개요

이 저장소는 **Hermes Agent용 스킬**이며, **CLI 기반**으로 동작합니다.  
핵심 아이디어는 **ChatGPT / OpenAI Codex OAuth로 로그인된 별도 Codex CLI 프로세스**를 이용해 이미지를 생성하는 것입니다.

Hermes의 기본 이미지 백엔드를 바로 사용하기 어려운 상황에서, 로컬에 `codex`가 설치되어 있고 이미 로그인까지 완료되어 있을 때 사용할 수 있는 우회 워크플로우입니다.

### TL;DR

- Codex 로그인이 이미 되어 있으면 바로 활용할 수 있는 CLI 전용 fallback입니다.
- Hermes 기본 이미지 백엔드는 건드리지 않고, 별도 `codex exec` 프로세스로 우회합니다.
- 실제 결과물은 `~/.codex/generated_images/`에서 다시 검증합니다.
- OpenAI Images API 통합이라기보다, 실용적인 Codex 기반 imagegen workaround에 가깝습니다.

### 왜 CLI 기반인가요?

이 워크플로우는 다음 전제에 의존합니다.

- 로컬 `codex` 실행 파일
- `codex login status`
- `codex exec --skip-git-repo-check --ephemeral ...`
- `~/.codex/generated_images/` 아래에 저장된 PNG 결과 검증

즉, 이것은 일반적인 OpenAI Images API 래퍼가 아닙니다.  
**Codex CLI를 활용하는 이미지 생성 우회 워크플로우**를 Hermes 스킬 형태로 정리한 것입니다.

### 이 스킬이 하는 일

- 별도의 Codex CLI 인스턴스를 실행합니다.
- Codex의 내장 `imagegen` 스킬로 이미지 생성을 시도합니다.
- Codex session id를 캡처합니다.
- Hermes 쪽에서 실제 생성 파일을 다시 검증합니다.
- `bwrap` 또는 샌드박스 파일 조회 에러가 곧 생성 실패를 뜻하지는 않는다는 점을 문서화합니다.

### 이 스킬이 하지 않는 일

- Hermes의 기본 `image_generate` 도구 백엔드를 바꾸지 않습니다.
- OpenAI Images API를 직접 호출하지 않습니다.
- Codex 로그인 필요성을 없애주지 않습니다.

### 요구 사항

- Hermes Agent
- `PATH`에 잡힌 Codex CLI
- 활성화된 ChatGPT / OpenAI Codex OAuth 로그인
- `codex exec`를 실행할 수 있는 Linux 또는 macOS 셸 환경

### 저장소 구성

- `SKILL.md` — 실제 Hermes 스킬 파일
- `README.md` — 공개용 안내 문서
- `LICENSE` — MIT 라이선스
- `.gitignore` — 최소한의 저장소 정리용 파일

### 공개 이름

- **저장소 이름:** `hermes-codex-imagegen-skill`
- **스킬 slug:** `codex-imagegen-via-chatgpt-oauth`

저장소 이름은 짧고 읽기 쉽게 유지하고, 스킬 slug는 실행 방식이 중요하므로 조금 더 구체적으로 유지했습니다.

### 빠른 공개 예시

```bash
git init
git add .
git commit -m "Prepare the Hermes Codex image generation skill for publication"
gh repo create hermes-codex-imagegen-skill --public --source . --push
```

### 참고

생성된 이미지 파일은 보통 아래 경로에 저장됩니다.

```text
~/.codex/generated_images/<session_id>/...
```

실제 프로젝트에서 사용해야 하는 이미지는 검증 후 프로젝트 폴더로 복사해서 사용하는 편이 좋습니다. `.codex` 내부에만 남겨둔 채 참조하면 관리가 지저분해질 수 있습니다.

<p align="right"><a href="#hermes-codex-imagegen-skill">↑ 맨 위로</a></p>
