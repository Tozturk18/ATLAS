# ATLAS — System Architecture

**Version:** 0.1  
**Status:** Proposed architecture  
**Requirements baseline:** ATLAS Design Specification v0.2  
**Primary platform:** macOS / Apple Silicon  
**Development approach:** Local-first, modular, incremental

---

## 1. Purpose

This document defines the proposed high-level software architecture of ATLAS, a local-first, context-aware AI engineering and personal assistant for macOS.

ATLAS is designed to operate as a persistent, resource-efficient system that understands the user's working environment, maintains relevant memory, plans actions, predicts their consequences, and verifies outcomes.

The architecture combines:

- Deterministic software and application-specific tools.
- Efficient local AI models.
- Structured representations of the working environment.
- Hierarchical and reconstructive memory.
- Predictive planning and feedback-driven execution.
- Continuous, interruptible conversational interaction.
- Secure, reversible automation.

The objective is not to create a conventional chatbot with access to external tools.

**ATLAS should function as an integrated engineering assistant that observes, reasons, acts, verifies, and learns.**

This document describes the intended architecture. It does not imply that the components have already been implemented.

---

## 2. Architectural Principles

ATLAS follows eight primary architectural principles.

### 2.1 Local-First Operation

Core reasoning, memory, orchestration, and supported application integrations should operate locally.

External services may supplement local capabilities when explicitly authorized.

The system should preserve useful offline functionality and should not depend on continuous internet connectivity for routine operations.

### 2.2 Deterministic Tools Before AI Inference

When a task can be completed reliably using deterministic software, ATLAS should prefer that approach over probabilistic model inference.

Examples include:

- Filesystem monitoring.
- Content hashing.
- Version comparison.
- Numerical calculations.
- Database queries.
- Circuit connectivity checks.
- CAD geometry operations.
- Engineering rule validation.

AI models should coordinate, interpret, and supplement these tools.

They should not replace reliable deterministic functionality without a demonstrated benefit.

### 2.3 Predictive, Feedback-Driven Execution

For complex or consequential operations, ATLAS should define the desired outcome before executing actions.

The system should predict expected changes, perform authorized operations, observe the resulting state, and compare actual outcomes with expectations.

Unexpected results should trigger investigation, replanning, recovery, or escalation.

### 2.4 Resource-Aware Intelligence

ATLAS should select the least computationally expensive approach that reliably satisfies the task.

This includes choosing between:

- Deterministic operations.
- Cached results.
- Structured retrieval.
- Small specialized models.
- General-purpose local language models.
- More capable reasoning models.
- Authorized external services.

Resource efficiency must be considered throughout the system rather than added as a later optimization.

### 2.5 Modular Architecture

ATLAS should be organized into components with explicit responsibilities and interfaces.

The initial implementation should favor a modular monolith rather than independent microservices.

Separate processes should be introduced when justified by security, platform constraints, fault isolation, or resource management.

### 2.6 Reconstructive Memory

ATLAS should preserve useful knowledge through structured records and progressively consolidated summaries.

Original documents, engineering files, and experimental data should remain authoritative.

When detailed information is needed, ATLAS should retrieve it from the original source rather than relying exclusively on summaries.

### 2.7 Human Authority

The user retains control over consequential actions and engineering decisions.

ATLAS may perform approved, low-risk operations autonomously.

Sensitive, destructive, externally visible, or otherwise consequential operations must respect explicit authorization policies.

### 2.8 Observable and Recoverable Behavior

State-changing operations should be traceable and recoverable.

ATLAS should distinguish:

- Intended actions.
- Authorized actions.
- Executed actions.
- Observed outcomes.
- Verified results.
- Unresolved failures.

The system must not equate successful tool execution with successful completion of the user's objective.

---

## 3. Deployment Architecture

### 3.1 Primary Host

The primary ATLAS host is an Apple Silicon Mac mini.

The initial target hardware is:

| Property | Configuration |
|---|---|
| Computer | Apple Mac mini |
| Processor | Apple M4 |
| Unified memory | 16 GB |
| Storage | 2 TB SSD |
| Operating system | macOS |
| Primary role | Persistent ATLAS host |

The Mac mini should host the authoritative ATLAS Core, persistent memory, orchestration services, and supported local AI inference.

The architecture must accommodate the hardware's limited unified memory.

Large AI models should not remain loaded unnecessarily.

### 3.2 Remote Client

A MacBook Pro serves as a remote ATLAS client.

The client should communicate with the Mac mini through an authenticated interface over a secure private network, initially using the user's existing Tailscale configuration.

The remote client may provide:

- Raycast commands.
- Native macOS interfaces.
- Notifications.
- Voice interaction.
- Local application context.
- User-approved tool execution.

Application-specific operations that require access to the MacBook Pro's local environment should execute through an authorized client-side adapter.

The Mac mini should not assume it can directly access or control every application running on the remote computer.

### 3.3 Logical Deployment Diagram

