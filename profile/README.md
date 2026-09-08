![ZIFFER. Keep the intelligence. Remove the authority.](https://raw.githubusercontent.com/ziffer-hq/.github/main/profile/banner.png)

ZIFFER is agent authorization: it sits between your AI agents and everything they touch, and it is built for the security or compliance owner who has been asked to approve an agent rollout.

## The problem

Your agents read email, tickets, documents and web pages that nobody controls.
A sentence inside any of them becomes an instruction, and the model has no way to tell it apart from yours.
Assume the injection works: OWASP LLM01:2025 records that no fool-proof prevention of prompt injection is known, and the UK NCSC wrote on 2025-12-10 that it may never close the way SQL injection did.
The question left on your desk is not whether an agent gets injected. It is what an injected agent can do.

## How it works

Five steps. The agent holds no credential at any of them.

### 1. Propose

Every action starts as a proposal. The agent holds one ZIFFER key that can ask,
not act, and the proposal names an action from your catalog.

- Through MCP, your catalog is the agent's toolset. An action outside the
  catalog has no form the agent could send.
- One SDK call works too, and so does plain HTTPS. Your agent, model, framework
  and prompts do not change.
- Least privilege you can show: there is no credential in the agent for an
  auditor to review.

### 2. Grant

Your policy grades each proposal and grants, holds or refuses it. The rule
decides, not the model.

- Granted when your rules grade the action LOW. Routine actions clear in
  milliseconds.
- Held for a quorum when your rules grade the action HIGH. The quorum is two
  named humans, 2-of-2, and each approver signs the exact bytes that will run.
  Silence is never consent.
- Refused by default when no rule exists. An action nobody wrote a rule for is
  refused, never graded.

### 3. Execute

Only ZIFFER runs a granted action, and it re-issues that action with its own
credential.

- Your systems accept actions only from ZIFFER's identity: one scoped
  credential per system.
- Your auditor can test the boundary. Send the same call around ZIFFER and
  watch it fail. That is a wall, not an alert.
- What was granted is exactly what ran. ZIFFER recomputes the action at
  execution and never trusts the message.

### 4. Prove

Everything that ran leaves a signed receipt, anchored externally.

- The receipt records what the agent read before it proposed the action, so a
  poisoned document is visible as the origin. That is provenance over actions.
- Receipts carry hybrid post-quantum signatures, Ed25519 with ML-DSA-65.
- Nobody can change a record once it is written, not us and not your own admin.
  Your auditor verifies non-repudiation with a public CLI, without production
  access.

### 5. Distribute

Rules are signed offline. Publishing ends in a signing ceremony, not a Save
button.

- No runtime component holds a key that can produce a valid policy signature,
  so a fully compromised ZIFFER still cannot rewrite the rules.
- The policy engine, the KMS and the executor each verify the bundle
  independently, on every read.
- Epochs only increase, so a rollback to yesterday's looser rules is refused.

## Guarantees

Six properties. Four carry a published clause, printed here as the standard
prints it. Two hold by construction, which means no clause enforces them: the
architecture leaves no other outcome available.

- **Zero credentials in the agent.** Holds by construction. Not vaulted, not
  brokered, not short-lived. The agent never touches a secret, so nothing can
  leak into a context window.
- **Provenance on every action.** Holds by construction. Every receipt records
  what the agent read before it proposed the action, so a poisoned document is
  visible as the origin.
- **Refusal by default.** Clause `8.4-3 · P-4`. An action with no rule is
  refused, never graded. Unknown risk is never LOW.
- **Consent is explicit and bound.** Clause `AC-3(2)`, dual authorization. The
  quorum is two named humans, each one signs the exact bytes that will run, and
  the proposer cannot approve their own action.
- **Receipts, not logs.** Clauses `AU-10 · AU-3`. Every action carries a
  KMS-signed receipt. It binds the action, the rules that graded it and the
  humans who signed.
- **History nobody can rewrite.** Clause `AU-9(3)`. The ledger only appends,
  anchored externally before an irreversible action releases.

## The open specification

ZIFFER is built on an open specification, and the specification is published
with the evidence about it. The repository ships four things: the specification
text, machine-checked proofs, a reference implementation, and public
conformance and attack suites.

Every claim replays on your machine:

```bash
./tools/verify.sh --suites
```

That gate runs the proofs, the suites and the harness. It needs no key from us,
and it is green at every commit. The mutation suites are the ones worth
reading: each security check is deleted in turn and the matching attack has to
succeed, which is how you know the check does something and the test is not
vacuous.

The licence, as that repository's README states it: Apache-2.0. © 2026 Code75
SASU, Yacine Kellib.

Repository: https://github.com/yacine-kellib/agent-control-plane

## What we do not claim

We do not filter prompts, and we do not prevent injection. The agent can be
manipulated from end to end and still change nothing, because it holds nothing.
That is the whole claim, and it is narrower than it sounds.

Nobody independent has checked us yet. No third party has run an adversarial
review, and we would rather write that here than let you discover it. The
specification repository names that review as its largest open gap.

What you can check today without us: the open specification, the reference
implementation under Apache 2.0, the attack suites that replay on your laptop
with each control deleted in turn, and the residual-risk file we published
before the claims.

## Links

- Site: https://ziffer.io
- Docs: https://ziffer.io/docs
- Blog: https://ziffer.io/blog
- The open specification: https://github.com/yacine-kellib/agent-control-plane
- A sign-off review, thirty minutes, by email:
  [hello@ziffer.io](mailto:hello@ziffer.io?subject=Sign-off%20review)

Keep the intelligence. Remove the authority.
