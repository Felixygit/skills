# Hugging Face Hub CLI (`hf`)

Install if missing:

```bash
curl -LsSf https://hf.co/cli/install.sh | bash -s
# or: pip install -U "huggingface_hub[cli]"
hf version
```

`hf` replaces the deprecated `huggingface-cli`. Prefer `--format json` or `--format agent` when an agent must parse output.

## Auth

```bash
hf auth login          # browser or paste token
hf auth whoami
hf auth list
hf auth logout
```

Token env var used by CLI and libraries: `HF_TOKEN`.

## Download / upload

```bash
# Single file or full repo
hf download OWNER/REPO [FILENAME] --repo-type model|dataset|space
hf download OWNER/REPO --include "*.safetensors" --local-dir ./weights
hf download OWNER/REPO --revision main --dry-run

# Upload (creates a commit; use --create-pr for a PR)
hf upload OWNER/REPO ./local-path --repo-type model --commit-message "msg"
hf upload OWNER/REPO ./data --repo-type dataset --private
```

## Repos

```bash
hf repos create OWNER/NAME --type model|dataset|space [--private] [--exist-ok]
hf repos list --namespace OWNER --type model
hf repos settings OWNER/NAME --gated auto|manual|false
hf repos delete OWNER/NAME --type model --yes   # irreversible — confirm with user first
hf repos branch create OWNER/NAME branch-name
hf repos tag create OWNER/NAME v1.0
```

Space create extras: `--sdk gradio|streamlit|docker|static`, optional `--flavor` hardware.

## Browse models, datasets, spaces, papers

```bash
hf models list --search QUERY --filter TAG --pipeline-tag text-generation \
  --sort downloads --limit 20 --format json
hf models info OWNER/NAME --format json
hf models card OWNER/NAME

hf datasets list --search QUERY --sort downloads --limit 20
hf datasets info OWNER/NAME
hf datasets parquet OWNER/NAME          # parquet URLs for SQL / DuckDB
hf datasets sql "SELECT * FROM read_parquet('URL') LIMIT 5"

hf spaces list --search QUERY
hf spaces info OWNER/NAME
hf spaces logs OWNER/NAME --tail 100
hf spaces restart OWNER/NAME

hf papers search QUERY
hf papers read PAPER_ID
```

## Cache

```bash
hf cache list --limit 20 --sort size:desc
hf cache rm OWNER/REPO --dry-run
hf cache prune --dry-run
```

## Jobs, buckets, endpoints (when relevant)

```bash
hf jobs --help
hf buckets --help
hf endpoints --help
hf sandbox --help
```

Use these for Hugging Face Jobs, storage buckets, Inference Endpoints, and experimental sandboxes. Run `hf <group> --help` for current flags — do not invent options from memory.

## Skills marketplace (official HF skills)

```bash
hf skills list
hf skills add huggingface-best
hf skills update
```

See [official-skills.md](official-skills.md) for which skill to install for a given task.

## Agent tips

- Start with `--dry-run` for downloads/uploads that might be large or destructive.
- Filter large model repos with `--include` / `--exclude` (prefer `*.safetensors`, tokenizer files, `config.json`).
- After gated-model failures, tell the user to open the model page, accept terms, then retry authenticated.
- Prefer `hf models info` / `hf datasets info` over scraping HTML.