```text
┌───────────────────────────────────────────────────────┐
│                    MACBOOK PRO                        │
│                                                       │
│  Raycast / Native UI / Voice                          │
│                 │                                     │
│                 ▼                                     │
│        ATLAS Client Adapter                           │
│                 │                                     │
│     Local Application Integrations                    │
└─────────────────┼─────────────────────────────────────┘
                  │
          Authenticated Connection
             (Private Network)
                  │
                  ▼
┌───────────────────────────────────────────────────────┐
│                      MAC MINI                         │
│                                                       │
│  ┌─────────────────────────────────────────────────┐  │
│  │                  ATLAS CORE                     │  │
│  │                                                 │  │
│  │  Orchestration / Planning / Verification        │  │
│  └─────────────────────────────────────────────────┘  │
│                       │                               │
│          ┌────────────┼────────────┐                  │
│          ▼            ▼            ▼                  │
│      Context       Memory        Model                │
│      Engine        Engine        Manager              │
│          │            │            │                  │
│          └────────────┼────────────┘                  │
│                       ▼                               │
│              Integration Layer                        │
│                       │                               │
│          Local Tools / Authorized APIs                │
│                                                       │
│     Persistent Storage / Recovery / Diagnostics       │
└───────────────────────────────────────────────────────┘
```

The diagram represents logical responsibilities rather than a required process layout.

### 3.4 Deployment Authority

The Mac mini is the authoritative host for persistent ATLAS state.

Client devices may maintain temporary caches and local interaction state.

Conflicting updates must be reconciled through explicit application logic rather than uncontrolled filesystem synchronization.

An actively written SQLite database must not be synchronized through Syncthing.

---

## 4. High-Level Software Architecture

ATLAS consists of a central orchestration layer supported by specialized components.

### 4.1 Architectural Components

| Component | Primary responsibility |
|---|---|
| ATLAS Core | Coordinate requests, tasks, state transitions, and component interactions |
| Interaction Engine | Manage user commands, conversations, voice, and notifications |
| Context Engine | Observe relevant changes in applications and files |
| Workspace State Model | Maintain a structured representation of the current environment |
| Memory Engine | Store, retrieve, consolidate, and reconstruct knowledge |
| Predictive Action Planner | Define objectives, decompose tasks, and predict outcomes |
| Action Engine | Execute authorized operations through controlled tools |
| Verification Engine | Compare expected outcomes with observed results |
| Model Manager | Select and coordinate appropriate AI models |
| Integration Layer | Connect ATLAS to applications, services, and engineering tools |
| Security and Permissions | Enforce authentication, authorization, and privacy boundaries |
| Runtime Manager | Manage resource budgets, scheduling, and reliability |
| Recovery System | Maintain checkpoints, version history, and rollback capabilities |

These components should initially be implemented as modules within a limited number of processes.

The architecture should avoid introducing a separate service for every logical component.

### 4.2 Central Orchestration

ATLAS Core coordinates interactions between components.

Its responsibilities include:

1. Receiving normalized user requests.
2. Determining the relevant context.
3. Retrieving required memory.
4. Selecting an execution strategy.
5. Invoking planning when appropriate.
6. Enforcing authorization requirements.
7. Dispatching actions.
8. Collecting observations.
9. Evaluating completion.
10. Updating relevant state and memory.

ATLAS Core should not contain application-specific business logic that belongs in an integration adapter.

It should coordinate specialized components through explicit interfaces.

---

## 5. Interaction Engine

The Interaction Engine manages communication between the user and ATLAS.

Supported interfaces are planned to include:

- Raycast.
- Native macOS interfaces.
- Voice interaction.
- Notifications.
- Morning briefings.
- Future companion interfaces.

### 5.1 Raycast

Raycast is the initial primary command interface.

The intended interaction pattern is:

`atlas <natural-language request>`

The Raycast extension should act as a lightweight client.

It should forward normalized requests to ATLAS Core and display the returned results.

It should not independently maintain a second authoritative memory database or duplicate core planning logic.

### 5.2 Conversation Manager

The Conversation Manager maintains dialogue state across successive interactions.

The initial state model includes:

| State | Description |
|---|---|
| Dormant | Waiting for explicit activation |
| Listening | Receiving an active user utterance |
| Responding | Generating or delivering a response |
| Follow-up | Accepting contextually related speech without repeated wake-word activation |

State transitions should be explicit and testable.

The Follow-up state should use a configurable timeout.

After the timeout, the conversation may return to Dormant while retaining relevant session context.

### 5.3 Addressee Detection

ATLAS must not assume that all speech detected during a follow-up window is directed at the assistant.

The Conversation Manager should combine available signals, including:

- Wake-word detection.
- Speaker cues.
- Turn-taking behavior.
- Conversational context.
- Explicit addressing.
- Environmental uncertainty.

A recognized speaker is not necessarily addressing ATLAS.

When addressee confidence is insufficient, ATLAS should avoid initiating actions.

For consequential operations, ambiguous speech must not be treated as authorization.

Addressee detection should be evaluated using realistic multi-speaker scenarios.

### 5.4 Interruptible Speech

The voice subsystem should support interruption of synthesized speech.

The audio pipeline should provide:

- Voice activity detection.
- Streaming speech recognition.
- Echo cancellation where supported.
- Streaming speech synthesis.
- Speech playback cancellation.
- Conversation-state updates.

