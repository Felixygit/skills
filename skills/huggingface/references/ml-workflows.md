# ML workflows: transformers, datasets, Gradio

Install common stack as needed:

```bash
pip install -U transformers datasets accelerate evaluate
# optional: peft trl gradio sentencepiece protobuf
```

Prefer reading current docs when APIs look unfamiliar — library defaults move quickly.

## Quick inference with `pipeline`

```python
from transformers import pipeline

pipe = pipeline(
    "text-generation",
    model="openai-community/gpt2",
    device_map="auto",  # needs accelerate + GPU when available
)
print(pipe("Once upon a time", max_new_tokens=40)[0]["generated_text"])
```

Other common tasks: `automatic-speech-recognition`, `image-classification`, `object-detection`, `summarization`, `translation`, `fill-mask`, `feature-extraction`, `text-classification`.

## Load model + tokenizer explicitly

```python
from transformers import AutoTokenizer, AutoModelForCausalLM

model_id = "HuggingFaceTB/SmolLM2-135M-Instruct"
tok = AutoTokenizer.from_pretrained(model_id)
model = AutoModelForCausalLM.from_pretrained(model_id, device_map="auto")

messages = [{"role": "user", "content": "Write a haiku about GPUs."}]
inputs = tok.apply_chat_template(messages, return_tensors="pt", add_generation_prompt=True)
inputs = inputs.to(model.device)
out = model.generate(inputs, max_new_tokens=64)
print(tok.decode(out[0], skip_special_tokens=True))
```

Guidance:

- Prefer models with `safetensors` weights.
- For chat models, use `apply_chat_template` rather than hand-rolled prompts.
- For low VRAM: `load_in_4bit=True` via `bitsandbytes` / quantization configs, or smaller models.

## Datasets

```python
from datasets import load_dataset

ds = load_dataset("imdb", split="train[:1%]")
print(ds[0])

# Hub dataset with config / streaming
stream = load_dataset("HuggingFaceFW/fineweb", split="train", streaming=True)
print(next(iter(stream)))
```

Push a processed dataset:

```python
ds.push_to_hub("my-user/my-dataset", private=False)
```

## Fine-tuning pointers

- Full / efficient LLM fine-tunes: **TRL** + **PEFT (LoRA/QLoRA)** — install official skill `trl-training` / `huggingface-llm-trainer` when doing a real training run ([official-skills.md](official-skills.md)).
- Vision training: `huggingface-vision-trainer`.
- Keep training scripts reproducible: seed, dataset revision hash, model revision, and push checkpoints with `hf upload` or `Trainer` Hub integration (`push_to_hub=True`).

Minimal Trainer-shaped sketch (customize heavily per task):

```python
from transformers import AutoModelForSequenceClassification, AutoTokenizer, Trainer, TrainingArguments

model_id = "distilbert/distilbert-base-uncased"
tok = AutoTokenizer.from_pretrained(model_id)
model = AutoModelForSequenceClassification.from_pretrained(model_id, num_labels=2)
# ... tokenize dataset ...
args = TrainingArguments(output_dir="./out", per_device_train_batch_size=8, num_train_epochs=1)
trainer = Trainer(model=model, args=args, train_dataset=tokenized_train)
trainer.train()
trainer.push_to_hub("my-user/imdb-distilbert")
```

## Gradio Space sketch

```python
import gradio as gr
from transformers import pipeline

pipe = pipeline("sentiment-analysis")

def predict(text: str) -> str:
    return str(pipe(text)[0])

demo = gr.Interface(fn=predict, inputs="text", outputs="text", title="Sentiment")
demo.launch()
```

Space repo needs `app.py` (or SDK entry), `requirements.txt`, and a README with YAML metadata (`sdk: gradio`). Create with:

```bash
hf repos create my-user/my-space --type space --sdk gradio
hf upload my-user/my-space . --repo-type space
```

## Hosted vs local

| Need | Approach |
|---|---|
| No local GPU, quick try | `InferenceClient` or Inference Endpoints |
| Full control / offline | `transformers` local load |
| Share a demo | Gradio Space |
| Batch jobs on HF infra | `hf jobs` / official Jobs docs |

## Safety & practical constraints

- Respect model licenses and dataset terms; gate acceptance is required for many LLMs.
- Do not recommend scraping Hub HTML when `hf` / `huggingface_hub` expose the data.
- For "best model for X", use leaderboards / the `huggingface-best` skill rather than inventing rankings.
