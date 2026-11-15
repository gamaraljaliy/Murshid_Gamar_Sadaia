# Murshid Mini

Murshid Mini is a Python-based service assistant developed as part of the LLM Application Engineering course (SDA-AIE-213).

The project focuses on building a structured, testable application around a model interface, using fictional service data.

## Features

**Day 1 — Foundations**
- Arabic and English service responses.
- A mock client with predefined responses.
- Input validation and response metadata.
- Basic tests for expected responses and invalid input.
- JSON output for test results.

## Project Structure

```text
notebooks/
    murshid.ipynb
results/
    core/
        baseline_results.json
```

## Getting Started

1. Open `notebooks/murshid.ipynb` in Google Colab.
2. Select Python 3 with CPU runtime.
3. Run the cells in order.
4. Review the test results.

The core project uses Python's standard library and requires no external API keys.

## Notes

Murshid Mini currently uses a mock client rather than a real language model. All service information is fictional, and the application does not perform real transactions.

## Acknowledgment

Developed during the LLM Application Engineering course at SDAIA Academy, instructed by Meaad Al-Marri.