The speech pipeline should remain sufficiently independent of the reasoning engine to detect interruptions while a response is being generated or spoken.

### 5.5 Speech and Task Cancellation

Interrupting speech and cancelling an operation are separate behaviors.

A speech interruption may immediately stop audio playback.

A task cancellation must follow the Action Engine's cancellation policy and respect safe execution boundaries.

The Interaction Engine should communicate cancellation requests without directly terminating state-changing operations.

### 5.6 Voice Profile

The intended default voice is calm, professional, concise, and naturally conversational, with a restrained British English accent.

Speech synthesis and conversational behavior should be configurable independently.

The architecture must not depend on imitating a specific individual.

---

## 6. Context Engine

The Context Engine observes changes in the user's working environment.

Its purpose is to provide relevant information without continuously analyzing every application or file.

### 6.1 Context Sources

Potential sources include:

- Filesystem events.
- Application APIs.
- Document metadata.
- Active application state.
- Version-control events.
- Structured engineering data.
- Authorized account integrations.
- Selective visual observations.

Each source should be accessed through an appropriate adapter.

### 6.2 Event-Driven Monitoring

ATLAS should prefer event-driven monitoring over continuous polling.

On macOS, filesystem monitoring may use supported operating-system mechanisms such as FSEvents.

Application-specific integrations should use native change notifications when available.

Polling may be used when necessary, but should have explicit scheduling and resource limits.

### 6.3 Incremental Change Detection

The Context Engine should avoid repeatedly processing unchanged content.

A proposed change-detection pipeline is:

```text
Application or File Event
           │
           ▼
Identify Affected Resource
           │
           ▼
Debounce / Coalesce Events
           │
           ▼
Check Revision or Content Hash
           │
           ▼
Determine Changed Portions
           │
           ▼
Extract Semantic Changes
           │
           ▼
Update Workspace State
           │
           ▼
Notify Relevant Components
```

Where supported, change detection should operate at the smallest reliable semantic unit.

Examples include:

- Modified document sections.
- Changed source-code functions.
- Updated schematic components.
- Altered PCB nets.
- Modified CAD parameters.
- Newly added experimental measurements.

### 6.4 Git and Version Comparison

Git may provide version history and differences for supported project files.

However, Git should not serve as the sole real-time monitoring mechanism.

ATLAS should combine filesystem events, content hashing, incremental parsing, and application-specific semantic comparison.

Version-control checkpoints should remain distinct from context-monitoring events.

### 6.5 Semantic Change Adapters

Application adapters may implement specialized comparison methods.

For example, a KiCad adapter could compare structured schematic representations and identify changes to:

- Symbols.
- Properties.
- Connections.
- Nets.
- Footprints.
- Geometry.

The adapter should report meaningful changes rather than relying exclusively on textual differences.

### 6.6 Reliability and Reconciliation

Filesystem notifications can be delayed, duplicated, or missed.

The Context Engine should support:

- Event deduplication.
- Debouncing.
- Revision tracking.
- Reconciliation after restart.
- Recovery from event overflow.
- Periodic consistency checks when justified.

When incremental comparison is unreliable, the system should fall back to a complete comparison or reparse.

Correctness takes precedence over avoiding every redundant computation.

---

## 7. Workspace State Model

The Workspace State Model maintains ATLAS's current understanding of the active working environment.

It is distinct from long-term memory.

### 7.1 Purpose

The Workspace State Model answers questions such as:

- Which project is active?
- Which application is being used?
- Which documents are open?
- What task is currently underway?
- What constraints are relevant?
- Which actions are pending?
- What information remains uncertain?

### 7.2 State Representation

State should be represented through structured, typed records.

A conceptual representation is:

```json
{
  "workspace_id": "workspace-example",
  "active_project": "example-project",
  "active_application": "KiCad",
  "active_document": "main.kicad_sch",
  "current_goal": "Integrate temperature sensing",
  "known_constraints": [],
  "pending_actions": [],
  "unresolved_questions": [],
  "last_observed_at": null
}
```

This example is illustrative, not a finalized schema.

The actual schema should support:

- Stable identifiers.
- Source references.
- Timestamps.
- State revisions.
- Verification status.
- Confidence or uncertainty metadata.
- Device and project scope.

### 7.3 State Updates

State should be updated incrementally from relevant events.

ATLAS should avoid reconstructing the entire workspace representation after every minor change.

When multiple sources disagree, the system should preserve the discrepancy and determine which source is authoritative.

### 7.4 State and Memory Separation

Workspace state represents the current environment.

Memory represents relevant historical information.

A temporary application selection should not automatically become permanent memory.

A durable engineering decision should not disappear merely because the application is closed.

---

## 8. Memory Engine

The Memory Engine provides persistent contextual knowledge across interactions and projects.

ATLAS uses a reconstructive, hierarchical memory strategy.

### 8.1 Memory Hierarchy

The proposed memory hierarchy includes five levels.

| Level | Content | Purpose |
|---|---|---|
| L1 — Working Context | Immediate interaction and task state | Fast reasoning |
| L2 — Recent Events | Meaningful recent observations and actions | Short-term continuity |
| L3 — Session Summaries | Consolidated task and conversation history | Efficient retrieval |
| L4 — Long-Term Knowledge | Decisions, preferences, constraints, and relationships | Persistent understanding |
| L5 — Source References | Links to original documents and data | Detailed reconstruction |

