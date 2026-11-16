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

## Day 2 core: structured tickets and read-only status

Day 2 extends the same cumulative notebook with Cells 4–6. It uses synthetic records only:

- `REF-1001` belongs to `demo-user-1` and has status `under_review`.
- `REF-1002` belongs to `demo-user-2` and has status `approved`.
- `REF-9999` does not exist.

The demo uses `SESSION = 'demo-user-1'` as a test identity supplied separately from the question. This is **not** real authentication.

1. `extract_ticket(question)` finds the reference and converts the question to a structured dictionary containing `service`, `city`, `request_id`, and `needs_clarification`.
2. `validate_ticket(ticket)` checks the exact fields, accepted values, reference format, Boolean type, and consistency of the clarification flag.
3. `route(question, session)` refuses cancellation requests, invalid tickets and incomplete status requests without calling the status tool.
4. `check_status(request_id, session)` looks up the record and checks its owner before returning the status.

For the reference question `ما حالة طلبي REF-1001 في الرياض؟`, the ticket specifies `application_status`, `Riyadh`, `REF-1001` and `needs_clarification=False`; the returned status is `under_review`.

### Tests and review

The five recorded case codes are `under_review`, `needs_clarification`, `not_found`, `unsupported_action` and `access_denied`. The corresponding status-tool call counts are `[1, 0, 1, 0, 1]`. The tests also verify that `REF-1002` does not expose its real status to the demo user, that a Day 1 FAQ still works, and that `RECORDS == INITIAL_RECORDS`.

**Testing improvement:** An extra negative check was added to the Day 2 core notebook to reject a ticket whose `needs_clarification` value is the string `'False'` rather than a Boolean. The saved output reports `Extra validation test: passed`, with zero status-tool calls for the invalid ticket. The main five-case tests remain unchanged.

**Notebook execution metadata:** The latest GitHub-saved core notebook retains Day 1/Day 2 output logs, but its code-cell execution counts are `null` after saving. The preserved outputs show successful execution; the notebook should be rerun top-to-bottom when opened in a fresh runtime.

**Limitation:** The records and user identity are fictional. Error codes in the JSON results are for classroom inspection; real status services need stronger authentication, authorization, logging and privacy controls.

## Day 2 homework: validate and dispatch structured tool calls

The optional homework is saved separately as `homework/day2_tools.ipynb`; it does not change the cumulative core notebook.

The `dispatch(call, trusted_user)` function accepts a dictionary with exactly the fields `name` and `arguments`. It permits only `check_status`, requires a single string `reference` argument in the `REF-0000` format, and then checks record ownership against the separately supplied demo user.

- **Validation** checks whether the call structure, tool name, argument fields and value format are valid. Invalid requests are rejected before record lookup.
- **Authorization** checks whether the trusted current user owns the record. A valid reference does not automatically permit access.
- The dispatcher returns the same outward `unavailable` result for absent records and records owned by another user, limiting information disclosure.
- These calls are supplied directly as simulated inputs; this is not native LLM tool calling or production authentication.

The seven saved automated tests cover an owned reference, an unowned reference, a missing reference, a disallowed tool, an extra owner argument, a numeric reference, and a string instead of a call dictionary. The report shows `passed: 7`, `total: 7` and `records_unchanged: true`. Each individual case reports `passed: true`. The notebook uses Python's standard library only.

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
- **Day 1 homework:** run `homework/day1_real_model.ipynb` top-to-bottom in a separate Colab Python 3 CPU runtime with internet access to download dependencies and model files.
- **Day 2 homework:** run `homework/day2_tools.ipynb` top-to-bottom in a separate Colab Python 3 CPU runtime. No downloads are required.
- **Saved outputs:** `results/core/baseline_results.json`, `results/core/day2_results.json`, and `results/day1/live_demo_result.json`.

## Limitations and next steps

This is a teaching exercise using a small synthetic example. It does not test broad factual accuracy, Arabic performance or production readiness. The core still uses a mock client and a local read-only status tool after Day 2. Future core stages and optional homework will be added only when completed.
