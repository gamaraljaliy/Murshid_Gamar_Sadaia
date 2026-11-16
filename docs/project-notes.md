# Project Notes

## Goal and scope

Murshid Mini is a learning project for building a structured, testable service assistant. The service directory and questions are synthetic; no real applications, users or government services are connected.

The repository keeps **one cumulative core notebook** and a separate notebook for each completed optional homework exercise. The Day 1 real-model extension is not part of the core mock workflow.

## Day 1 core: data and design

The `SERVICE_DIRECTORY` dictionary holds fictional application-status help text in Arabic (`ar`) and English (`en`). The notebook defines `FAQ_AR` and `FAQ_EN` for its fixed demonstration questions.

- `FakeClient.generate(question)` matches the input to a predefined response and increments the call counter.
- `ask(question)` validates that the question is a non-empty string. Invalid input returns a helpful error message without calling `FakeClient`.
- Valid calls return `trace_id`, `answer`, `model` and `latency_ms`.
- `save_json(...)` writes the saved test results under `murshid_outputs/` inside the Colab runtime.

The core notebook checks three expected answers, checks the result fields, and confirms that an empty question does not invoke the client. Its recorded Day 1 checks passed. The corresponding exported data is at `results/core/baseline_results.json`.

This stage uses Python's standard library only: no hosted API, authentication token or real inference model.

## Day 1 homework: real model

The homework uses `google/flan-t5-small` with a pinned model revision and runs inference on CPU. The notebook follows this sequence:

1. Load the tokenizer and model.
2. Build a prompt containing the synthetic context and question.
3. Convert the prompt to input token IDs.
4. Generate output token IDs with deterministic decoding (`do_sample=False`, `num_beams=1`).
5. Decode the generated tokens and record the result and measurements.

**Context:** You can check your application status using its reference number.

**Question:** How can I check my application status?

**Recorded answer:** Use its reference number

The saved report records 34 input tokens, 6 output tokens including special tokens, and 0.306 seconds of inference. Inference timing depends on the runtime.

The successful Colab environment used `transformers==4.49.0`, `sentencepiece==0.2.1` and preinstalled PyTorch `2.14.1+cpu`. The SentencePiece version differs from the reference notebook's 0.2.0 due to an installation failure on Python 3.13. A public model does not require a paid inference key.

## Difference between the approaches

`FakeClient` looks up an exact question and returns a response that was written ahead of time. FLAN-T5 converts a prompt to tokens, generates tokens with learned model weights, and decodes them into text.

A non-empty generated answer is a successful execution check, not proof of correctness. For this example, the answer correctly mentions the reference number, with no invented fees, dates or status claims.

## Running and reproducing

- **Core:** run `notebooks/murshid.ipynb` top-to-bottom using Python 3.9+ in Colab.
- **Homework:** run `homework/day1_real_model.ipynb` top-to-bottom in a separate Colab Python 3 CPU runtime with internet access to download dependencies and model files.
- **Saved outputs:** `results/core/baseline_results.json` and `results/day1/live_demo_result.json`.

## Limitations and next steps

This is a teaching exercise using a small synthetic example. It does not test broad factual accuracy, Arabic performance or production readiness. The core workflow is still mock-based after Day 1. Future core lab stages and optional homework will be added only when completed.
