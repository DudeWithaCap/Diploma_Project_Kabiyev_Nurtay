# Part 1 — Project Proposal

**Project:** Development of a Local AI Agent for Automated Processing of Office Documents (Word, Excel) and Email Correspondence in Confidentiality-Sensitive Corporate Environments

**Institution:** Astana IT University - School of Software Engineering 

**Team:** Adilet Kabiyev (SE-2415); Aibek Nurtay (SE-2405)  

**Supervisor:** Almas Ospanov, Senior Lecturer  


## 1.1 Project Overview and Team

This diploma project proposes a self-hosted AI office assistant that supports common office workflows through natural-language requests. Users will interact with a local web application, submit documents, inspect agent actions and generated artifacts, and approve sensitive operations before execution. The main use cases include Word, Excel, CSV, PDF, and PowerPoint files, as well as business email correspondence.

The system will use an open-weight language model hosted within a controlled environment and deterministic tools for file and email operations. An agent orchestration layer will coordinate tasks, while independent validation, authorization, approval, auditing and network-isolation mechanisms will limit unsafe behavior. The project will evaluate not only whether office tasks can be completed, but also whether tool invocation is reliable and confidentiality controls can be tested.

### Team and proposed division of work

| Member | Proposed responsibility |
|---|---|
| Adilet Kabiyev | Agent core; LLM adapter; tool-call reliability; local RAG; benchmarking, CI/CD pipeline and Observability stack |
| Aibek Nurtay | Web gateway and UI; Word/Excel/email tools; policy and approval services; audit; workspace isolation; security and deployment |
| Both students | Tool contracts and MCP registry, observability, requirements, integration, documentation and peer review |

The supervisor provides technical and methodological guidance.

## 1.2 Problem Statement

Large language models and AI agents can accelerate routine office work, such as reviewing contracts, analyzing spreadsheets, preparing presentations and drafting business correspondence. However, organizations handling sensitive corporate information may restrict or prohibit external AI assistants because using them can involve transmitting documents, emails or personal data to third-party infrastructure. Such restrictions may arise from internal information-security policies, contractual obligations and applicable legal requirements.

Data protection and legal requirements in Kazakhstan create additional constraints. The Law of the Republic of Kazakhstan On Personal Data and Their Protection (No. 94-V) governs the collection, processing and protection of personal data. In particular, Article 12 addresses the storage of personal data in databases or digital objects located in Kazakhstan, while Article 16 establishes conditions for cross-border transfers. The Law On Artificial Intelligence (No. 230-VIII, 17 November 2025), Article 10, also requires data protection and confidentiality measures when AI systems process personal information. As office documents and corporate correspondence may contain employee, customer or other protected information, organizations need to assess where such data is processed, which parties can access it, and whether external transfers are permitted. These provisions do not amount to a universal prohibition on cloud AI; obligations depend on the information, purposes and arrangements involved.

Internal corporate security policies may impose stricter limits than the minimum legal requirements. An organization may ban publicly hosted AI chatbots and agents altogether, authorize only specifically reviewed enterprise tools, or prohibit the upload of source code, contracts, customer records, financial reports and other confidential files. These decisions can reflect trade-secret protection, contractual nondisclosure commitments, vendor-risk assessments, data-classification rules, retention policies, and limited visibility into how external AI platforms handle information. Consequently, employees may lose access to useful AI-assisted workflows even when a particular external service has privacy or enterprise-security features. The prevalence and exact form of these restrictions will be investigated through the stakeholder interviews described in Part 2 rather than assumed in advance.

Running an open-weight model locally reduces reliance on external AI APIs but does not, by itself, make an agent safe. Smaller local models may produce invalid tool calls or incorrect calculations; documents and incoming emails can contain prompt-injection instructions; and tools with broad permissions may modify files or send messages without adequate checks. Claims that data remains inside an organization's infrastructure also require technical verification rather than assumption.

The problem addressed by this project is therefore the need for an accessible, locally deployable office AI agent whose task execution is both reliable and subject to verifiable confidentiality and action-control mechanisms. The project will investigate how a tool-call reliability layer, explicit policies, human approval, audit logging and network isolation can address these limitations.

## 1.3 Target Users and Stakeholders

The target users are employees and professionals who perform everyday office work, including administrative staff, analysts, managers, HR specialists, finance personnel and technical employees. The system is particularly relevant in organizations with confidentiality requirements or internal restrictions on external AI services.

The application defines one human actor: User. Professional background does not determine a separate role or a different set of product features. Every user has full functional access to their own AI agent and authorized workspace, including document processing, email drafting and approval, task history and agent settings. File isolation, organizational security policies and mandatory approval for sensitive actions still apply. Organizations provide the operational context for deployment, rather than a separate user or stakeholder role in this proposal.