These levels are logical categories.

They do not necessarily require separate databases.

### 8.2 Memory Records

Memory records should distinguish:

- Observations.
- Confirmed facts.
- User preferences.
- Engineering decisions.
- Inferences.
- Hypotheses.
- Corrections.
- Unresolved questions.

Records should include appropriate provenance, scope, and timestamps.

### 8.3 Temporal Memory

ATLAS should retain a compact history of meaningful changes.

Rather than storing every observable event indefinitely, the system should progressively consolidate related observations.

A typical progression is:

```text
Raw Events
    │
    ▼
Recent Activity Buffer
    │
    ▼
Session Summary
    │
    ▼
Project Knowledge
    │
    ▼
Long-Term Memory
```

Consolidation should preserve information necessary to understand important decisions and reconstruct relevant details.

### 8.4 Causal Provenance

Memory should preserve why important decisions were made.

For example, a component selection record should ideally include:

- Selected component.
- Relevant constraints.
- Decision rationale.
- Supporting documentation.
- Applicable project.
- Verification status.

The system must distinguish evidence-supported explanations from AI-generated hypotheses.

### 8.5 Prediction Error Memory

Meaningful discrepancies between predicted and observed outcomes may be retained as learning records.

A prediction error record should identify:

1. The predicted outcome.
2. The executed action.
3. The observed outcome.
4. The discrepancy.
5. The proposed explanation.
6. The successful correction, if known.

Repeated failures should be consolidated into useful lessons when justified.

Unverified explanations must not automatically become permanent operating rules.

### 8.6 Retrieval

Retrieval should prioritize relevant information based on:

- Active task.
- Project.
- Source reliability.
- Recency.
- Semantic relevance.
- Explicit user preferences.
- Available context budget.

Structured filtering should be used when possible.

Semantic retrieval may supplement structured queries.

### 8.7 Source Reconstruction

Summaries must not replace original engineering files, research data, or documents.

When detailed information is required, ATLAS should retrieve the original source.

The Memory Engine should preserve sufficient references to support this reconstruction.

### 8.8 Initial Storage Strategy

SQLite is the proposed initial database for authoritative local metadata and structured memory.

The design should support:

- Transactions.
- Schema migrations.
- Referential integrity.
- Indexed retrieval.
- Backup and recovery.
- Concurrent access through a controlled service boundary.

Large files and binary artifacts should generally remain in appropriate filesystem storage rather than being indiscriminately embedded in the database.

Vector search may be introduced through a compatible local index when justified by retrieval requirements.

The final indexing strategy remains subject to benchmarking.

---

## 9. Predictive Action Planner

The Predictive Action Planner transforms user objectives into structured, verifiable plans.

### 9.1 Planning Inputs

The planner receives:

- User objective.
- Current workspace state.
- Relevant memory.
- Available tools.
- Known constraints.
- Authorization boundaries.
- Resource limits.
- Unresolved uncertainties.

### 9.2 Goal Representation

A goal should identify:

- Desired outcome.
- Initial state.
- Success conditions.
- Relevant constraints.
- Required permissions.
- Verification strategy.

For consequential operations, success conditions should be established before execution.

### 9.3 Hierarchical Planning

Complex goals should be decomposed into smaller subgoals.

Subgoals should track:

- Dependencies.
- Preconditions.
- Completion status.
- Expected outputs.
- Failure conditions.

Planning depth should depend on task complexity.

Routine operations should not require unnecessary multi-level planning.

### 9.4 Action Prediction

Before executing an action, ATLAS should determine:

- What is expected to change.
- What should remain unchanged.
- Which constraints must remain satisfied.
- How success will be measured.
- Which failures are plausible.

Deterministic simulation and application-native validation should be preferred when available.

### 9.5 Uncertainty-Aware Planning

The planner should identify missing information that materially affects the task.

Possible responses include:

- Retrieving additional context.
- Inspecting the active application.
- Consulting documentation.
- Running a deterministic check.
- Asking the user a targeted question.
- Deferring an unsafe operation.

The planner must not invent missing engineering constraints.

### 9.6 Planning Strategy

The default strategy should resemble a receding-horizon control loop:

```text
Define Objective
       │
       ▼
Observe Current State
       │
       ▼
Retrieve Relevant Knowledge
       │
       ▼
Generate Plan
       │
       ▼
Predict Expected Outcomes
       │
       ▼
Execute Next Authorized Action
       │
       ▼
Observe Actual Outcome
       │
       ▼
Verify Result
       │
       ├── Expected → Continue
       │
       ├── Unexpected → Replan
       │
       ├── Unsafe → Stop / Recover
       │
       └── Uncertain → Gather Evidence / Ask
```

The system should avoid executing an entire long plan without checking intermediate outcomes when those checks are meaningful.

### 9.7 Planning Limitations

The initial planner does not require a learned neural world model.

Structured state representations, deterministic tools, and feedback-driven planning should be implemented and evaluated first.

