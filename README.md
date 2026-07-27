# Decision Receipt — public reference

A **Decision Receipt** is a cryptographically signed (Ed25519), replayable, independently verifiable record of why an automated decision was allowed, revised, blocked, or escalated. This repository is the public-facing reference for that artifact: the shapes you send, the shapes you get back, and how to verify one without trusting us.

- Live service — <https://decrec.summitcognitive.ai>
- Full documentation — <https://docs.summitcognitive.ai>
- API reference — <https://docs.summitcognitive.ai/api/reference>

> **Authoritative source.** The machine-readable OpenAPI document served at `GET /v1/openapi.json` is authoritative. The schemas in this repository are a human-readable mirror of the published reference, kept here so integrators can read and diff them without a key. Where the two disagree, the served spec wins — please open an issue.

## Contents

| Path | What it is |
| --- | --- |
| [`schemas/claim-input.schema.json`](schemas/claim-input.schema.json) | The request body accepted by `POST /v1/evaluate` and `POST /v1/simulate`. |
| [`schemas/evaluate-response.schema.json`](schemas/evaluate-response.schema.json) | The envelope returned by `POST /v1/evaluate`: policy verdict, replay determination, and receipt. |
| [`examples/`](examples) | Worked request and response documents. |

## The shape of a decision

A receipt binds four things into one tamper-evident artifact, chained to the receipt before it:

1. **Evidence** — typed, provenanced sources: CI results, code review, test output, human approval, model traces. At least one source is required.
2. **Policy** — the rules evaluated and the verdict they produced: `ALLOWED`, `BLOCKED`, or `ESCALATED`.
3. **Replay** — whether the same inputs deterministically reproduce the same outcome, and the replay hash that demonstrates it.
4. **Attestation** — the Ed25519 signature and hash chain.

Admissibility status is reported separately from the policy verdict: `ACCEPTED`, `NON_DETERMINISTIC`, or `REJECTED`. A decision can be *authorized* by policy and still fail to be *admissible* — for example, when it does not replay deterministically.

## Verifying a receipt offline

```bash
# Fetch the Ed25519 public key (PEM, no auth required)
curl -s https://decrec.summitcognitive.ai/v1/keys/server

# Or check a receipt against the live verifier
curl -s https://decrec.summitcognitive.ai/v1/verify \
  -H 'Content-Type: application/json' \
  -d @examples/evaluate-response.json
```

`POST /v1/verify` requires no API key. That is deliberate: a record you can only check by asking the issuer's permission is not independently verifiable.

## What a passing verification does and does not mean

A receipt that verifies is proof **about the record** — that the inputs, the policy and the outcome are what they claim to be, unaltered and in order.

It is **not** a warranty that the model's answer was correct. Correctness remains a human judgement. What the receipt changes is that the judgement becomes reviewable instead of unreviewable.

## Contributing

Issues and pull requests are welcome, particularly:

- discrepancies between these schemas and the served OpenAPI document,
- ambiguous or under-specified fields,
- verification recipes for languages not yet covered.

## License

Documentation and schemas in this repository are published under [CC BY 4.0](LICENSE). The Summit Cognitive platform itself is proprietary.

---

<sub>Maintained by <a href="https://github.com/Summit-Cognitive">Summit Cognitive</a> · <a href="https://summitcognitive.ai">summitcognitive.ai</a> · brian@summitcognitive.ai</sub>
