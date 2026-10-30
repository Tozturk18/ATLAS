# ADR-0001: Local-First Modular Monolith

**Status:** Accepted  
**Date:** 2026-10-30  
**Decision Makers:** Project Owner  
**Requirements Baseline:** ATLAS Design Specification v0.2  
**Related Documentation:** `docs/architecture/SYSTEM_ARCHITECTURE.md`

---

## 1. Context

ATLAS is a local-first, context-aware AI engineering and personal assistant for macOS.

The system is intended to operate continuously, maintain persistent contextual memory, interact with local applications, coordinate AI models and deterministic tools, and execute authorized actions with appropriate verification and recovery.

Its architecture must support several competing objectives:

- **Privacy:** Keep personal, professional, and research information under the user's control.
- **Efficiency:** Operate reliably on resource-constrained Apple Silicon hardware.
- **Responsiveness:** Support low-latency interaction while performing background operations.
- **Reliability:** Preserve data integrity and recover from interrupted or unsuccessful operations.
- **Modularity:** Allow individual components to evolve without requiring major architectural changes.
- **Extensibility:** Support additional applications, models, interfaces, and engineering workflows.
- **Maintainability:** Keep development, testing, deployment, and debugging manageable for a small project.
- **Offline Operation:** Preserve useful functionality without continuous internet connectivity.

The initial deployment environment consists of an Apple M4 Mac mini with 16 GB of unified memory and a 2 TB SSD, alongside a MacBook Pro acting as a remote client.

The Mac mini is intended to host ATLAS's authoritative persistent state, core orchestration, and supported local inference.

The MacBook Pro will provide user interfaces and access to authorized applications running locally on that device.

ATLAS also requires logical separation between several major responsibilities:

1. User interaction.
2. Context collection.
3. Workspace state representation.
4. Persistent memory.
5. Predictive planning.
6. Action execution.
7. Outcome verification.
8. AI model management.
9. Application integrations.
10. Security, runtime management, and recovery.

The architectural challenge is to maintain these boundaries without introducing unnecessary processes, network services, deployment complexity, or resource overhead.

---

## 2. Decision

**ATLAS will adopt a local-first modular monolith as its initial core architecture.**

The Mac mini will serve as the authoritative host for ATLAS Core and persistent system state.

Core components will initially be organized as logically independent modules within a limited number of processes, communicating primarily through explicit internal interfaces.

Separate processes or services will be introduced only when justified by platform requirements, security boundaries, fault isolation, resource management, or measured performance.

Remote clients will communicate with the authoritative host through authenticated interfaces over secure private networking.

External AI services will remain optional and subject to explicit authorization.

### 2.1 Local-First Operation

ATLAS will prioritize local execution for:

- Core orchestration.
- Persistent memory.
- Workspace state management.
- Predictive planning.
- Tool coordination.
- Supported AI inference.
- Context processing.
- Recovery and checkpoint management.

Local-first does not mean local-only.

External services may be used when they provide necessary capabilities or measurable benefits and the user has authorized their use.

Supported local operations should remain functional without internet access.

### 2.2 Modular Monolith

The initial implementation will maintain explicit boundaries between architectural components without requiring each component to run as a separate service.

For example, the Memory Engine and Predictive Action Planner may operate within the same ATLAS Core process while exposing separate internal interfaces.

Components should not access one another's internal state or storage structures without an established interface.

The architecture should support future extraction of individual components into separate processes when justified.

### 2.3 Authoritative Host

The Mac mini will own the authoritative persistent ATLAS state.

Remote clients may maintain temporary caches and local interaction state.

Persistent updates must pass through controlled interfaces that enforce authorization, consistency, and validation.

Actively written databases must not be synchronized between devices through ordinary file synchronization.

### 2.4 Remote Clients

Remote macOS clients will communicate with the Mac mini through authenticated interfaces.

The initial private-network approach will use the existing Tailscale infrastructure.

Client-side adapters may execute authorized operations on applications and files available only on the client device.

The authoritative host will coordinate these operations without assuming unrestricted access to remote applications.

### 2.5 Selective Process Isolation

The modular monolith decision does not require every operation to execute inside one process.

Separate processes may be appropriate for:

- Local AI inference.
- Audio processing.
- Native macOS integrations.
- Application automation.
- Untrusted document parsing.
- Operations requiring distinct permissions.
- Potentially unstable third-party dependencies.

These boundaries should be introduced based on demonstrated needs rather than by default.

---

## 3. Decision Drivers

The decision is based on the following considerations.

### 3.1 Resource Efficiency

The initial Mac mini has 16 GB of unified memory.