JEPA-inspired representations and learned world models remain research directions.

---

## 10. Action Engine

The Action Engine performs authorized operations through controlled interfaces.

### 10.1 Responsibilities

The Action Engine should:

- Validate tool inputs.
- Check permissions.
- Confirm preconditions.
- Create checkpoints when required.
- Execute operations.
- Report execution status.
- Support cancellation.
- Preserve recoverability.

### 10.2 Tool Interface

Each tool should expose a defined contract.

A tool contract should identify:

- Tool identifier.
- Supported operation.
- Input schema.
- Output schema.
- Required permissions.
- Expected side effects.
- Cancellation behavior.
- Recovery support.
- Verification mechanism.

Tool interfaces should be independent of the language model used to invoke them.

### 10.3 Action Classification

Actions should be classified according to risk and reversibility.

A proposed classification is:

| Category | Examples | Default behavior |
|---|---|---|
| Read-only | Inspect files, query metadata | Execute within granted permissions |
| Low-risk reversible | Organize temporary working data | Execute within approved policy |
| State-changing | Modify project files | Checkpoint and verify |
| Consequential | Send messages, publish, share data | Require appropriate authorization |
| Destructive | Permanently delete or overwrite critical data | Require explicit approval and recovery safeguards |

The final permission policy should be documented separately.

### 10.4 Cancellation

Operations should support cancellation where technically feasible.

Cancellation must respect safe execution boundaries.

For file modifications, prefer atomic writes, transactions, temporary files, or application-supported undo operations.

A cancellation request must not silently leave corrupted artifacts.

### 10.5 Idempotency

Where practical, operations should support idempotency or duplicate-execution detection.

Retries must not unintentionally repeat consequential side effects.

---

## 11. Verification Engine

The Verification Engine determines whether executed actions achieved their intended outcomes.

### 11.1 Verification Inputs

Verification should consider:

- Expected state.
- Observed state.
- Action results.
- Relevant constraints.
- Applicable acceptance criteria.
- Tool-generated diagnostics.

### 11.2 Verification Methods

Prefer deterministic verification whenever possible.

Examples include:

- File hash comparison.
- Schema validation.
- Unit tests.
- Engineering rule checks.
- Numerical tolerance checks.
- Application state inspection.
- Successful retrieval of generated artifacts.

Model-based interpretation may supplement these methods.

### 11.3 Discrepancy Handling

When expected and observed outcomes differ, the Verification Engine should produce a structured discrepancy report.

The planner may then:

- Retry with revised parameters.
- Select an alternative action.
- Request additional evidence.
- Restore a checkpoint.
- Ask for human intervention.

Repeated identical retries without new evidence should be avoided.

### 11.4 Verification Status

Results should distinguish:

- Executed.
- Observed.
- Verified.
- Partially verified.
- Failed.
- Unverified.

A successful API response does not automatically establish that the user's goal was achieved.

---

## 12. Model Manager

The Model Manager selects and coordinates AI models according to task requirements and available resources.

### 12.1 Model Categories

ATLAS may use:

- Small language models.
- General-purpose local language models.
- Embedding models.
- Speech recognition models.
- Speech synthesis models.
- Wake-word detection models.
- Specialized vision models.
- Authorized external reasoning services.

Not every category must be implemented initially.

### 12.2 Model Registry

Each configured model should have metadata describing:

- Model identifier.
- Runtime.
- Supported capabilities.
- Estimated memory requirements.
- Supported context length.
- Expected latency.
- Privacy classification.
- Resource requirements.
- Availability.

### 12.3 Model Selection

Model selection should consider:

1. Whether a deterministic solution is available.
2. Required capabilities.
3. Task complexity.
4. Latency requirements.
5. Memory availability.
6. Privacy restrictions.
7. Reliability.
8. Computational and monetary cost.

### 12.4 Fast and Deliberative Modes

Fast Mode prioritizes responsiveness for straightforward tasks.

Deliberative Mode allows more extensive planning and verification for complex tasks.

Mode selection should be configurable and testable.

A request for additional reasoning should not bypass authorization or safety requirements.

### 12.5 Model Lifecycle

The Model Manager should control model loading and unloading.

Avoid keeping multiple large models resident without justification.

Support resource-aware scheduling and appropriate caching.

### 12.6 Initial Model Runtime

A local inference runtime compatible with Apple Silicon should be selected through benchmarking.

Candidates may include runtimes supporting quantized models and Metal acceleration.

The architecture must not depend on a single model family or provider.

Specific model selections should be recorded separately from this architecture.

---

## 13. Integration Layer

The Integration Layer provides application-specific interfaces.

### 13.1 Integration Principles

Prefer:

1. Supported structured APIs.
2. Native application interfaces.
3. Document parsers.
4. Application automation interfaces.
5. Visual interaction when necessary.

Avoid brittle GUI automation when reliable structured alternatives exist.

### 13.2 Initial Integrations

Planned integrations include:

- Raycast.
- Local filesystem.
- Git.
- Email and calendar accounts.
- Notes and knowledge systems.
- KiCad.
- Codex.

Additional integrations should be introduced according to approved priorities.

### 13.3 Engineering Applications

Engineering adapters should expose structured domain operations.

