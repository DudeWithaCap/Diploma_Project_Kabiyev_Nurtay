# Local AI Office Agent

EN: Development of a Local AI Agent for Automated Processing of Office Documents (Word, Excel) and Email Correspondence in Confidentiality-Sensitive Corporate Environments

RU: Разработка локального ИИ-агента для автоматизированной обработки офисных документов (Word, Excel) и электронной переписки в корпоративных средах с повышенными требованиями к конфиденциальности

KZ: Құпиялылыққа жоғары талаптар қойылатын корпоративтік ортада кеңсе құжаттарын (Word, Excel) және электрондық хат алмасуды автоматтандырылған өңдеуге арналған жергілікті ЖИ агентін әзірлеу

Institution: Astana IT University, School of Software Engineering  
Supervisor: Almas Ospanov, Senior Lecturer  
Team: Adilet Kabiyev (SE-2415) · Aibek Nurtay (SE-2405)

## About the Project

Organisations handling confidential information may restrict the use of external AI services. This diploma project investigates a self-hosted office assistant that processes documents, spreadsheets and email using a locally hosted language model and controlled tools.

Users will submit natural-language tasks through a web interface, inspect generated files and approve sensitive actions before execution. The research focuses on tool-call reliability and protection against unauthorised actions, including those induced by prompt injection. Security and confidentiality properties will be evaluated through reproducible tests.

## Current Status

Stage: analysis, design and initial project scaffolding.

All eleven documentation components are completed for the current checkpoint. The repository includes project documentation, editable architecture diagrams, interface prototype screenshots and the supervisor feedback log.

The initial versions of the requirements, architecture and evaluation plan were accepted in Supervisor Review 2. In the table below, Completed refers to the documentation deliverable for this checkpoint. Application implementation and experimental verification remain separate milestones.

Initial frontend and backend scaffolding is covered by task `101-20`. Prototype screenshots illustrate intended interactions. End-to-end AI workflows and the stated performance targets have not yet been validated.

## Documentation

Documentation is maintained in [docs/](docs/), with supporting [diagrams](diagrams/) and [prototype materials](prototype/).

| No. | Component | Status |
| --- | --- | --- |
| 01 | Project Proposal | Completed |
| 02 | [Relevance Evidence](docs/02-relevance-evidence.md) | Completed |
| 03 | [Literature Review](docs/03-literature-review.md) | Completed |
| 04 | Existing Systems Comparison | Completed |
| 05 | Technology Comparison and Selection | Completed |
| 06 | Requirements Specification | Completed |
| 07 | Architecture Design and [Diagrams](diagrams/) | Completed |
| 08 | Interface Prototype and [Screenshots](prototype/) | Completed |
| 09 | Verification and Evaluation Plan | Completed |
| 10 | Project Management and Risks | Completed |
| 11 | [Supervisor Feedback Log](docs/11-supervisor-feedback-log.md) | Completed |

## Research Questions

- RQ1 — Reliability: Do schema validation, corrective feedback, bounded retries and constrained decoding improve tool-call validity and end-to-end task completion compared with native tool calling?
- RQ2 — Security: How effectively do independent policy enforcement, explicit user approval and handling of untrusted content prevent unauthorised actions and reduce prompt-injection success?

The evaluation plan defines reliability configurations C0–C4, security configurations D0–D3, artifact checks and reproducibility procedures. Acceptance thresholds are criteria for future experiments, not achieved results.

## Scope

The current scope and priorities are:

| Priority | Capabilities |
| --- | --- |
| Must | DOCX workflows; XLSX and CSV through a shared tabular tool; email workflows through IMAP/SMTP, Gmail API and Microsoft Graph for Outlook |
| Should | Read-only PDF text extraction; basic local RAG |
| Could | PPTX generation from structured outlines and templates; LoRA adaptation |
| Outside the current scope | OCR |

