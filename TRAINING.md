# Training the Model

Once you have generated the `data/train_clean.jsonl` file, you are ready to fine-tune an LLM.

## Recommended Base Models
Because IEC 61131-3 Structured Text is highly rigid and mathematical, you do not need a massive 70B parameter model.
- **Llama-3 (8B)** - Excellent reasoning capabilities.
- **Gemma-2 (9B)** - Phenomenal coding capabilities for its size.

## Dataset Format
The pipeline outputs data in the standard **ChatML format**:
```json
{"messages": [{"role": "user", "content": "Write Siemens SCL logic... "}, {"role": "assistant", "content": "IF bStart THEN..."}]}
```

## How to Fine-Tune (Using Unsloth)

The fastest and most cost-effective way to train this model is using **Unsloth** via Google Colab or a local GPU (RTX 3090 / 4090).

1. Install Unsloth:
```bash
pip install "unsloth[colab-new] @ git+https://github.com/unslothai/unsloth.git"
```

2. Python Training Script:
```python
from unsloth import FastLanguageModel
from datasets import load_dataset
from trl import SFTTrainer
from transformers import TrainingArguments
import torch

# 1. Load the Llama-3 8B Model (Memory efficient for T4)
max_seq_length = 1024 # Reduced to 1024 to make it absolutely bulletproof against OOM
model, tokenizer = FastLanguageModel.from_pretrained(
    model_name = "unsloth/llama-3-8b-Instruct-bnb-4bit",
    max_seq_length = max_seq_length,
    load_in_4bit = True,
)

# 2. Add LoRA Adapters
model = FastLanguageModel.get_peft_model(
    model,
    r = 16,
    target_modules = ["q_proj", "k_proj", "v_proj", "o_proj", "gate_proj", "up_proj", "down_proj"],
    lora_alpha = 16,
    lora_dropout = 0,
)

# 3. Load your uploaded PLC dataset from the Colab Sidebar
dataset = load_dataset("json", data_files="/content/train_clean.jsonl", split="train")

# Convert the generic messages into Llama-3's specific conversational format
def formatting_prompts_func(examples):
    convos = examples["messages"]
    texts = [tokenizer.apply_chat_template(convo, tokenize=False, add_generation_prompt=False) for convo in convos]
    return { "text" : texts }

dataset = dataset.map(formatting_prompts_func, batched = True)

# 4. Start the Training!
trainer = SFTTrainer(
    model = model,
    tokenizer = tokenizer,
    train_dataset = dataset,
    dataset_text_field = "text", 
    max_seq_length = max_seq_length,
    args = TrainingArguments(
        per_device_train_batch_size = 1, # Bulletproof setting: 1 file at a time
        gradient_accumulation_steps = 8, 
        warmup_steps = 5,
        max_steps = 200, 
        learning_rate = 2e-4,
        fp16 = not torch.cuda.is_bf16_supported(),
        bf16 = torch.cuda.is_bf16_supported(),
        logging_steps = 10,
        optim = "adamw_8bit",
        output_dir = "outputs",
    ),
)
trainer.train()

# 5. Save your new proprietary PLC AI
model.save_pretrained("/content/PLC_AI_Model")
```