For example, a KiCad adapter may provide:

- Project inspection.
- Schematic parsing.
- Component metadata.
- Net information.
- Electrical rule checks.
- PCB design-rule checks.
- Supported design modifications.
- Semantic change detection.

Capabilities must be limited to those supported by the installed application version and available interfaces.

### 13.4 Scientific Data

Scientific integrations should preserve:

- Raw measurements.
- Units.
- Acquisition parameters.
- Processing methods.
- Source provenance.
- Analysis outputs.

Derived results must remain distinguishable from original measurements.

---

## 14. Security and Permissions

Security controls should be enforced at system boundaries rather than relying solely on language-model instructions.

### 14.1 Authentication

Remote clients must authenticate before accessing protected services.

Use secure transport and appropriate credential storage.

### 14.2 Authorization

Tool execution must be subject to explicit permission checks.

Authorization should consider:

- User identity.
- Device.
- Tool.
- Requested operation.
- Data sensitivity.
- Potential side effects.

### 14.3 Data Boundaries

Distinguish:

- Public project information.
- Private personal information.
- Confidential professional information.
- Sensitive research data.
- Credentials and authentication material.

External transmission must respect applicable permissions.

### 14.4 Prompt Injection

Retrieved content must be treated as data, not as an authoritative instruction source.

Instructions found in emails, documents, websites, or tool outputs must not override approved system policies.

### 14.5 Runtime Instructions

ATLAS runtime instructions should be stored separately from development instructions.

The repository's `AGENTS.md` governs Codex development behavior.

It should not automatically become the runtime policy for the deployed assistant.

Runtime policy changes should support review, approval, versioning, and rollback.

---

## 15. Recovery System

The Recovery System protects user-controlled files and project state.

### 15.1 Local Checkpoints

The planned short-term checkpoint policy is:

- Retain checkpoints for up to five days.
- Retain up to twenty completed state-changing prompts per project.
- Expire checkpoints when either limit is exceeded.

These limits apply to managed recovery checkpoints, not original project files or research data.

### 15.2 Version Control

Git may provide long-term version history for authorized repositories.

Repository operations must respect privacy settings and user-approved synchronization policies.

Not every file type is suitable for Git-based recovery.

### 15.3 Recovery Workflow

Before a supported state-changing operation:

1. Identify affected resources.
2. Determine the recovery mechanism.
3. Create the required checkpoint.
4. Execute the operation.
5. Verify the result.
6. Record the final state.

Rollback should restore the intended prior state without overwriting unrelated changes.

---

## 16. Runtime Manager

The Runtime Manager coordinates background execution and resource usage.

### 16.1 Scheduling

Prioritize interactive tasks over nonessential background processing.

Examples of lower-priority tasks include:

- Memory consolidation.
- Background indexing.
- Periodic consistency checks.
- Optional model preloading.
- Nonurgent summarization.

### 16.2 Resource Budgets

The system should monitor:

- CPU utilization.
- Unified memory usage.
- Storage consumption.
- Inference latency.
- Background task duration.
- Model loading overhead.

Performance limits should be established through approved requirements and measured benchmarks.

### 16.3 Fault Isolation

Components with elevated failure risk or platform-specific requirements may operate in separate processes.

Examples include:

- Audio processing.
- Application automation.
- Model inference.
- Untrusted document parsing.

Process isolation should be introduced where its reliability or security benefits justify the overhead.

### 16.4 Reliability

ATLAS should handle:

- Temporary network disconnection.
- Unavailable models.
- Failed integrations.
- Interrupted tasks.
- Application restarts.
- Partial execution.
- Database recovery.

The system should fail safely and preserve useful state when possible.

---

## 17. Internal Communication

### 17.1 Communication Strategy

The initial architecture should favor direct, typed module interfaces within the ATLAS Core.

Remote clients and isolated processes should communicate through authenticated local or private-network interfaces.

### 17.2 API Design

A versioned local API is proposed for client communication.

The initial implementation may use HTTP with structured JSON payloads.

Streaming or bidirectional communication may be added for tasks requiring real-time updates, such as voice interaction and long-running operations.

The final transport selection should be recorded in an ADR.

### 17.3 Event Distribution

ATLAS should support internal event notifications for meaningful state changes.

Examples include:

- File modified.
- Workspace updated.
- Task started.
- Task completed.
- Action failed.
- Verification failed.
- Memory updated.
- User approval required.

A lightweight in-process event mechanism is preferred initially.

An external message broker should not be introduced without a demonstrated need.

### 17.4 Event Records

Events should include appropriate:

- Event identifiers.
- Event types.
- Timestamps.
- Source identifiers.
- Project scope.
- Correlation identifiers.
- Payload schemas.

Events containing sensitive information must follow privacy and retention policies.

---

## 18. Data Architecture

ATLAS should distinguish operational data from user-controlled source material.

### 18.1 Data Categories

