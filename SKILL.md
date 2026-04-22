---
name: codex-imagegen-chatgpt-oauth
description: Use a separate Codex CLI instance logged in via ChatGPT / OpenAI Codex OAuth to generate an image with Codex's built-in imagegen skill, then verify the saved PNG from ~/.codex/generated_images.
version: 1.1.0
author: Hermes Agent
license: MIT
---

# Codex image generation via ChatGPT OAuth

Use this when Hermes' own `image_generate` tool is unavailable because FAL or the Nous Tool Gateway is not configured, but the machine has a working Codex CLI session authenticated through ChatGPT / OpenAI Codex OAuth.

## What this is

This is a **workaround through a separate Codex process**.

It does **not** rewire Hermes' native `image_generate` tool to use Codex or the OpenAI Images API directly.

## Preconditions

1. `codex` is installed and on `PATH`
2. `codex login status` returns a live ChatGPT login, for example `Logged in using ChatGPT`
3. You accept that generated files land under `~/.codex/generated_images/<session_id>/...`

## Required checks

Run these first:

```bash
which codex && codex --version
codex login status
codex exec --help | head -n 40
```

Why:
- confirms Codex is really installed
- confirms OAuth is actually live
- confirms the local CLI supports `exec`, `--skip-git-repo-check`, and sandbox flags

## Core workflow

### 1. Create a temp workspace

```bash
TMP=$(mktemp -d /tmp/codex-image-XXXXXX)
```

### 2. Run Codex non-interactively

Use `--skip-git-repo-check` because image generation does not need a repository.
Use `--ephemeral` so you do not leave extra Codex session state behind.
Use `-o "$TMP/last.txt"` so the final assistant message is saved locally too.

```bash
codex exec \
  --skip-git-repo-check \
  --ephemeral \
  --sandbox workspace-write \
  -C "$TMP" \
  -o "$TMP/last.txt" \
  'You are testing whether image generation is available. Generate an image of "<user request>". If possible, save the resulting image to a file or leave a URL. If that is not possible, explain exactly why. In your final answer, respond in the user\'s language and include the success or failure status plus the output path or URL.'
```

### 3. Capture the session id from stdout

Codex prints a line like:

```text
session id: 019db2a7-8461-7251-9281-46a7606caade
```

Keep that id.

### 4. Verify the actual PNG outside Codex

Do **not** rely only on Codex's self-report.
Verify from Hermes side by searching `~/.codex/generated_images/`.

Preferred:

```text
search_files(target='files', path='~/.codex/generated_images/<session_id>', pattern='*')
```

Broad fallback if the session id was missed:

```text
search_files(target='files', path='~/.codex/generated_images', pattern='*.png', limit=20)
```

### 5. Optionally inspect the image

Use `vision_analyze` to quickly check whether the output actually matches the prompt before presenting it.

## Recommended Hermes tool sequence

1. `terminal` — verify Codex install and login, then run `codex exec`
2. `search_files` — confirm the generated file exists
3. `vision_analyze` — sanity-check prompt fit

## Known pitfall

Codex may show sandbox or bubblewrap errors like:

```text
bwrap: loopback: Failed RTM_NEWADDR: Operation not permitted
```

This can happen **after** the image was already generated, when Codex tries to inspect files inside its own sandbox.

Do not treat that message as proof of generation failure.

Instead:
- verify the real output from Hermes side
- search `~/.codex/generated_images/`
- trust actual file existence over Codex's post-generation introspection errors

## Good prompt wrapper

Use a wrapper that forces Codex to explicitly report success or failure and leave a path or URL:

```text
Generate an image of "<scene>". If possible, save the resulting image to a file or leave a URL. If that is not possible, explain exactly why. In your final answer, respond in the user's language and include the success or failure status plus the output path or URL.
```

## Example used successfully

User request:

```text
A view of Sangam-dong, Seoul at 7 PM
```

Observed file pattern:

```text
~/.codex/generated_images/<session_id>/ig_<hash>.png
```

## If the image must live in the current project

Codex normally writes into `~/.codex/generated_images/...`.
If the result should become a real project asset, copy it into the workspace after verification.

Example:

```bash
cp ~/.codex/generated_images/<session_id>/ig_<hash>.png ./public/images/
```

Do not leave a project-referenced asset only inside `~/.codex/generated_images/`.

## Verification checklist

- Codex installed
- ChatGPT login active
- `codex exec` exits successfully
- PNG exists under `~/.codex/generated_images`
- optional: visual inspection says the image roughly matches the request

## When not to use

Do **not** use this workflow when:
- the user expects this to change Hermes' native image backend
- Codex is not logged in
- the output must land in a deterministic path without a follow-up copy step
- the task requires a repeatable official API workflow rather than a tool-side workaround
