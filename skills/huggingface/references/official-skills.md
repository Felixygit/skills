# Official Hugging Face agent skills

These skills are installed from the Hugging Face marketplace with:

```bash
hf skills add <name> --dest skills --force
```

They live next to this skill under `skills/` and are registered in the
`huggingface-skills` plugin in `.claude-plugin/marketplace.json`.

Upstream source: [huggingface/skills](https://github.com/huggingface/skills) (Apache-2.0).
See also [`skills/HF_SKILLS_NOTICE.md`](../../HF_SKILLS_NOTICE.md).

## Refresh / install

```bash
# list marketplace skills
hf skills list

# install or refresh one skill into this repo
hf skills add hf-cli --dest skills --force
hf skills add huggingface-best --dest skills --force

# install every marketplace skill into skills/
hf skills list --format json | python3 -c "
import json, sys, subprocess
for row in json.load(sys.stdin):
    subprocess.check_call(['hf', 'skills', 'add', row['name'], '--dest', 'skills', '--force'])
"
```

Claude Code can also bootstrap just the CLI skill via marketplace:

```text
/plugin marketplace add huggingface/skills
/plugin install hf-cli@huggingface/skills
```

Then use `hf skills add <name>` for the rest.

## Skill picker

| Task | Skill directory |
|---|---|
| Hub CLI deep reference (auto-generated) | `hf-cli` |
| Best / recommended model for a task | `huggingface-best` |
| Datasets / Dataset Viewer API | `huggingface-datasets` |
| Gradio apps | `huggingface-gradio` |
| LLM / vision training on HF Jobs (TRL, Unsloth) | `huggingface-llm-trainer` |
| Vision training (detection, classification, SAM) | `huggingface-vision-trainer` |
| Local / GGUF models (llama.cpp) | `huggingface-local-models` |
| Spaces deploy & debug | `huggingface-spaces` |
| LoRA Space builder | `huggingface-lora-space-builder` |
| Papers on the Hub | `huggingface-papers` |
| Paper publishing | `huggingface-paper-publisher` |
| Tool / script builder using HF APIs | `huggingface-tool-builder` |
| Trackio experiment tracking | `huggingface-trackio` |
| ZeroGPU Spaces | `huggingface-zerogpu` |
| Community evals (inspect-ai / lighteval) | `huggingface-community-evals` |
| TRL CLI training | `trl-training` |
| Sentence Transformers training | `train-sentence-transformers` |
| Transformers.js | `transformers-js` |
| Memory estimation for Hub weights | `hf-mem` |
| AWS context discovery | `hf-cloud-aws-context-discovery` |
| Python env for SageMaker / AWS | `hf-cloud-python-env-setup` |
| SageMaker deployment planner | `hf-cloud-sagemaker-deployment-planner` |
| SageMaker IAM preflight | `hf-cloud-sagemaker-iam-preflight` |
| SageMaker production defaults | `hf-cloud-sagemaker-production-defaults` |
| Serving image selection | `hf-cloud-serving-image-selection` |

When a row matches the user's task, **read that skill's `SKILL.md`** (and its
references) instead of improvising — those skills are authoritative.

## How this skill relates

- **`huggingface` (router)** — everyday Hub + transformers/datasets guidance and routing into the table above.
- **Marketplace skills in this folder** — installed via `hf skills add`; prefer them for specialized work.
