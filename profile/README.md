![ZIFFER. Keep the intelligence. Remove the authority.](https://raw.githubusercontent.com/ziffer-hq/.github/main/profile/banner.png)

ZIFFER is agent authorization: it sits between your AI agents and everything they touch, and it is built for the security or compliance owner who has been asked to approve an agent rollout.

## The problem

Your agents read email, tickets, documents and web pages that nobody controls.
A sentence inside any of them becomes an instruction, and the model has no way to tell it apart from yours.
Assume the injection works: OWASP LLM01:2025 records that no fool-proof prevention of prompt injection is known, and the UK NCSC wrote on 2025-12-10 that it may never close the way SQL injection did.
The question left on your desk is not whether an agent gets injected. It is what an injected agent can do.

## Where this bites

In each case an agent proposes something consequential and nothing between
the model and the effect can refuse.

| Setting | The action an agent takes | What goes wrong with nothing in between | What ZIFFER does |
|---|---|---|---|
| **Cloud and infrastructure ops** | Modify a firewall rule, rotate a key, terminate instances, apply infrastructure as code | A poisoned ticket or log line becomes a production change. The agent had the credential, so the change is "authorised". | The risk of the action comes from your signed policy, not from the request. A firewall change on the production database is HIGH: two named humans sign it or it does not run. |
| **Finance and payments** | Release a payment, change payee details, approve an invoice | Invoice-fraud text in a PDF the agent summarises redirects a transfer. No human ever saw the change. | An irreversible action needs a positive acknowledgement from someone other than the operator, signed and bound to that exact payment. Silence is never consent. |
| **Research and lab automation** | Order a synthesis, book instrument time, release a dataset to a partner | Cross-program disclosure to a competitor. It cannot be recalled; the damage is instant and permanent. | Releasing to a partner is HIGH and irreversible by policy. A quorum is required, and the model's request is only ever a proposal. |
| **Customer support and CRM** | Issue a refund, delete an account, export a customer list | A customer message containing instructions gets treated as an instruction. Mass action at machine speed. | Limits count executions, not decisions, and the agent's capability is checked again at execution time, not only when it asked. |
| **Software delivery** | Merge, deploy, publish a package, rotate a secret | A comment in a dependency README triggers a release. Supply chain, one step removed. | The executor recomputes the risk and rehashes the artifact. Approval covers the exact bytes deployed, not a similar request. |
| **Healthcare and clinical** | Amend a record, submit to a regulator, release trial data | Regulated data integrity failure; the audit trail is rewritten after the fact. | The audit chain is anchored externally before the action releases. A rewrite after the anchor is detectable, never silent. |
| **Legal and contracts** | Send a signed document, accept terms, file with a court | Disclosure and commitment are both irreversible. | Same class as a partner release: irreversible means a mandatory acknowledgement, bound to the action and usable once. |
| **Any MCP or tool-calling deployment** | Whatever the server exposes | The model's output is the control signal. Tool poisoning or context poisoning becomes execution. | The model's only tool is propose. Every action is a typed proposal through one door, and the door can say no. |

The model is not the problem in any of these. The authorisation is. When the
credential is the authorisation, a manipulated agent is an authorised agent.

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

The executor runs on your side, and it runs a granted action only on a
verified receipt.

- ZIFFER holds none of your credentials, your data or your keys. The
  executor keeps them, in your environment, and refuses to act without a
  receipt that verifies against the policy you signed.
- Your auditor can test the boundary. Send the same call without a receipt
  and watch the executor refuse. That is a wall, not an alert.
- What was granted is exactly what ran. The executor recomputes the action
  from the receipt's bytes and never trusts the message.

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

## Threat model

We assume the worst about every part that can be talked to, and we assume any
single component can be compromised. The design holds anyway, or it says where
it does not.

**What we assume.** The model is manipulated. Prompt injection succeeds, every
time, and the agent proposes exactly what the attacker wants. Beyond the model,
any one of the policy engine, the executor, the signing service or the approval
screen may be compromised. No single one of them is trusted to be right on its
own: a decision is checked again by the party that acts on it.

**What the design answers.** In plain words, with the full table of threat ids
and countermeasures in section 4 of the specification.

| Threat | What stops it |
|---|---|
| Injected instructions in anything the agent reads | The agent can only propose. A proposal outside your action catalog has no form the agent could send. |
| A valid-looking action with harmful parameters | Risk is graded on the parameters, not only on the action name. |
| Many small actions that add up to one large one | Session accumulators count what was executed, and a limit refuses the next one. |
| A compromised policy engine | Every decision is a signed receipt, and the executor verifies it before acting. |
| A compromised executor | It holds no signing key and cannot mint a receipt. Without one, your systems refuse it. |
| Tampered or rolled-back policy | The policy bundle is signed with your own key and served by epoch. An epoch only increases; yesterday's looser rules are refused. |
| A tampered audit trail | Records are hash-chained and anchored externally before an irreversible action releases. |
| A replayed receipt or approval | Every receipt carries a nonce and an expiry, and a ledger consumes each one once. |
| A lie on the approval screen | The approver signs the bytes that will run, and an independently rendered summary reaches the same humans on a second path before release. |
| Approver fatigue by flooding | Held actions are queued apart from routine ones, and a flood is measured rather than absorbed. |

**What we do not cover.** An action that is valid in form and harmful in the
domain, such as a legitimate-looking trade or a real molecule, is not detected
here; domain screening is a separate layer. Availability is not guaranteed.
Compromise below the container boundary is out of scope. Exactly-once
execution against a system that is not idempotent is not achievable without
that system's help, and the specification says what is achievable instead.

## The specification

The specification is public: the decision rules, the wire schemas and the
conformance vectors, at https://github.com/ziffer-hq/ziffer-spec, under the
ZIFFER Specification Licence. Read it, copy it verbatim, implement it in
software that interoperates with ZIFFER.

The repository is generated from the engine at a named commit, and
`PROVENANCE.json` lists every file with its hash. The engine, the reference
implementation and the test evidence are not published: what your auditor
needs is the receipt format and a verifier, and both are yours to run without
us.

Versions through v1.3.19 were published under the name ACP under Apache-2.0.
Nothing since is. ZIFFER is a registered trademark of code75 SASU.

## What we do not claim

We do not filter prompts, and we do not prevent injection. The agent can be
manipulated from end to end and still change nothing, because it holds nothing.
That is the whole claim, and it is narrower than it sounds.

Nobody independent has checked us yet. No third party has run an adversarial
review, and we would rather write that here than let you discover it.

What you can check today without us: the specification, its schemas and
vectors, and any receipt ZIFFER issued, verified with the published format
against the policy you signed.

## Links

- Site: https://ziffer.io
- Docs: https://ziffer.io/docs
- Blog: https://ziffer.io/blog
- The specification: https://github.com/ziffer-hq/ziffer-spec
- A sign-off review, thirty minutes, by email:
  [hello@ziffer.io](mailto:hello@ziffer.io?subject=Sign-off%20review)

Keep the intelligence. Remove the authority.
