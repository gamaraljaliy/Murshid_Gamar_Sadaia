# Murshid Mini

A small service-assistant project built for **LLM Application Engineering (SDA-AIE-213)**. The project starts with a predictable mock client and progresses through the four-day course. All examples use fictional service information.

## Current progress

**Core project — Day 1: Foundations**
- Built an Arabic/English service directory and a `FakeClient` with predefined responses.
- Added an `ask(question)` function with input validation, a trace ID, model name and latency.
- Tested three service questions and checked that an empty question does not call the client.
- Saved the baseline results as JSON. The recorded checks passed.

**Optional homework — Day 1: Real-model demo**
- Ran `google/flan-t5-small` locally in a separate Google Colab CPU runtime.
- Passed synthetic context and a question to the model, generated an answer, and saved the report.
- Reviewed the answer for unsupported claims.

Days 2–4 will be added as they are completed.

## Repository structure

```text
murshid-mini/
├── README.md
├── notebooks/
│   └── murshid.ipynb
├── homework/
│   └── day1_real_model.ipynb
├── results/
│   ├── core/
│   │   └── baseline_results.json
│   └── day1/
│       └── live_demo_result.json
└── docs/
    └── project-notes.md
```

## Run the core project

1. Open [notebooks/murshid.ipynb](notebooks/murshid.ipynb) in Google Colab.
2. Select a Python 3 runtime (Python 3.9 or newer).
3. Run the cells in order.
4. Check the printed `"checks": "passed"` message and review [baseline_results.json](results/core/baseline_results.json).

The core Day 1 lab uses only Python's standard library. It does not download a model or call an external API.

## Run the Day 1 homework

1. Open [homework/day1_real_model.ipynb](homework/day1_real_model.ipynb) in a **separate** Colab notebook and Python 3 CPU runtime.
2. Run all four cells in order. The first run downloads the model and dependencies.
3. Review the generated response, token counts and inference time.
4. The notebook writes `murshid_live_outputs/live_demo_result.json`; the saved report is also in [results/day1/live_demo_result.json](results/day1/live_demo_result.json).

The notebook uses `transformers==4.49.0` and `sentencepiece==0.2.1`. SentencePiece was changed from 0.2.0 because the original version failed to install in the Python 3.13 Colab runtime. No paid inference API or GPU is required.

## Day 1 homework result

**Context:** You can check your application status using its reference number.

**Question:** How can I check my application status?

**Generated answer:** Use its reference number

| Metric | Recorded value |
| --- | --- |
| Model | `google/flan-t5-small` |
| Device | CPU |
| Input tokens | 34 |
| Output tokens (including special tokens) | 6 |
| Inference time | 0.306 seconds |
| External inference API calls | 0 |

The answer uses the reference number from the context and does not invent a fee, date or status. This is one example, not a broad model evaluation.

## Real model vs. FakeClient

The tokenizer converts the context and question into input token IDs; FLAN-T5 generates output tokens, which are decoded into a readable answer. Unlike `FakeClient`, which returns fixed answers from a dictionary, FLAN-T5 generates its response using model weights.

## Notes

See [project notes](docs/project-notes.md) for the data, design decisions, testing approach and limitations. This project uses synthetic data and does not access real applications or perform real transactions.

**Course:** LLM Application Engineering — SDA-AIE-213  
**Instructor:** Meaad Al-Marri