Running numerous independent services would introduce additional memory consumption, scheduling overhead, and operational complexity.

A modular monolith allows ATLAS to share infrastructure and coordinate resource usage more efficiently.

### 3.2 Development Complexity

ATLAS is an evolving project with substantial architectural experimentation ahead.

A modular monolith allows components to be implemented, tested, and modified without requiring distributed deployment infrastructure.

This reduces the cost of early architectural changes.

### 3.3 Local Data Control

Centralizing authoritative state on the Mac mini simplifies data ownership, access control, backups, and consistency.

It also reduces the need to duplicate sensitive information across devices or external services.

### 3.4 Low-Latency Communication

Internal module calls generally have less communication overhead than network-based service calls.

This is particularly useful for interactions involving context retrieval, planning, and verification.

### 3.5 Reliability and Recovery

A centralized orchestration layer simplifies coordination between state-changing operations, checkpoints, and verification.

Explicit module boundaries still allow components to be tested independently.

### 3.6 Future Extensibility

The architecture must accommodate additional models, applications, interfaces, and engineering tools.

Modularity is necessary, but independently deployed services are not required to achieve it.

### 3.7 Privacy and Security

A local-first architecture reduces unnecessary external data transmission.

Centralized authorization can provide consistent enforcement across integrations and tools.

However, local execution does not automatically guarantee security; authentication, access controls, process permissions, and data protection remain necessary.

---

## 4. Alternatives Considered

### 4.1 Distributed Microservices

**Description:** Implement major ATLAS components as independently deployed services communicating through network protocols.

**Advantages:**

- Independent deployment and scaling.
- Stronger process isolation by default.
- Independent technology stacks.
- Potentially easier distribution across multiple computers.

**Disadvantages:**

- Higher operational complexity.
- Additional communication overhead.
- More difficult debugging.
- More complex failure handling.
- Additional authentication and service-management requirements.
- Potentially greater resource consumption.

**Decision:** Rejected for the initial implementation.

Microservices would introduce complexity that is not currently justified by ATLAS's scale, hardware, or development requirements.

Individual components may be extracted into services later if measurable benefits justify the change.

### 4.2 Single Tightly Coupled Application

**Description:** Implement all functionality within one application without strong internal component boundaries.

**Advantages:**

- Simple initial implementation.
- Minimal communication overhead.
- Limited deployment complexity.

**Disadvantages:**

- Increasingly difficult maintenance.
- Poor separation of responsibilities.
- Harder testing.
- Greater risk of unintended interactions.
- Difficult replacement of AI models and integrations.
- More expensive architectural changes.

**Decision:** Rejected.

Although a single application may be efficient, ATLAS requires explicit modular boundaries to support long-term development.

### 4.3 Cloud-First Architecture

**Description:** Host the primary reasoning, memory, and orchestration services on external infrastructure.

**Advantages:**

- Access to more powerful computing resources.
- Easier access from multiple devices.
- Potentially simpler remote availability.
- Reduced dependence on local hardware performance.

**Disadvantages:**

- Recurring infrastructure costs.
- Dependence on network connectivity.
- Additional privacy and confidentiality concerns.
- Greater external data exposure.
- Potential vendor dependence.
- Less direct control over persistent information.

**Decision:** Rejected as the default architecture.

Cloud services may supplement ATLAS when explicitly authorized, but they will not be required for its core local functionality.

### 4.4 Fully Decentralized Peer-to-Peer Architecture

**Description:** Allow each device to maintain independent authoritative state and synchronize changes between peers.

**Advantages:**

- Reduced dependence on one host.
- Potentially better offline operation on each device.
- Flexible distribution of computation.

**Disadvantages:**

- Complex state reconciliation.
- Increased risk of conflicting updates.
- Difficult synchronization of memory and task state.
- More complicated permissions.
- Greater recovery complexity.

**Decision:** Rejected for the initial implementation.

A single authoritative host provides a simpler and more reliable starting point.

Remote clients may still perform local operations and retain limited temporary state.

---

## 5. Consequences

### 5.1 Positive Consequences

**Simpler development:** Components can be developed and tested without distributed infrastructure.

**Lower resource overhead:** Shared infrastructure reduces unnecessary duplication.

**Centralized state:** Persistent memory, task history, and permissions can be managed consistently.

**Reduced network dependence:** Most core operations remain local.

**Clear component boundaries:** Individual modules can evolve without requiring a microservices architecture.

**Improved traceability:** Planning, execution, verification, and recovery can be coordinated through one authoritative system.

**Flexible AI integration:** Local and authorized external models can be managed behind common interfaces.

### 5.2 Negative Consequences

