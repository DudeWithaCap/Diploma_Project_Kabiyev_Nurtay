# 7. Architecture Design

## 7.1. Architecture Overview

The Local AI Agent is designed to automate office tasks while processing organizational data within a controlled, locally deployed environment. Users interact with the application through a web interface and issue instructions in natural language. The system uses a locally hosted large language model (LLM) to interpret requests and select tools for working with documents, spreadsheets, presentations, PDF files, and email.

The architecture separates user interaction, agent orchestration, model inference, tool execution, and data storage. This separation supports maintainability, independent tool development, human approval of sensitive operations, and controlled access to confidential files. External AI APIs are not required for normal system operation.

## 7.2. System Context Diagram

![Figure 7.1 — System context diagram](/diagrams/SystemContextDiagram.png)

*Figure 7.1. System context and high-level data flows.*

The primary actor is the User, who submits requests, uploads files, reviews proposed actions, and receives results. The Local AI Agent is the central system. Its supporting environment includes a local LLM runtime, tool servers, a database, and file storage.

The diagram illustrates the following interactions:

- The User submits natural-language requests, office files, email instructions, and action approvals.
- The Local AI Agent sends prompts and context to the local LLM and receives text responses or proposed tool calls.
- The agent invokes dedicated MCP tools for Word, PPTX, Excel, CSV, email, and PDF operations.
- The database stores tasks, conversation history, approval decisions, audit records, and settings; the workspace stores uploaded and generated files, versions, and RAG-related indexes.
- Connections to external AI APIs are blocked by the intended network policy.

## 7.3. Use Case Diagram

![Figure 7.2 — Use case diagram](/diagrams/UseCaseDiagram.png)

*Figure 7.2. Use-case diagram.*

The system has one human actor, User. The actor can submit natural-language tasks, upload and manage files, analyze and modify supported office formats, work with email, review proposed changes, and retrieve outputs. Every user interacts with their own agent and data through the application interface.

describes the use case Edit Word Document, using a cross-functional activity/swimlane notation. It shows the order of responsibilities across User, Web UI / Gateway, Agent Core, Local LLM, Reliability / Policy, MCP Tool Registry, and Workspace / Audit.

1. The User uploads a document and specifies the requested changes.
2. The gateway receives the request, stores the file in the workspace, and the Agent Core creates a task.
3. The Local LLM proposes a document-reading action. The system validates the arguments, checks read permissions, and calls the Word MCP tool.
4. The agent receives the document content and requests proposed edits from the LLM.
5. The proposed edits are validated and displayed to the User as a comparison between original and revised content.
6. If the User approves, the Word tool applies the modification, saves a new version, and the system records the operation.
7. If the User rejects, the system records the rejection and leaves the document unchanged.

The important architectural property is human-in-the-loop control: the model does not directly modify files without passing through application-controlled validation, policy checks, and the approval process required for that operation.

## 7.4. Component Diagram

![Figure 7.3 — Component diagram](/diagrams/SystemComponentDiagram.png)

*Figure 7.3. Internal organization of the Local AI Agent.*

The application consists of the following major groups.

| Component | Responsibility |
|---|---|
| Web Gateway | Provides the browser-facing interface, authentication, task submission, approval UI, and audit visibility using FastAPI and web technologies. |
| Agent Core | Coordinates the `plan → call → observe` loop, task state, and execution limits. |
| LLM Adapter | Provides a common interface to the local LLM runtime, such as Ollama or vLLM. |
| Reliability Layer | Validates tool-call arguments and supports repair, retries, and constrained outputs. |
| Policy Engine | Evaluates operation risk and determines whether an action is permitted, denied, or requires approval. |
| Approvals Service | Presents proposed changes and handles user decisions. |
| Tool Registry | Maintains tool interfaces and invokes MCP servers. |
| RAG | Retrieves relevant information from locally stored documents. |
| MCP Tool Servers | Perform format-specific operations through separate Word, XLSX, Mail, PPTX, CSV, and PDF tools. |
| State and Storage | Stores workspace files, versions, task and approval state, audit information, and local indexes. |
| Egress Monitor | Helps verify that unauthorized outbound connections are blocked. |

The diagram separates orchestration from tool execution: the LLM proposes actions, but dedicated application code checks and executes them. MCP tool servers are presented as independently isolated execution units.


## 7.5. Data Storage and Confidentiality

The architecture separates structured application data from file storage. The database stores tasks, conversation history, approval decisions, and audit records. The local workspace stores source documents, generated artifacts, file versions, and data used by retrieval operations. The model accesses needed content through controlled application interfaces rather than through unrestricted access to organizational storage.

Confidentiality controls are distributed across the architecture. The LLM is hosted locally, tools are isolated from one another, and the Policy Engine checks proposed operations independently of the model. Document modification is subject to user approval as illustrated by the Word-editing workflow. Monitoring and network restrictions are intended to prevent unauthorized communication with external AI services.

The email tool requires an explicit deployment decision about its permitted mail server and network route. Blocking external AI APIs must not be confused with blocking all legitimate, approved mail delivery.

## 7.6. Alignment of Requirements, Components, Data, and Scenarios

| Architectural requirement or capability | Responsible elements | Data involved |
| --- | --- | --- |
| Natural-language task processing | Web Gateway, Agent Core, Local LLM | User prompts and task state |
| Office file processing | Tool Registry, MCP Tool Servers | DOCX, XLSX, PPTX, CSV, PDF files |
| Local execution without external AI APIs | Local LLM, network controls, Egress Monitor | Prompts, document content, generated outputs |
| Human review of sensitive actions | Policy Engine, Approvals Service, Web Gateway | Proposed changes, approval decisions |
| File persistence and versioning | Workspace / State and Storage | Uploaded files, results, versions |
| Traceable task execution | Agent Core, Audit and Storage | Task state, action history, audit records |
| Retrieval from local documents | RAG, Workspace, Local LLM | Document fragments and local indexes |

This traceability demonstrates that the user-facing functions in the system context and use-case views are supported by identifiable architectural components. The document-editing workflow further illustrates how those components cooperate to enforce user control during a concrete operation.

## 7.7. Architectural Decisions Requiring Validation

The diagrams describe the intended architecture rather than a verified production deployment. Before implementation is finalized, the team should confirm:

- The exact deployment boundary between Web Gateway and Agent Core, including whether they run as one or multiple application processes.
- Tool interfaces, permissions, and acceptance criteria for the additionally depicted PPTX, CSV, and PDF servers.
- The permitted network connection to the corporate mail system while retaining the prohibition on external AI API usage.
- The precise workspace isolation, audit retention, and security tests needed to support confidentiality claims.

## 7.8. Summary

The proposed architecture combines a local LLM with a modular agent backend and isolated MCP tools. A dedicated orchestration layer selects and supervises actions, while validation, policy enforcement, and user approvals remain outside the LLM. Structured state and files are stored locally, and unauthorized external AI connectivity is restricted.

Together, the system context, draft use-case, component, and document-editing workflow diagrams describe the system from functional, structural, and behavioral perspectives. They provide an initial architectural basis for implementation, integration testing, and evaluation of the diploma project.
