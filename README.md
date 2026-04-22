# hermes-codex-imagegen-skill

A **Hermes Agent skill** for **CLI-based** image generation through a separate **Codex CLI** process authenticated with **ChatGPT / OpenAI Codex OAuth**.

This skill exists for the case where Hermes' native image backend is unavailable, but `codex` is installed and already logged in.

## Why this is CLI-based

This workflow depends on:

- the local `codex` executable
- `codex login status`
- `codex exec --skip-git-repo-check --ephemeral ...`
- verifying generated PNG files under `~/.codex/generated_images/`

So this is not a generic OpenAI Images API wrapper. It is a **Codex CLI workaround workflow** packaged as a Hermes skill.

## What it does

- launches a separate Codex CLI instance
- asks Codex to generate an image using its built-in `imagegen` skill
- captures the Codex session id
- re-verifies the actual output file from Hermes side
- warns that `bwrap` / sandbox inspection errors do not always mean the image generation failed

## What it does not do

- it does **not** rewire Hermes' native `image_generate` tool
- it does **not** directly call the OpenAI Images API
- it does **not** remove the need for a working Codex login

## Requirements

- Hermes Agent
- Codex CLI installed and on `PATH`
- active ChatGPT / OpenAI Codex OAuth login
- a Linux/macOS shell environment where `codex exec` can run

## Repository contents

- `SKILL.md` — the Hermes skill itself
- `README.md` — publish-facing overview
- `LICENSE` — MIT license
- `.gitignore` — minimal repo hygiene

## Published skill slug

For the published repo copy, I recommend this **skill** slug:

```text
codex-imagegen-via-chatgpt-oauth
```

It reads better than `codex-imagegen-chatgpt-oauth` and still keeps the auth mechanism explicit.

## Naming recommendation

For GitHub, I recommend the **repository** name:

```text
hermes-codex-imagegen-skill
```

Why:
- shorter than `hermes-codex-imagegen-chatgpt-oauth`
- still clearly tied to Hermes
- makes the repo purpose obvious at a glance
- keeps the implementation detail (`chatgpt-oauth`) inside the docs instead of bloating the repo slug

If you later want to rename the **skill** slug too, a cleaner option would be:

```text
codex-imagegen-via-chatgpt-oauth
```

## Quick publish flow

```bash
git init
git add .
git commit -m "Prepare the Hermes Codex image generation skill for publication"
gh repo create hermes-codex-imagegen-skill --public --source . --push
```

## Notes

The generated image files normally land under:

```text
~/.codex/generated_images/<session_id>/...
```

If you want to use a generated file in a real project, copy it into the project after verification instead of leaving it only under `.codex`.
