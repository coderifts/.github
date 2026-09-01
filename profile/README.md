# CodeRifts

**Signed, offline-verifiable authorization for AI-agent contract changes.**
Without a valid receipt, the change does not cross the execution boundary.

Most tools tell you what changed. CodeRifts tells you what may proceed — and proves it: every verdict is an Ed25519-signed receipt that anyone can verify offline, with an explicit statement of what the proof does and does not establish.

## The execution boundaries

A receipt is only worth something if a target requires it. These verifiers run on your side, hold no CodeRifts credentials, and fail closed.

| Boundary | Verifier | What it does without a valid receipt |
|---|---|---|
| GitHub merge | [contract-gate](https://github.com/coderifts/contract-gate) | Fails the required check |
| Agent tool registry | [agent-guard](https://github.com/coderifts/agent-guard) | The mutating tool is never created |
| Kubernetes admission | [k8s-admission](https://github.com/coderifts/k8s-admission) | Denies the admission (`failurePolicy: Fail`) |
| API gateway | [gateway-verifier](https://github.com/coderifts/gateway-verifier) | Rejects the request |

Each verifier vendors the verification core from receipt-verifier and pins its SHA-256, so you can check which core it runs. A refusal carries a machine-readable next step ([deny-remedy.v1](https://coderifts.com/schemas/deny-remedy.v1.json)), so an agent that is refused knows what to request.

## Verify a receipt yourself

- [receipt-verifier](https://github.com/coderifts/receipt-verifier) — JavaScript, zero dependencies
- [python-verifier](https://github.com/coderifts/python-verifier) — Python, byte-exact to the JavaScript verifier on shared test vectors

Receipts export to DSSE / in-toto, so existing supply-chain tooling can consume them. The format is specified in [RECEIPT_FORMAT.md](https://github.com/coderifts/receipt-verifier/blob/main/RECEIPT_FORMAT.md).

## Run it

- MCP server: `https://app.coderifts.com/mcp` — three tools: `preflight_change_set`, `verify_receipt`, `get_decision_details`
- Agent-setup files for your repository: `curl https://app.coderifts.com/snippets`
- Discovery: [`/.well-known/coderifts.json`](https://app.coderifts.com/.well-known/coderifts.json)
- Conformance suite: `npx @coderifts/conformance --subject reference`
- Reference executor and data-plane demo: [capability-demo](https://github.com/coderifts/capability-demo)

## What this does not prove

A receipt proves that a specific change set was authorized for a specific operation, and the verifiers above prove that a target refused to proceed without one. It does not prove that every path to a target is guarded — that depends on what you deploy, and each verifier states its own boundary. Our benchmark publishes its [definition and schema](https://coderifts.com/benchmark/north-star.json) before any numbers, and marks what is not yet measured.

[coderifts.com](https://coderifts.com) · [Documentation](https://coderifts.com/docs/) · [Decision Spec](https://coderifts.com/decision-spec/)
