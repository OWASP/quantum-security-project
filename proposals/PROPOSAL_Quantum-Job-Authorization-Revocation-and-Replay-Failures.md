## Proposal: Quantum Job Authorization, Revocation, and Replay Failures

**Surface:** Platform — quantum platforms and hybrid quantum-classical systems.

**Evidence:** Theoretical attack class supported by documented platform conditions. This proposal does not claim a production incident, CVE, or quantum-specific proof of concept involving expired, revoked, replayed, over-scoped, or multiply consumed job-specific authority.

**Description:**

Official quantum-platform documentation establishes lifecycle conditions including asynchronous provider queues, state-dependent cancellation and completion behavior, target- and shot-specific requests, creation-time spending controls that account for queued work, session semantics under which previously submitted jobs may continue, and request-level idempotency mechanisms. These sources establish the operating conditions on which this risk model depends; they do not establish that any named platform is vulnerable.

Even if a quantum job remains correctly linked to its submitted artifact, executes on the advertised hardware, and returns an authentic result, it may still be accepted, altered in scope, or repeated outside the authority granted for that exact logical job.

A logical quantum job may cross tenant, broker, provider, queue, and device boundaries. Material job-specific constraints may include the approved workload, principal, delegate, provider, target device, shot count, cost ceiling, purpose, timing condition, delegation scope, and permitted execution count. A security failure arises when authority constraints required by policy are absent or unsatisfied yet the relevant lifecycle transition is permitted, or when ambiguity or inconsistent enforcement in declared authority semantics permits behavior outside the intended authority at submission, broker acceptance, provider acceptance, dispatch, retry, cancellation, or completion.

Authentication identifies the actor. Account-level authorization may permit use of the platform. Neither, by itself, defines or consumes authority for this exact logical job.

This entry does not require every queued job to be reauthorized immediately before physical execution. A platform may legitimately treat successful submission or provider acceptance as durable authority to complete a job. Delayed execution, state-dependent cancellation, and durable acceptance are not vulnerabilities by themselves. The risk arises when declared scope, validity, delegation, revocation, cancellation, single-use, or retry semantics are promised or relied upon but are not enforced consistently at their stated lifecycle boundaries.

The underlying authorization and replay principles are not unique to quantum computing. Their quantum-platform manifestation is material because one logical workload may cross several administrative and execution boundaries; target selection affects execution characteristics and cost, while shot count affects cost and statistical precision; execution may occur materially after submission; and cancellation behavior may depend on the provider and current job state.

This entry concerns the authority state governing an exact logical job. It does not address compromised credentials or control-plane access, substitution or loss of integrity across job transformation and dispatch, or independent appraisal of claims about the underlying physical execution and returned result.

The same job parameter can participate in distinct failure conditions. For shot count, this entry asks whether the shot-count value at a lifecycle point governed by the applicable authorization policy—such as request, acceptance, or dispatch—conforms to the authorized value, range, or limit. Execution-and-result assurance asks whether independently appraisable evidence supports the claim that the stated number of shots was actually performed. Such evidence may help reveal some authorization violations, but support for an execution claim does not establish that the execution was authorized, and evidence of authorization does not establish that the claimed execution occurred.

**Common Examples of Vulnerability:**

1. A broker retries a quantum job after an acknowledgement timeout using a new downstream request identifier, causing two provider tasks to be accepted even though the principal authorized one logical execution.
2. A delegated operator has valid platform credentials but changes the approved target device, raises the requested shot count above the authorized maximum, or exceeds the approved cost ceiling because the system enforces account-level permission without enforcing a required job-specific authorization record.
3. Two dispatch workers concurrently observe the same one-time job authority as unused and both dispatch the workload before either records its consumption.
4. A broker reports a job as cancelled after recording the tenant's request but without obtaining or preserving the downstream provider's cancellation disposition.
5. A validity condition is expressly defined against provider acceptance or dispatch but is evaluated only when the tenant first submits the job.
6. A broker or reseller forwards a technically valid workload without preserving required constraints concerning the principal, delegate, target, shot count, cost, purpose, or permitted execution count.
7. Idempotency is enforced separately within individual API boundaries, but no durable logical-job identity or deduplication state links the tenant, broker, and provider requests.

**How to Prevent:**

1. Define the authority lifecycle for each job class. Specify whether authority becomes effective at tenant submission, broker acceptance, provider acceptance, dispatch, physical execution, or more than one of those points, and define the effect of later expiry, revocation, or cancellation.
2. Bind job-specific authority to an integrity-protected representation of the logical job and its material constraints, including the principal, submitting actor or delegate, provider, target device, shot count, cost ceiling, purpose, validity semantics, delegation scope, permitted execution count, and applicable policy version.
3. Distinguish logical-job identity from transport- or API-request identity. Maintain durable cross-boundary deduplication state for the logical job, map each local request identifier or idempotency token to that identity, and reject reuse for a materially different workload or authorization record.
4. Enforce one-time or limited-use authority through an atomic single-winner state transition, such as a conditional write, compare-and-consume operation, transaction, or equivalent mechanism. Do not rely on a separate read-then-write sequence that permits concurrent consumption.
5. Record cancellation and revocation as stateful dispositions rather than undifferentiated events. Distinguish requested, accepted, pending, too late, unsupported, cancelled, rejected, and completed despite request, and preserve which party produced each status.
6. Re-evaluate authority at every lifecycle point where the declared policy requires current evidence. Block the relevant transition or require renewed authorization when required evidence is absent, expired, revoked, already consumed, mismatched, or outside scope.
7. Do not silently reinterpret intentionally durable accepted-work semantics as requiring retroactive cancellation. Where accepted jobs are intended to survive later account, budget, session, or authorization changes, document that behavior and communicate it before acceptance.
8. Retain an auditable decision record identifying the logical job, evaluated authority, policy version, enforcement point, material scope, consumption count, retry lineage, cancellation disposition, assertion producer, and applicable trust boundary.
9. Test negative and concurrent cases, including a target selection or shot-count value outside the authorized scope, reuse of request identifiers, duplicate broker retries, simultaneous consumption attempts, cancellation races, late provider acceptance, and loss of delegation constraints across intermediaries.

