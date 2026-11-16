# Murshid Mini

A small, testable service-assistant project built as part of **LLM Application Engineering (SDA-AIE-213)**.

## Project Overview

Murshid Mini explores how to build reliable application workflows around language-model-style interactions. It begins with a predictable `FakeClient` and extends into structured ticket extraction, schema validation, a controlled read-only status lookup, and ownership checks.

The core work lives in **one cumulative notebook** (`notebooks/murshid.ipynb`) for the four-day course. Optional homework is kept in separate notebooks. **Days 1 and 2 are complete; Days 3 and 4 are not yet included.**

The core uses fictional service information and records. It does not connect to a real government system, database, or hosted LLM API.

## What I've Built

### Day 1 — Foundations
- Created fixed Arabic and English service responses using `FakeClient`.
- Built `ask(question)` with input validation, a trace ID, model information, and latency measurement.
- Checked expected answers and verified that blank input does not call the client.

### Day 2 — Structured Tickets and Read-Only Tools
- Added `extract_ticket()` to turn questions into structured fields: `service`, `city`, `request_id`, and `needs_clarification`.
- Added `validate_ticket()` to enforce field names, data types, formats, and consistency rules.
- Built `route()` and `check_status()` to handle clarification requests, read-only lookups, and record ownership checks.
- Rejected unsupported cancellation requests and kept the synthetic records unchanged.

### Day 2 Improvement — Data-Driven Validation
- Created **eight synthetic test cases** covering valid tickets, malformed fields, invalid values, missing fields, and clarification logic.
- Ran the dataset through the existing workflow and compared actual results with expected responses and tool-call counts.
- Saved the detailed report in [`day2_validation_results.json`](results/core/day2_validation_results.json).

### Optional Homework
- **Day 1:** Ran `google/flan-t5-small` locally on a Colab CPU to generate an answer from a synthetic FAQ context.
- **Day 2:** Built a separate `dispatch(call, trusted_user)` workflow that validates structured tool calls, allows only `check_status`, and verifies record ownership.

## Key Results

| Component | Recorded result | Evidence |
| --- | --- | --- |
| Day 1 core | 3 expected FAQ answers verified; invalid blank input rejected | [Baseline results](results/core/baseline_results.json) |
| Day 2 core | **5/5 reference cases passed**; records unchanged | [Day 2 results](results/core/day2_results.json) |
| Day 2 validation improvement | **8/8 tests passed**; records unchanged | [Validation report](results/core/day2_validation_results.json) |
| Day 2 tool-dispatch homework | **7/7 tests passed**; records unchanged | [Homework notebook](homework/day2_tools.ipynb) |
| Day 1 real-model homework | Generated: **"Use its reference number"** | [Model report](results/day1/live_demo_result.json) |

The Day 2 reference cases check an owned request, a missing reference, a nonexistent record, a disallowed cancellation, and an unauthorized lookup. The extra validation tests verify that malformed tickets are rejected before a status-tool call.

The recorded Day 1 real-model run used **34 input tokens**, **6 output tokens including special tokens**, and **0.306 seconds of CPU inference**. These are observations from one run, not performance benchmarks.

## Repository Structure

```text
Murshid_Gamar_Sadaia/
├── README.md
├── notebooks/
│   └── murshid.ipynb                  # Cumulative core project (Days 1–2)
├── homework/
│   ├── day1_real_model.ipynb          # Real-model demonstration
│   └── day2_tools.ipynb               # Tool-dispatch homework
├── results/
│   ├── core/
│   │   ├── baseline_results.json      # Day 1 core output
│   │   ├── day2_results.json          # Day 2 core output
│   │   └── day2_validation_results.json  # Eight-case validation report
│   └── day1/
│       └── live_demo_result.json      # Day 1 real-model output
└── docs/
    └── project-notes.md               # Design decisions and limitations
```

## How to Run

**Core project**

1. Open [`notebooks/murshid.ipynb`](notebooks/murshid.ipynb) in Google Colab.
2. Use a Python 3 runtime (Python 3.9 or newer) with CPU.
3. Run all cells in order. Both Day 1 and Day 2 should print `"checks": "passed"`, and the additional Day 2 validation report should show `"passed": 8`.

The core uses Python's standard library; no extra packages, API keys, or model downloads are required.

**Day 1 optional homework**

Open [`homework/day1_real_model.ipynb`](homework/day1_real_model.ipynb) separately and run its cells in order. It uses `transformers==4.49.0` and `sentencepiece==0.2.1`, downloading the public `google/flan-t5-small` model on first use. The SentencePiece version was adjusted after an installation issue in the Colab Python 3.13 environment. A GPU or paid inference API is not required.

**Day 2 optional homework**

Open [`homework/day2_tools.ipynb`](homework/day2_tools.ipynb) separately and run its three code cells in order. The expected result is `"passed": 7` with `"records_unchanged": true`. No package installation is required.

## Design Notes and Limitations

- **Validation** checks the shape and values of input data; **authorization** checks whether the current user may access a record.
- The demo user identity is supplied separately from the question or tool-call arguments. It is a teaching fixture, **not production authentication**.
- Status lookups are read-only. Missing and unauthorized records receive the same outward-facing message in the core workflow.
- The Day 2 homework uses hand-written tool-call objects, **not model-selected or provider-native tool calling**.
- The real-model homework demonstrates generation from model weights, whereas `FakeClient` returns predefined responses. Neither example establishes production readiness.

More implementation details are in [`docs/project-notes.md`](docs/project-notes.md).

---

**Course:** LLM Application Engineering — SDA-AIE-213  
**Training context:** [SDAIA Academy](https://github.com/SDAIAAcademy)  
**Instructor:** Meaad Al-Marri
