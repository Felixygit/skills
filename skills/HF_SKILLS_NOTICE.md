# Hugging Face marketplace skills

The directories listed in the `huggingface-skills` plugin
(`.claude-plugin/marketplace.json`) were installed from the Hugging Face
skills marketplace with:

```bash
hf skills add <name> --dest skills --force
```

Upstream: https://github.com/huggingface/skills  
License: Apache License 2.0 (see that repository's `LICENSE`, and
`huggingface-llm-trainer/LICENSE.txt`).

Refresh with the same `hf skills add` command. Prefer regenerating `hf-cli`
from your installed `huggingface_hub` version rather than hand-editing it.