**Example Attack Scenarios:**

Scenario #1: A broker submits a quantum workload to an underlying provider but times out before receiving an acknowledgement. It retries using a new downstream request identifier. The principal authorized one execution, but deduplication is local to each API boundary and is not associated with the same logical-job identity across the broker-provider chain. Both tasks are accepted, executed, and billed.

Scenario #2: A delegated operator holds valid credentials that permit quantum-task submission. The principal approved one identified workload on Backend A with a maximum shot count and cost ceiling. The operator changes the target to a more expensive device and raises the requested shot count above the approved maximum. Because the system evaluates account-level permission but does not enforce the required job-specific authorization record, the altered task is accepted despite falling outside the approval.

Scenario #3: A one-time job authorization is visible to two dispatch workers. Each checks the shared record before either worker commits a consumption update, and each observes the authority as unused. Both dispatch the workload, resulting in two executions and two charges under authority intended for a single use.

Scenario #4: A tenant requests cancellation of a queued job through a broker. The broker records the request as effective and reports the job as cancelled without obtaining the downstream provider's disposition. The provider had already accepted the job and later executes it. The failure is not that cancellation became too late; it is that an unconfirmed request was represented as an effective cancellation and the downstream cancellation disposition was neither confirmed nor preserved.

**Reference Links:**

<!-- References verified 2026-07-28 against authoritative platform documentation and IETF standards. Platform documentation establishes lifecycle conditions, not a vulnerability, incident, or quantum-specific proof of concept. -->

1. [Microsoft Azure Quantum — Introduction to jobs](https://learn.microsoft.com/en-us/azure/quantum/how-to-work-with-jobs): Documents the provider queue and state-dependent cancellation behavior, including cancellation of waiting jobs and provider-dependent support once execution has begun.
2. [Microsoft Azure Quantum — Jobs: Cancel REST API](https://learn.microsoft.com/en-us/rest/api/azurequantum/dataplane/jobs/cancel?view=rest-azurequantum-dataplane-2026-01-15-preview): Defines distinct job states including queued, waiting, executing, cancellation requested, cancelling, finishing, completed, and cancelled.
3. [Amazon Braket — CreateQuantumTask](https://docs.aws.amazon.com/braket/latest/APIReference/API_CreateQuantumTask.html): Documents required request attributes including a client token, target device ARN, and shot count.
4. [Amazon Braket — CreateJob](https://docs.aws.amazon.com/braket/latest/APIReference/API_CreateJob.html): Documents a required client token that guarantees idempotency for hybrid-job creation and a required QPU or simulator device configuration.
5. [Amazon Braket — Cost tracking and saving](https://docs.aws.amazon.com/braket/latest/developerguide/braket-pricing.html): Documents optional per-device spending controls evaluated at task creation and accounting for current and queued spend when a spending limit is updated.
6. [IBM Quantum — Execution modes FAQs](https://quantum.cloud.ibm.com/docs/guides/execution-modes-faq): Distinguishes closing a session, under which existing jobs continue to completion, from cancelling a session, which cancels pending jobs.
7. [IETF RFC 9396 — OAuth 2.0 Rich Authorization Requests](https://www.rfc-editor.org/rfc/rfc9396.html): Defines fine-grained authorization details beyond coarse-grained scope, including actions, locations, identifiers, and API-specific transaction constraints.
8. [IETF RFC 9449 — OAuth 2.0 Demonstrating Proof of Possession](https://www.rfc-editor.org/rfc/rfc9449.html): Defines request-bound proof-of-possession and replay-detection mechanisms, including unique proof identifiers, request method and URI binding, time limits, nonce use, and the practical difficulty of strict single-use checks where multiple servers lack shared state.

**Standards and Regulatory Mapping:**

The references reviewed for this proposal do not identify a quantum-specific standard defining the authority lifecycle for an exact logical job across tenant, broker, provider, queue, and device boundaries.

RFC 9396 provides a generic model for expressing fine-grained authorization requirements beyond account-level or coarse-scope access. RFC 9449 provides generic request-binding and replay-detection mechanisms and discusses the operational limits of strict single-use enforcement in distributed systems. These RFCs are architectural anchors and are not presented as quantum-specific attack evidence.

Current quantum-platform documentation establishes that jobs may be queued, cancellation and completion behavior can depend on state, target device and shot count are material request attributes, request-level idempotency exists for some job types, spending controls account for queued work, and previously submitted jobs may continue under defined session semantics. These documented behaviors are not vulnerabilities by themselves. They demonstrate why platforms and intermediaries need explicit and consistently enforced semantics for job-specific scope, effectiveness, cancellation, consumption, retry, and replay.
