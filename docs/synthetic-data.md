# Synthetic Dataset — `synthetic-v1`

Murshid Mini uses a **small, hand-written teaching dataset**. The references, statuses, and users below are fictional. They are not taken from a government system or real users. No downloads, API keys, or personal records are required.

## Public service directory

| Language | Example question | Directory answer |
| --- | --- | --- |
| Arabic | `كيف أتابع طلبي؟` | يمكنك متابعة حالة طلبك باستخدام رقم المرجع. |
| English | `How can I check my application status?` | You can check your application status using its reference number. |

A separate Arabic example, `ساعدني`, receives a fixed clarification response. The core's `FakeClient` uses predetermined answers; it does not understand arbitrary phrasing or generate text using a trained model.

## Local application-status fixtures

| Reference | Fictional owner | Status |
| --- | --- | --- |
| `REF-1001` | `demo-user-1` | `under_review` |
| `REF-1002` | `demo-user-2` | `approved` |
| `REF-9999` | — | Does not exist |

The test session is `demo-user-1`. `check_status()` compares the record owner with that supplied session before returning status. `REF-1002` therefore produces `access_denied` in the core tests, without exposing `approved` in the outward answer. The identity is a **test fixture, not real authentication**.

## Structured ticket contract

`extract_ticket()` and `validate_ticket()` use exactly four fields:

| Field | Accepted value |
| --- | --- |
| `service` | `service_info`, `application_status`, or `other` |
| `city` | `Riyadh`, `Jeddah`, or `unknown` |
| `request_id` | `null` or `REF-` followed by four ASCII digits |
| `needs_clarification` | A Boolean (`true` or `false`) |

A missing reference for an application-status request requires clarification. Allowed schema values are not evidence that every city or every natural-language phrasing is recognized. The extraction is rule-based.

## Tests and boundaries

The ten Day 3 golden cases cover public FAQs, an authorized record, a missing record, a missing reference, an unsupported request, an unauthorized record, an instruction override, invalid input, and a malformed ticket. Day 4 reruns those cases after adding a public-FAQ cache; the cache never stores application-status records or user-specific responses.

The reference data is exported in [`results/core/synthetic_data.json`](../results/core/synthetic_data.json); the observed behavior and limits are documented in [`project-notes.md`](project-notes.md).

**Limitations:** These records do not establish production correctness, identity verification, real government access, or comprehensive prompt-injection resistance. Do not replace them with real personal data when reproducing the exercise.
