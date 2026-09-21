# Python: `huggingface_hub`

```bash
pip install -U huggingface_hub
```

## Auth

```python
import os
from huggingface_hub import login

# Prefer env: export HF_TOKEN=hf_...
login(token=os.environ.get("HF_TOKEN"))  # no-op-ish if already logged in
```

Or rely on `HF_TOKEN` / cached login without calling `login()`.

## Download

```python
from huggingface_hub import hf_hub_download, snapshot_download

path = hf_hub_download(repo_id="gpt2", filename="config.json")

# Full or filtered snapshot
snapshot_download(
    repo_id="openai/whisper-tiny",
    local_dir="./whisper-tiny",
    allow_patterns=["*.json", "*.safetensors", "tokenizer*"],
)
```

`repo_type` can be `"model"` (default), `"dataset"`, or `"space"`.

## Upload & repo management

```python
from huggingface_hub import HfApi

api = HfApi()
api.create_repo("my-user/my-model", repo_type="model", exist_ok=True, private=False)

api.upload_file(
    path_or_fileobj="README.md",
    path_in_repo="README.md",
    repo_id="my-user/my-model",
)

api.upload_folder(
    folder_path="./output",
    repo_id="my-user/my-model",
    commit_message="Add weights",
    allow_patterns=["*.safetensors", "*.json"],
)
```

Useful reads: `api.model_info`, `api.list_models`, `api.list_datasets`, `api.list_repo_files`.

## Inference (hosted)

```python
from huggingface_hub import InferenceClient

client = InferenceClient(token=os.environ.get("HF_TOKEN"))

# Chat / text generation (model must support the task / provider)
out = client.chat_completion(
    model="meta-llama/Meta-Llama-3-8B-Instruct",
    messages=[{"role": "user", "content": "Say hi in one sentence."}],
    max_tokens=64,
)
print(out.choices[0].message.content)
```

For local models, use `transformers` (see [ml-workflows.md](ml-workflows.md)), not `InferenceClient`.

## Model / dataset cards

```python
from huggingface_hub import ModelCard, DatasetCard

card = ModelCard.load("gpt2")
print(card.data)  # metadata
```

When publishing, write a clear model/dataset card (license, intended use, training data, limitations).

## Pitfalls

- Pass `token=True` or rely on env/cache — do not hardcode secrets in source.
- Large uploads: prefer `upload_folder` with patterns; for huge trees follow current Hub large-upload guidance (`hf upload` is preferred over deprecated `upload-large-folder`).
- Gated repos raise auth/access errors until the user accepts the license on the Hub and is logged in.
- Cache lives under `HF_HOME` (default `~/.cache/huggingface`). Respect `HF_HUB_CACHE` / `HF_HOME` when debugging disk use.
