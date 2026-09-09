# Official Hugging Face agent skills

Specialized workflows live in [huggingface/skills](https://github.com/huggingface/skills). Install them with the Hub CLI rather than reinventing them here.

## Install

```bash
# list available skills
hf skills list

# install one into the current agent/skills destination
hf skills add huggingface-best
hf skills add huggingface-llm-trainer

# refresh installed HF skills
hf skills update
```

Claude Code marketplace (bootstrap `hf-cli` only):

```text
/plugin marketplace add huggingface/skills
/plugin install hf-cli@huggingface/skills
```

Then use `hf skills add <name>` for the rest.

## Skill picker

| Task | Skill name |
|---|---|
| Hub CLI deep reference (auto-generated) | `hf-cli` |
| Best / recommended model for a task | `huggingface-best` |
| Datasets workflows | `huggingface-datasets` |
| Gradio apps | `huggingface-gradio` |
| LLM training | `huggingface-llm-trainer` |
| Vision training | `huggingface-vision-trainer` |
| Local models | `huggingface-local-models` |
| Spaces | `huggingface-spaces` |
| LoRA Space builder | `huggingface-lora-space-builder` |
| Papers on the Hub | `huggingface-papers` |
| Paper publishing | `huggingface-paper-publisher` |
| Tool builder | `huggingface-tool-builder` |
| Trackio | `huggingface-trackio` |
| ZeroGPU | `huggingface-zerogpu` |
| Community evals | `huggingface-community-evals` |
| TRL training | `trl-training` |
| Sentence Transformers | `train-sentence-transformers` |
| transformers.js | `transformers-js` |
| HF memory helpers | `hf-mem` |
| SageMaker / AWS HF Cloud helpers | `hf-cloud-*` |

If `hf skills` is unavailable, point the user at https://github.com/huggingface/skills and copy the needed `skills/<name>/` folder into their agent's skills directory.

## How this skill relates

- **`huggingface` (this repo)** — everyday Hub + transformers/datasets guidance and routing.
- **`hf-cli` / specialized HF skills** — authoritative, often generated or domain-deep instructions. Prefer them when the user's task clearly matches a row above.
