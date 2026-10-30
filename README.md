# ATLAS

A local-first, context-aware AI engineering and personal assistant for macOS.

ATLAS is an open-source project aimed at developing a proactive, personalized AI assistant that understands its user’s projects, workflows, preferences, and working environment.

Rather than functioning as a conventional chatbot, ATLAS is designed to maintain persistent context, reason about objectives, anticipate the consequences of actions, and learn from experience.

The goal is to create an assistant that works alongside its user, combining efficient AI models, structured memory, predictive reasoning, and deterministic engineering tools to support complex tasks across applications.

### Key Capabilities

ATLAS is being designed around the following capabilities:

* **Contextual Awareness:** Understand active applications, projects, documents, and workflows through resource-efficient, event-driven monitoring and incremental change detection.
* **Persistent Memory:** Retain important knowledge, decisions, and preferences through hierarchical memory, contextual retrieval, and progressive information consolidation.
* **Predictive Reasoning:** Decompose objectives into manageable subgoals, predict action outcomes, verify results, and adapt plans when observations differ from expectations.
* **Proactive Assistance:** Identify potential issues, recognize missing information, suggest improvements, and provide timely notifications.
* **Engineering Integration:** Assist with electronic design, CAD, scientific data analysis, and software development through application-specific tools and deterministic verification.
* **Natural Interaction:** Support continuous, interruptible voice conversations, natural-language commands through Raycast, and native macOS interfaces.
* **Personal Productivity:** Organize communications, meetings, notes, schedules, and tasks into actionable summaries.
* **Reversible Actions:** Maintain checkpoints and version history to support safe automation and straightforward recovery.
* **Adaptive AI:** Select appropriate local models and tools based on task complexity, available resources, and privacy requirements, with optional authorized external delegation.

### Design Principles

ATLAS follows several core principles:

1. **Local First:** Prioritize local processing, storage, privacy, and user control.
2. **Human in Control:** Support human decision-making rather than replace it, particularly in engineering applications.
3. **Efficient by Design:** Minimize unnecessary computation, memory consumption, storage, and model inference.
4. **Predict Before Acting:** Establish intended outcomes, evaluate possible actions, and verify results against expectations.
5. **Reason Under Uncertainty:** Recognize missing information, distinguish facts from assumptions, and seek clarification when necessary.
6. **Learn Selectively:** Preserve useful knowledge, causal relationships, and source references without accumulating redundant information.
7. **Act Responsibly:** Prefer reversible operations, transparent permissions, and recoverable changes.
8. **Remain Adaptable:** Support modular integrations, interchangeable AI models, and evolving user workflows.

### System Architecture

ATLAS is designed as a modular system consisting of:

| Component | Responsibility |
| --- | --- |
| ATLAS Core |	System orchestration and coordination |
Interaction Engine |	Raycast, continuous voice interaction, notifications, and conversation management
Context Engine |	Event-driven monitoring and incremental information extraction
Workspace State Model |	Structured representation of the current working environment
Memory Engine |	Hierarchical memory, consolidation, retrieval, and learning
Predictive Action Planner |	Goal decomposition, consequence prediction, and adaptive planning
Action & Verification Engine |	Controlled execution, outcome verification, and corrective feedback
Model Manager |	Resource-aware selection and coordination of AI models
Integration Layer |	Communication with applications, services, and engineering tools
Security & Recovery |	Permissions, privacy, checkpoints, and rollback
Runtime Manager |	Resource monitoring, scheduling, and system reliability

These components represent logical responsibilities and do not necessarily require separate processes.

The primary host is an Apple Silicon Mac mini, with support planned for remote macOS clients over secure private networking.

ATLAS is intended to operate locally by default, with optional, explicitly authorized delegation to external AI services.

### Development Status

**Status: Early development — architecture and planning**

The project is currently focused on establishing its technical specifications, software architecture, development environment, and implementation roadmap.

The initial development priorities are:

1. Local ATLAS Core and resource-aware model management.
2. Raycast integration and natural-language interaction.
3. Persistent memory and structured workspace context.
4. Predictive planning, tool execution, and verification.
5. Email, calendar, and productivity integrations.
6. Native macOS interfaces and continuous voice interaction.
7. Engineering application integrations and proactive assistance.

Features described in this README represent development goals and are not yet implemented.

### Documentation

The project’s functional and technical requirements are maintained in:

[ATLAS Design Specification v0.2](./docs/ATLAS_Design_Specification_v0.2.xlsx)

Additional architecture decisions, component interfaces, security specifications, and development plans will be maintained in the [docs/](./docs/) directory as the project progresses.

Development instructions for AI coding agents are provided in AGENTS.md.

### Privacy and Security

ATLAS is designed to keep personal information, project context, and persistent memory under the user’s control.

The public repository will contain source code, documentation, and synthetic examples only. Credentials, personal communications, private memories, and confidential project data must remain outside version control.

External AI services and data sharing require explicit authorization.

### Project Vision

An AI assistant that does not simply respond to instructions, but understands the context behind them, anticipates the consequences of its actions, learns from experience, and helps its user work more effectively.

⸻

_ATLAS is an independent, experimental open-source project under active development._