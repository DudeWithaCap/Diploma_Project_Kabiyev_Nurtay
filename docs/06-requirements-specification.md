# 6. Requirements Specification

## 6.1 Scope and Priorities

The project develops a self-hosted AI office assistant for confidentiality-sensitive organizations. One human actor, User, can access all supported functions for their own data and connected services. Inference, document processing, and retrieval run on organization-controlled infrastructure; external AI model APIs are excluded from production. Authorized mail-provider communication is a narrowly defined exception to outbound network restrictions.

The mandatory MVP covers: a web interface, DOCX, XLSX, CSV, PDF, and PPTX workflows, local RAG; reading, drafting, and sending email, controlled agent execution, reliability controls, independent security policy, explicit user approvals, and auditable operations. All requirements below are mandatory for the MVP. Verification targets are planned criteria, not achieved results.

## 6.2 Functional Requirements

| ID | Requirement | Acceptance criteria | Verification |
|---|---|---|---|
| FR-01 | Provide an authenticated web interface supporting natural-language tasks, uploads, task progress, action review, conversation history, and downloadable outputs. | Users can complete an end-to-end interaction; unauthorized users cannot access another user's tasks or artifacts. | Test, Demonstration |
| FR-02 | Maintain isolated user workspaces, files, sessions, metadata, and versioned artifacts. | Owner checks reject cross-user access; originals are preserved and updated outputs remain retrievable after restart. | Test |
| FR-03 | Orchestrate bounded multi-step tasks using registered, schema-defined tools and a replaceable local LLM adapter. | Multi-tool workflows enforce step/time limits and accurately report errors, changing from development Qwen2.5-7B to the selected Qwen3 production model requires no rewrite of office tools. | Test, Demonstration |
| FR-04 | Implement reliability mechanisms for structured tool invocation, including validation, bounded retries, corrective feedback, and constrained decoding. | Invalid calls cannot execute; C0–C4 configurations can be compared using repeatable tests. | Test, Benchmark |
| FR-05 | Create, populate, inspect, and safely edit DOCX files. | Outputs open correctly; headings, content constraints, template fields, and preservation of unrelated elements are verified. | Test, Benchmark |
| FR-06 | Analyze and modify XLSX and CSV data using bounded operations, including filtering, grouping, calculations, and permitted write-back. | Answers match independent pandas calculations on varied inputs; unauthorized arbitrary code execution is unavailable; untouched workbook content is preserved. | Test, Benchmark |
| FR-07 | Generate and perform supported edits to PPTX presentations through schema-validated outlines and deterministic template rendering. | Valid presentations satisfy specified slide/structure requirements; controlled edits preserve unaffected content. | Test, Benchmark |
| FR-08 | Extract, summarize, and use text from searchable PDF files. | Extracted content supports source-grounded answers; unsupported scanned-only PDFs are identified rather than silently misread. | Test, Demonstration |
| FR-09 | Index authorized local content for RAG and provide evidence-based answers with source references. | Retrieval respects document ownership, citations support answers, and unanswerable questions receive appropriate refusals. | Test, Benchmark |
| FR-10 | Read and summarize user-authorized incoming email and prepare editable drafts and attachments. | Supported mailboxes, messages, and drafts are handled within the owner's permissions; missing recipient information is not invented. | Test, Demonstration |
| FR-11 | Send outgoing email only after explicit approval of the exact recipients, subject, body, and attachments. | Changed or rejected drafts cannot be sent; approved messages use Gmail API/OAuth 2.0, Microsoft Graph/OAuth 2.0, or supported corporate SMTP/IMAP connections without duplicate sends. | Test, Demonstration |
| FR-12 | Enforce deterministic risk-aware policies outside the LLM for tool actions and resource access. | Unauthorized file operations, recipients, and external actions are denied regardless of model output; approval-required actions cannot bypass consent. | Test |
| FR-13 | Handle external document and email content as untrusted input and record auditable decisions. | Prompt-injection fixtures are evaluated, audit events capture actors, actions, approvals, and outcomes without exposing secrets in routine logs. | Test, Inspection, Benchmark |
| FR-14 | Provide repeatable self-hosted deployment, persistent application storage, and automated verification facilities. | Docker Compose deploys the stack, PostgreSQL persists app metadata, pgvector supports retrieval, and a FakeLLM/synthetic-mail test environment runs without a paid model. | Test, Demonstration |
| FR-15 | Support tasks in Russian, English, and Kazakh and preserve evaluation evidence. | At least ten language-tagged tasks per language are evaluated independently; runs record configurations and results. | Test, Benchmark |

## 6.3 Action Permissions and Approval Rules

The agent distinguishes read-only operations from creation, modification, and external side effects. A deterministic policy engine evaluates each proposed action and either allows it, requires explicit approval, or denies it. The decision is independent of the LLM. Approval is bound to the precise proposed operation and is invalidated if its content, target, or attachments change. Unknown tools and unauthorized resources are denied by default. Retrieved documents and email messages never receive system-level authority.

## 6.4 Non-Functional Requirements

