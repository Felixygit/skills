---
name: huggingface
description: >-
  Guide for working with the Hugging Face Hub and ecosystem — auth, models,
  datasets, Spaces, Inference, transformers, and the `hf` CLI / `huggingface_hub`
  Python SDK. Use whenever the user mentions Hugging Face, huggingface, HF Hub,
  `hf`, `huggingface-cli`, transformers, datasets library, Gradio Spaces,
  Inference Endpoints, model cards, or wants to download/upload/search models
  or datasets, run Hub inference, publish a Space, or pick an open model.
  Prefer this skill over guessing Hub APIs from memory; Hub CLI shape drifts
  (`huggingface-cli` is deprecated in favor of `hf`).
license: Complete terms in LICENSE.txt
---

# Hugging Face Hub & Ecosystem

Help the user get real work done on the Hugging Face Hub and with HF libraries.
Prefer live CLI / docs over recalled API shapes — the Hub surface changes often.

## Defaults

| Concern | Default |
|---|---|
| CLI | `hf` (never the deprecated `huggingface-cli`) |
| Python Hub SDK | `huggingface_hub` (`HfApi`, `hf_hub_download`, `snapshot_download`, `InferenceClient`) |
| Auth | `HF_TOKEN` env var, or `hf auth login` / `huggingface_hub.login()` |
| Machine learning | `transformers` + `datasets` (+ `accelerate` / `peft` / `trl` when fine-tuning) |
| Demos | Gradio on Spaces |
| Agent output | Prefer `--format json` / `--format agent` on `hf` when parsing programmatically |

## Workflow

1. **Identify the job** — auth, search, download, upload, inference, train/fine-tune, Space, or model recommendation.
2. **Check auth** when the task needs private repos, uploads, gated models, Jobs, or Inference Endpoints:
   ```bash
   hf auth whoami
   ```
   If not logged in, guide `hf auth login` or set `HF_TOKEN` (create tokens at https://huggingface.co/settings/tokens). Never print or commit tokens.
3. **Prefer the `hf` CLI for Hub operations** (download, upload, repo CRUD, search, Spaces, papers, jobs). Use Python `huggingface_hub` when embedding Hub calls in application code.
4. **Read the matching reference** before writing commands or code:
   - Hub CLI patterns → [references/hub-cli.md](references/hub-cli.md)
   - Python Hub SDK → [references/python-hub.md](references/python-hub.md)
   - transformers / datasets / inference recipes → [references/ml-workflows.md](references/ml-workflows.md)
   - Deeper official HF agent skills → [references/official-skills.md](references/official-skills.md)
5. **Verify** with a dry-run or read-only call when destructive (`hf repos delete`, cache prune, Space hardware changes).

## Job → action map

| User wants… | Do this |
|---|---|
| Log in / who am I | `hf auth whoami` / `hf auth login` |
| Find a model or dataset | `hf models list --search …` / `hf datasets list --search …` |
| Download weights or a dataset repo | `hf download OWNER/NAME [--include …]` |
| Upload a folder or checkpoint | `hf upload OWNER/NAME PATH` (create repo first if needed) |
| Create a model/dataset/Space repo | `hf repos create OWNER/NAME --type model\|dataset\|space` |
| Run hosted inference | Python `InferenceClient` (see ml-workflows.md) |
| Load a model in Python | `transformers` `pipeline` / `AutoModel*` (see ml-workflows.md) |
| Recommend the best model for a task | Follow [references/official-skills.md](references/official-skills.md) (`huggingface-best`) or query official leaderboards |
| Build / debug a Space | `hf spaces …` + Gradio patterns in ml-workflows.md |
| Install / refresh specialized HF skills | `hf skills add <name> --dest skills --force` (see official-skills.md; many are already vendored here) |

## Hard rules

- Use `hf`, not `huggingface-cli`.
- Do not invent model IDs, dataset IDs, or leaderboard scores — look them up with `hf` / Hub API.
- Do not bypass gated-model access; tell the user to accept the model license on the Hub UI, then retry with auth.
- Prefer `safetensors` over pickle/`bin` when recommending download filters.
- For large repos, download selectively (`--include` / `allow_patterns`) instead of full snapshots unless needed.
- When writing app code, read tokens from the environment (`HF_TOKEN`) — never hardcode them.

## Quick examples

**Download a single file**
```bash
hf download gpt2 config.json
```

**Download a full model snapshot into a local folder**
```bash
hf download openai/whisper-tiny --local-dir ./whisper-tiny
```

**Upload a trained checkpoint**
```bash
hf repos create my-user/my-model --type model --exist-ok
hf upload my-user/my-model ./output --commit-message "Add fine-tuned weights"
```

**Search instruction-tuned LLMs**
```bash
hf models list --search "instruct" --filter "text-generation" --sort downloads --limit 10 --format json
```

## When to hand off to official Hugging Face skills

This skill covers everyday Hub + library work. Specialized marketplace skills
(`hf-cli`, `huggingface-best`, `huggingface-llm-trainer`, Spaces, Gradio, SageMaker
helpers, etc.) are already installed under `skills/` via `hf skills add`. When the
user's task matches one of them, **read that skill's `SKILL.md` next** — see
[references/official-skills.md](references/official-skills.md). To refresh or add
more: `hf skills add <name> --dest skills --force`.