## 1.4 Project Aim

To design, implement and experimentally evaluate a locally deployable AI office assistant for DOCX, XLSX, CSV, PDF, PPTX, and email workflows that uses no external AI APIs during core inference and document processing, improves the reliability of LLM-generated tool calls, and enforces verifiable safeguards against unauthorized actions and data disclosure.

## 1.5 Project Objectives

1. Analyze relevant research, existing local/cloud AI assistants, office automation tools and security threats to tool-using LLM agents.
2. Define measurable functional and security requirements; prepare synthetic office-task datasets and evaluation procedures.
3. Compare candidate local LLMs and runtimes under the team's actual hardware constraints using a reproducible benchmark.
4. Design a modular agent with a replaceable LLM adapter, dedicated Word/Excel/CSV/PDF/PowerPoint and email tools, and basic local RAG.
5. Implement a tool-call reliability layer using schema validation, bounded repair/retry and supported structured-output constraints.
6. Implement deterministic risk-based authorization, human approval, workspace isolation, audit logging and controls against prompt injection and unauthorized network access.
7. Evaluate task success, tool-call validity, security behavior, latency and resource use; report successes, failures and limitations.

## 1.6 Research Questions and Hypotheses

- RQ1 / H1 — Reliability: Does schema validation, corrective feedback and constrained decoding improve valid tool calls and end-to-end task completion relative to native tool calling alone?
- RQ2 / H2 — Security: Can independent policy enforcement and human approval prevent unauthorized side-effecting actions, and how much do they reduce the success of prompt-injection attempts?


## 1.7 Research Object and Subject

- Object of study: Corporate workflows involving office documents and business email correspondence.
- Subject of study: Methods and software mechanisms for developing a reliable, controllable, confidentiality-oriented local LLM agent with tool access.

## 1.8 Project Scope

### Mandatory MVP

- Local web interface: Natural-language chat, file upload/download, agent execution status, action previews, approvals and results.
- Word (`.docx`): Read, summarize, extract structured information, fill templates, create documents and perform controlled edits.
- Excel (`.xlsx`): Read and analyze workbooks, execute approved deterministic calculations, create and update sheets, and write validated results.
- CSV (`.csv`): Import, analyze, filter, aggregate, edit and export tabular data as a first-class user-facing format.
- PDF (`.pdf`): Extract and analyze text, summarize documents and use their contents in office workflows. Reliable arbitrary PDF editing and OCR of scanned files are not promised as baseline functions.
- PowerPoint (`.pptx`): Generate and modify presentations through controlled tools, including slide text and commonly supported layout elements.
- Secure email correspondence: Read and summarize messages using IMAP, create and edit drafts, handle appropriate attachments and deliver through SMTP only after explicit user approval; development and evaluation use synthetic messages and a local test mail server.
- Basic local RAG: Index authorized documents, retrieve relevant passages with local embeddings and return answers with file/section references.
- Agent execution and safeguards: Pluggable local LLM runtime, multi-step tool execution, schema validation and bounded repair/retry, deterministic policy enforcement, human approval, audit logging, workspace isolation and prompt-injection defenses.
- Controlled deployment and evaluation: Docker Compose deployment, safeguards against unapproved external data transmission, functional/security tests and a reproducible evaluation harness.

All capabilities listed above belong to the mandatory project MVP, including CSV, PDF text processing, PPTX creation/editing and basic RAG. Scope is defined by the operations stated here rather than unrestricted editing of every feature of each file format.

### Out of scope for the initial research prototype

- Training a foundation LLM from scratch or requiring large-model fine-tuning to complete the MVP.
- Enterprise SLA, production certification, high availability and broad multi-tenant deployment.
- Live corporate mailboxes or confidential corporate datasets during development or evaluation.
- Third-party cloud LLM APIs as a fallback; messaging platforms as the primary interface.
- Native mobile applications, voice interaction and full ERP/CRM integrations.

## 1.9 Expected Outcomes

1. A working research prototype with local LLM inference and tested DOCX, XLSX, CSV, PDF, PPTX, RAG and email workflows.
2. Modular architecture and implementation of reliability, tool-policy, approval, audit and workspace controls.
3. A reproducible synthetic benchmark, repeatable evaluation scripts and comparative results for models/reliability configurations.
4. Security test evidence covering unauthorized actions, prompt injection and outbound network traffic, including negative controls.
5. Architecture diagrams, requirements, technical documentation, deployment instructions and a Git/YouTrack record of individual contributions.

Expected performance numbers are targets for future experiments, not demonstrated achievements.