| Category | Examples | Proposed storage |
|---|---|---|
| Configuration | Model settings, integration preferences | Local configuration |
| Operational state | Active tasks, session state | Controlled local database |
| Structured memory | Decisions, preferences, relationships | Local database |
| Event history | Meaningful observations and actions | Local database |
| Search indexes | Semantic or structured retrieval indexes | Local index |
| Recovery checkpoints | Previous file and task states | Managed local storage |
| Original source files | CAD, documents, experimental data | Existing user-controlled locations |
| Credentials | API tokens and secrets | Secure credential storage |

### 18.2 Database Ownership

The Mac mini should own the authoritative persistent database.

Remote clients must access persistent state through approved service interfaces.

### 18.3 Schema Evolution

Persistent schemas should support versioned migrations.

Migrations must preserve existing data and provide an appropriate recovery strategy.

### 18.4 Data Minimization

ATLAS should avoid retaining unnecessary copies of sensitive information.

Derived records should maintain references to their sources where practical.

---

## 19. Representative Workflows

### 19.1 Natural-Language Command

```text
User
  │
  ▼
Raycast / Voice
  │
  ▼
Interaction Engine
  │
  ▼
ATLAS Core
  │
  ├── Retrieve Workspace State
  │
  ├── Retrieve Relevant Memory
  │
  └── Select Execution Strategy
              │
              ▼
       Planner / Direct Tool
              │
              ▼
       Permission Check
              │
              ▼
         Action Engine
              │
              ▼
       Verification Engine
              │
              ▼
         ATLAS Core
              │
              ▼
         User Response
```

Simple read-only requests may bypass extensive planning.

### 19.2 Engineering Modification

```text
User Requests Design Change
              │
              ▼
       Inspect Project
              │
              ▼
       Retrieve Constraints
              │
              ▼
       Identify Goal State
              │
              ▼
       Generate Plan
              │
              ▼
       Predict Consequences
              │
              ▼
       Request Approval if Required
              │
              ▼
       Create Checkpoint
              │
              ▼
       Modify Design
              │
              ▼
       Run Native Engineering Checks
              │
              ▼
       Compare Expected / Actual State
              │
       ┌──────┴──────┐
       ▼             ▼
    Success       Mismatch
       │             │
       ▼             ▼
  Record Result   Replan / Recover
```

### 19.3 Incremental Context Update

```text
Application Change
       │
       ▼
Event Detection
       │
       ▼
Revision Comparison
       │
       ▼
Semantic Change Extraction
       │
       ▼
Workspace State Update
       │
       ▼
Relevant Memory / Task Update
```

### 19.4 Memory Consolidation

```text
Recent Events
       │
       ▼
Identify Meaningful Information
       │
       ▼
Group Related Observations
       │
       ▼
Generate Structured Summary
       │
       ▼
Preserve Source References
       │
       ▼
Validate Scope and Provenance
       │
       ▼
Store Derived Memory
       │
       ▼
Include Reviewable Changes
```

---

## 20. Initial Implementation Strategy

The architecture should be implemented incrementally.

### Milestone 1 — Local Foundation

Establish:

- Repository structure.
- Development environment.
- Configuration management.
- Logging.
- Local service.
- Basic API.
- Testing infrastructure.

**Acceptance objective:** A local ATLAS service can start reliably, respond to a basic health request, and shut down cleanly.

### Milestone 2 — Model Management

Implement:

- Model registry.
- Local inference adapter.
- Basic model selection.
- Resource monitoring.

**Acceptance objective:** ATLAS can execute a supported local language task through a replaceable model interface.

### Milestone 3 — Raycast Interaction

Implement:

- Raycast command interface.
- Client-to-core communication.
- Request and response handling.
- Basic task status reporting.

**Acceptance objective:** A user can submit a natural-language request through Raycast and receive a response from the local ATLAS service.

### Milestone 4 — Persistent Memory

Implement:

- Structured memory records.
- Persistent local storage.
- Basic retrieval.
- Session context.
- Source references.

**Acceptance objective:** ATLAS can retain approved information across restarts and retrieve it in a later interaction.

### Milestone 5 — Workspace Context

Implement:

- Basic filesystem monitoring.
- Incremental change detection.
- Workspace state records.
- Event handling.

**Acceptance objective:** ATLAS can identify supported file changes and update its workspace representation without repeatedly processing unchanged content.

### Milestone 6 — Predictive Actions

Implement:

- Structured goals.
- Basic subgoal planning.
- Action contracts.
- Expected outcomes.
- Verification.
- Safe cancellation.
- Initial checkpointing.

**Acceptance objective:** ATLAS can complete a controlled multi-step task, verify the result, and safely handle a failed or interrupted operation.

### Milestone 7 — Productivity Integrations

Implement approved email, calendar, and knowledge integrations.

**Acceptance objective:** ATLAS can retrieve authorized information and generate a contextual briefing without exposing unrelated private data.

### Milestone 8 — Voice Interaction

Implement:

- Wake-word activation.
- Speech recognition.
- Speech synthesis.
- Conversation state.
- Follow-up interaction.
- Speech interruption.
- Addressee gating.

**Acceptance objective:** ATLAS supports a continuous, interruptible conversation and does not execute consequential actions based on ambiguous speech.

### Milestone 9 — Engineering Integrations

Begin with supported KiCad operations.

**Acceptance objective:** ATLAS can inspect a supported engineering project, identify relevant changes, run available deterministic checks, and report verified findings.