**Host dependence:** Availability of authoritative ATLAS services depends on the Mac mini.

**Limited independent scaling:** Components cannot be scaled independently without additional architectural changes.

**Shared-process failure risk:** A failure in one in-process component may affect other components.

**Resource contention:** AI inference and background tasks may compete for the same hardware resources.

**Potential coupling:** Poorly enforced module boundaries could cause the modular monolith to become difficult to maintain.

**Remote latency:** Client interactions depend on the quality and availability of the connection to the Mac mini.

These limitations are accepted for the initial implementation.

### 5.3 Mitigations

The architecture will mitigate these risks through:

- Explicit component interfaces.
- Centralized resource scheduling.
- Fault isolation where justified.
- Persistent task state.
- Controlled database access.
- Checkpoints and recovery.
- Graceful handling of disconnected clients.
- Automated testing.
- Performance monitoring.
- Architecture decision reviews.

---

## 6. Implementation Constraints

The following constraints apply to implementations governed by this ADR.

### 6.1 Component Independence

Components must expose explicit interfaces.

Avoid direct dependencies on another component's private implementation details.

### 6.2 Storage Ownership

Persistent state must have clearly defined ownership.

Clients and integrations should not independently modify authoritative database files.

### 6.3 No Unnecessary Services

Do not introduce independent services, containers, or message brokers without a documented technical justification.

### 6.4 Local Processing Preference

Prefer local processing when it satisfies the task's functional, performance, and reliability requirements.

### 6.5 Controlled External Delegation

External AI services require appropriate authorization and data minimization.

### 6.6 Replaceable Models

AI models and inference runtimes must be replaceable without redesigning the ATLAS Core.

### 6.7 Application-Specific Execution

Operations requiring access to a particular device or application should execute through an authorized adapter on the appropriate device.

### 6.8 Resource Awareness

Background tasks and model inference must respect configurable resource budgets.

### 6.9 Recoverability

Supported state-changing operations must integrate with the applicable checkpoint, verification, and recovery mechanisms.

### 6.10 Architectural Traceability

Significant deviations from this decision require a new ADR or an explicit revision to this record.

---

## 7. Validation Criteria

This decision should be evaluated during implementation.

The modular monolith approach will be considered successful if ATLAS can:

1. Run its authoritative core services reliably on the target Mac mini.
2. Support local inference within approved resource budgets.
3. Serve authenticated remote macOS clients.
4. Maintain persistent state consistently across sessions.
5. Execute and verify supported multi-step tasks.
6. Isolate unstable or privileged operations when necessary.
7. Recover from supported failures without corrupting authoritative state.
8. Add or replace major integrations without extensive changes to unrelated components.
9. Preserve useful offline functionality.
10. Meet applicable performance and acceptance criteria in the approved design specification.

These are validation objectives, not claims of completed functionality.

Quantitative performance targets should be evaluated against the approved requirements and measured on the intended hardware.

---

## 8. Conditions for Reconsideration

This decision should be reconsidered if implementation evidence demonstrates that the modular monolith no longer satisfies ATLAS's requirements.

Potential triggers include:

- Persistent resource contention that cannot be resolved through scheduling or process isolation.
- A component requiring independent deployment or scaling.
- Reliability problems caused by shared-process failures.
- Security requirements demanding stronger isolation.
- Multiple authoritative hosts becoming necessary.
- Substantial performance improvements from distributing computation.
- Operational complexity becoming greater than that of a more distributed architecture.
- New hardware or deployment requirements that materially change the system's constraints.

A reconsideration should compare measured costs and benefits rather than relying solely on architectural preference.

Any replacement architecture must preserve ATLAS's local-first principles unless those principles are explicitly revised through an approved decision.

---

## 9. Related Decisions

The following topics should be addressed in separate ADRs as development progresses:

- Core programming language and framework.
- Persistent database and memory indexing.
- Local AI inference runtime.
- Client-server communication and authentication.
- Internal event distribution.
- Voice processing architecture.
- Application integration boundaries.
- Checkpoint storage and recovery.
- Model selection and resource scheduling.

These decisions should remain independently revisable where possible.

---

## 10. Final Decision

ATLAS will begin development using a **local-first modular monolith**, with the Mac mini acting as the authoritative host for core orchestration and persistent state.

Components will communicate primarily through explicit internal interfaces, while remote clients and isolated processes will use authenticated communication mechanisms.

The architecture will prioritize resource efficiency, maintainability, privacy, reliability, and recoverability.

Distributed services and additional process boundaries may be introduced when justified by measurable technical requirements.

**The objective is to preserve the simplicity of a unified local system without sacrificing the modularity required for ATLAS to evolve.**