| ID | Category | Requirement and planned verification target | Verification |
|---|---|---|---|
| NFR-01 | Confidentiality and data locality | Production LLM inference, embeddings, document processing, and RAG remain local. Unauthorized egress is blocked and measured in an isolated test profile; authorized external mail services are explicitly configured exceptions. | Inspection, Test |
| NFR-02 | Security and access control | Authenticate users, isolate owned resources, protect secrets, restrict executable tool operations, require exact-action approvals, and evaluate prompt-injection protection on at least 30 attempts (target attack success ≤10%). | Test, Inspection, Benchmark |
| NFR-03 | Tool reliability | Target at least 98% valid structured tool calls with the reliability layer enabled; report C0 baseline results separately. | Benchmark |
| NFR-04 | Task and artifact quality | Target at least 75% success on a 60-task core set (20 DOCX, 20 XLSX, 20 Email); separately validate CSV, PDF, PPTX, and RAG outputs, including file integrity and factual consistency. | Test, Benchmark |
| NFR-05 | Performance and capacity | Target median task duration ≤60 seconds and p95 ≤180 seconds on declared production-model hardware; test five concurrent users without crashes or cross-user exposure. | Benchmark |
| NFR-06 | Deployability and availability | Target a clean installation within 30 minutes once prerequisites/model files are ready; persist work across restarts and perform core operations without external AI inference. | Test, Demonstration |
| NFR-07 | Testability, observability, and reproducibility | Target ≥95% automated tests passing and ≥80% core-module coverage; record model/runtime, data versions, resource usage, errors, approvals, latency, and benchmark conditions without logging secrets. | Test, Inspection, Benchmark |
| NFR-08 | Controlled external evaluation | External AI baselines use synthetic/public data only, an opt-in isolated `eval/external_baselines/` package, no external calls in production or CI. | Inspection, Test |

## 6.5 Verification and Acceptance Criteria

Acceptance criteria AC-01–AC-10 are aligned with the Section 9 verification plan and describe planned measurements, not obtained results.

| ID | Planned acceptance criterion | Evidence |
|---|---|---|
| AC-01 | ≥75% task success over ≥60 core tasks: 20 DOCX, 20 XLSX, and 20 email. | Reference checkers and saved task artifacts. |
| AC-02 | ≥98% valid tool calls with reliability controls, compared with C0. | Repeated E1 C0–C4 results. |
| AC-03 | No side-effecting action executed without the required approval. | Policy and approval test logs. |
| AC-04 | No unauthorized outbound network packets in the isolated zero-egress test profile, including successful detection of a negative control. | Captured network measurements. |
| AC-05 | ≤10% executed prompt-injection success over ≥30 attacks. | E3 D0–D3 evaluation records. |
| AC-06 | Median task time ≤60 s; p95 ≤180 s on declared hardware. | Timed end-to-end results. |
| AC-07 | ≥95% automated tests passing; ≥80% coverage for core modules. | CI test/coverage reports. |
| AC-08 | Clean installation ≤30 minutes with prerequisites available. | Deployment rehearsal. |
| AC-09 | ≥10 evaluation tasks each for RU, EN, and KK, reported independently. | Language-tagged benchmark records. |
| AC-10 | Five concurrent users tested without crashes or improper cross-user access. | Load-test logs. |

DOCX/PPTX evaluation additionally covers L0–L4 (validity, constraints, factual fidelity, judged quality, edit correctness). CSV/XLSX uses independently computed reference results. RAG uses the retriever × generator 2×2 design. These are mandatory test activities even though AC-01's inherited core set focuses on three workflows.

## 6.6 Research and Evaluation Traceability

| Research or experiment | Requirement coverage | Evidence |
|---|---|---|
| RQ1 / H1; E1 | FR-03, FR-04; NFR-03 | C0–C4 reliability ablations, valid-call rate, task success. |
| RQ2 / H2; E3–E4 | FR-11–FR-13; NFR-01, NFR-02 | D0–D3 security ablations, approval tests, measured egress. |
| Functional workflows; E2, E6 | FR-05–FR-10, FR-15; NFR-04 | Format-specific tests and multilingual results. |
| Performance and deployment; E5 | FR-14; NFR-05–NFR-07 | Latency, capacity, deployment and test records. |
| Model swap; E7 | FR-03, FR-04; NFR-08 | Same tools/harness and prompts, changing only the model. |
| Product comparison; E8 | FR-05–FR-11; NFR-08 | Small descriptive manual baseline against cloud AI products. |
| Document and slide quality; E9 | FR-05, FR-07; NFR-04 | L0–L4 checks, blinded comparative evaluation. |

The Qwen2.5-7B model is only for feature-level development and module tests. Integrated-agent and production-oriented evaluation uses the chosen Qwen3-32B, with its exact checkpoint and runtime recorded. Cloud model APIs serve only as optional research baselines on synthetic data.

## 6.7 Requirements Management

Implementation tasks are tracked in YouTrack and linked to GitHub branches and reviewed pull requests. Each requirement must be supported by tests, demonstrations, inspections, or benchmark artifacts.