These milestones define an implementation sequence, not a commitment to specific delivery dates.

---

## 21. Technology Decisions

The following technologies are proposed starting points.

| Area | Proposed approach | Status |
|---|---|---|
| Core architecture | Modular monolith | Proposed |
| Primary host | Apple Silicon Mac mini | Approved deployment target |
| Primary client | macOS / Raycast | Approved direction |
| Core implementation | Python | Proposed |
| Native macOS components | Swift | Proposed |
| Raycast extension | TypeScript | Proposed |
| Persistent metadata | SQLite | Proposed |
| Local inference | Replaceable Apple Silicon-compatible runtime | To benchmark |
| Client communication | Authenticated local HTTP API | Proposed |
| Real-time communication | Streaming protocol as needed | To evaluate |
| Filesystem monitoring | Native macOS events | Proposed |
| Source versioning | Git where appropriate | Approved direction |
| Private networking | Tailscale | Existing infrastructure |
| Memory retrieval | Structured queries with optional local semantic search | Proposed |
| Voice processing | Modular local speech pipeline | To evaluate |

Technology selections should be finalized through focused experiments and recorded in ADRs.

---

## 22. Research Directions

The architecture should remain compatible with future research into predictive representations and learned world models.

Potential research areas include:

- Joint Embedding Predictive Architectures.
- Hierarchical predictive representations.
- Action-conditioned world models.
- Learned state abstractions.
- Efficient multimodal context processing.
- Adaptive memory consolidation.

These approaches should be evaluated against simpler alternatives.

A research feature should only become a required production dependency when it demonstrates measurable improvements in reliability, efficiency, or task performance.

ATLAS should not require training a custom foundation model for its initial implementation.

---

## 23. Architectural Risks and Tradeoffs

### 23.1 Resource Constraints

A 16 GB unified-memory system limits the size and number of models that can remain active simultaneously.

Mitigation:

- Small and quantized models.
- Model lifecycle management.
- Deterministic alternatives.
- Resource-aware scheduling.
- Performance benchmarking.

### 23.2 Context Reliability

Application events may be incomplete or delayed.

Mitigation:

- Revision tracking.
- Incremental comparison.
- Reconciliation.
- Source verification.
- Explicit uncertainty representation.

### 23.3 Memory Errors

Summaries may omit important information or preserve incorrect interpretations.

Mitigation:

- Source provenance.
- Scoped memory.
- User review.
- Reconstructive retrieval.
- Confidence and verification metadata.

### 23.4 Planning Errors

AI-generated plans may contain invalid assumptions.

Mitigation:

- Structured preconditions.
- Deterministic validation.
- Intermediate verification.
- Uncertainty gates.
- Safe replanning.

### 23.5 Voice Ambiguity

ATLAS may incorrectly interpret speech directed at another person.

Mitigation:

- Wake-word support.
- Addressee detection.
- Conservative action gating.
- Explicit confirmation for consequential actions.
- Realistic multi-speaker testing.

### 23.6 Integration Fragility

Application APIs and file formats may change.

Mitigation:

- Versioned adapters.
- Capability detection.
- Compatibility tests.
- Explicit fallback behavior.

### 23.7 Architectural Complexity

Excessive modularization could increase development and maintenance costs.

Mitigation:

- Modular monolith.
- Minimal initial dependencies.
- Incremental implementation.
- Clear interface contracts.
- Evidence-based architectural changes.

---

## 24. Open Architectural Decisions

The following decisions require further evaluation:

1. Final core programming language and framework.
2. Local inference runtime and initial model selection.
3. Persistent memory schema and retrieval index.
4. Internal event distribution mechanism.
5. Client authentication and API transport.
6. Voice recognition and synthesis engines.
7. Follow-up timeout and addressee detection strategy.
8. Detailed permission and approval policy.
9. Recovery storage format and checkpoint implementation.
10. Application-specific semantic diff strategies.
11. Background scheduling and resource budgets.
12. Final performance targets and benchmark methodology.

Each significant decision should be documented through an ADR before implementation depends on it.

---

## 25. Architecture Validation

The architecture should be evaluated against the approved functional and technical requirements.

Validation should include:

- Requirement traceability.
- Component interface tests.
- Data integrity tests.
- Permission enforcement.
- Memory retrieval tests.
- Predictive planning tests.
- Recovery tests.
- Voice interaction tests.
- Resource benchmarks.
- End-to-end acceptance tests.

Performance claims must be based on measured results.

Architectural assumptions should be revised when implementation evidence contradicts them.

---

## 26. Summary

ATLAS is designed as a local-first, modular engineering assistant that combines structured environmental awareness, reconstructive memory, efficient AI inference, deterministic tools, and predictive action planning.

Its central operating loop is:

**Observe → Understand → Plan → Predict → Act → Verify → Learn**

The architecture prioritizes correctness, efficiency, privacy, recoverability, and human authority.

The initial implementation should establish these capabilities through simple, testable components rather than unnecessary infrastructure or speculative model training.

**The long-term objective is an assistant that understands the user's working environment, anticipates the consequences of its actions, recognizes uncertainty, and improves through verified experience.**