The intended deployment is on organisation-controlled infrastructure, with local AI inference. Email integrations will connect to configured mail services through IMAP/SMTP, Gmail API or Microsoft Graph for Outlook. A local mail server will be used as a development and testing environment, rather than as the only supported deployment option.

External AI inference APIs are excluded from the product and may be used only in explicitly enabled evaluation experiments with synthetic data. This restriction concerns AI inference services; Gmail API and Microsoft Graph are planned email integrations.

Provider accounts will require explicit authorisation, appropriately scoped permissions and protected credentials. Sending a message through any provider will require user approval bound to the exact recipients, content and attachments.

The scope above guides the requirements, technology choices, architecture and evaluation.

## Planned Architecture

| Layer | Planned technology or approach |
| --- | --- |
| Frontend | React + Vite |
| Backend | Python + FastAPI |
| Agent orchestration | LangGraph |
| Local model serving | Ollama / vLLM; selection subject to evaluation |
| Application database | PostgreSQL |
| Local retrieval | Local embeddings + pgvector |
| Office tools | Bounded document and tabular operations |
| Email | Provider adapters for IMAP/SMTP, Gmail API and Microsoft Graph (Outlook); explicit approval before sending |
| Deployment | Docker Compose |
| Automated checks | GitHub Actions |

Model selection and minimum hardware requirements will be determined through comparative testing on declared hardware. Validation, authorisation and approval enforcement must operate independently of model-generated instructions.

Component responsibilities and detailed data flows are documented in Part 07.

## Team

| Member | Role label | Main responsibilities |
| --- | --- | --- |
| [Adilet Kabiyev](https://github.com/DudeWithaCap), SE-2415 | A | Agent core, LLM adapter, tool-call reliability, local RAG, benchmarking, CI/CD and observability |
| [Aibek Nurtay](https://github.com/KhanOfTheMoon), SE-2405 | B | Web gateway and UI, office tools, email provider adapters, policy and approvals, audit, workspace isolation, security and deployment |
| Both students | Shared | Tool contracts, integration, documentation, evaluation and peer review |

This table defines responsibilities. Actual contributions are recorded through commits, pull requests and submission notes.

## Project Tracking and Contribution Workflow

Tasks are managed in the team's YouTrack project. Each work item should have an owner, expected deliverable, deadline and acceptance criteria. Its ID is included in the branch name, commit messages and pull request title.

The team's workflow requires changes to `main` to go through a pull request with one formal approval from the other student. Reviews should include at least one substantive comment about the content.

Example for the initial skeleton task:

```text
Branch: 101-20-build-frontend-skeleton
Commit: 101-20: Add initial frontend and backend skeleton
PR:     101-20: Add initial frontend and backend skeleton
```

Supervisor review references such as `SR1` and `SR2` identify feedback from Reviews 1 and 2. Include the relevant feedback reference when a task addresses a supervisor comment.

## Repository Structure

| Path | Purpose |
| --- | --- |
| `README.MD` | Project overview, status and documentation navigation |
| `.gitignore` | Exclusions for dependencies, temporary files and local configuration |
| `docs/` | Proposal, evidence, literature review, requirements, architecture, plans and feedback |
| `diagrams/` | Editable diagram sources and image exports |
| `prototype/` | Interface prototype materials and screenshots |
| `src/` | Existing source-code placeholder; final layout to be reconciled with the implementation plan |
| `frontend/` | Initial frontend scaffold specified by task `101-20` |
| `backend/` | Initial backend scaffold accompanying the frontend skeleton |

The initial skeleton task specifies root-level `frontend/` and `backend/` directories with placeholder files. A runnable application, dependencies and detailed module structure are subsequent implementation deliverables.

## Checkpoints

The first checkpoint is planned under the annotated tag `prob-period-1`, with a submission note at `docs/submissions/prob-period-1.md`.

Create the tag after the checkpoint materials have been reviewed and merged. Record outstanding work with owners and deadlines in the submission note and YouTrack.
