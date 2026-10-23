# 10. Project Management and Risks

## 10.1 Project Management Approach

The Local AI Agent is developed by a two-student team using GitHub for version control and YouTrack for planning and tracking work. The project follows an incremental development approach: individual modules are developed and tested before integration into the complete AI agent. Documentation, architectural decisions, code, and evaluation materials are maintained in the same repository.

### GitHub and YouTrack Workflow

- Each meaningful work item is represented by a YouTrack task with an assignee, priority, deadline, expected deliverable, and acceptance criteria.
- A task is developed in a dedicated Git branch whose name contains the YouTrack issue ID (for example, `101-2-github-repository`).
- Commit messages and pull requests reference the corresponding YouTrack issue ID.
- Changes are submitted through pull requests and reviewed by the other team member before being merged into `main`.
- The relevant pull request or commit is linked to its YouTrack task; completed tasks are closed after their acceptance criteria are met.
- Each student uses their own GitHub account so individual contributions remain traceable.
- The diploma project probationary period submission will be identified with the Git tag `prob-period-1`.

The repository and YouTrack project have already been created and connected, and the team follows the branch → commit → push → pull request → review → merge workflow.

## 10.2 Team Responsibilities


| Member | Proposed responsibility |
|---|---|
| Adilet Kabiyev | Agent core; LLM adapter; tool-call reliability; local RAG; benchmarking, CI/CD pipeline and Observability stack |
| Aibek Nurtay | Web gateway and UI; Word/Excel/email tools; policy and approval services; audit; workspace isolation; security and deployment |
| Both students | Tool contracts and MCP registry, observability, requirements, integration, documentation and peer review |

The supervisor provides technical and methodological guidance.

## 10.3 Project Milestones

The schedule below is a project planning baseline rather than an irreversible statement of the milestones. It is subject to change.

| Period | Milestone | Expected deliverables |
|---|---|---|
| By 24 October 2026 | Probationary period | Project proposal, research and comparisons, requirements, architecture materials, prototype materials, evaluation plan, repository and YouTrack evidence. |
| November 2026 | Agent core | FastAPI backend, interchangeable LLM adapter, orchestration, initial tool contracts and basic office tools. |
| December 2026 | Office and email capabilities | DOCX, XLSX, CSV, PDF and PPTX workflows; Gmail API, Microsoft Graph and/or SMTP/IMAP integrations; email review and approval. |
| January 2027 | Integrated MVP | React interface, integrated office agent, basic RAG, local deployment and user-facing task flows; target MVP freeze around mid-January. |
| February 2027 | Reliability and security verification | Tool-call validation/retry, policy enforcement, prompt-injection tests, action approval and audit verification. |
| March 2027 | Benchmarking and evaluation | Task benchmarks, full-model tests, performance measurements, model comparisons and analysis of failures. |
| April–May 2027 | Finalization and defense | Stabilization, diploma report, supervisor revisions, presentation and final demonstration. |

The milestones will be adjusted correctly over the course of the diploma project development with new information arriving about pre defenses and the diploma projects schedules.

## 10.4 Major Project Risks

| Risk | Probability | Impact | Mitigation strategy |
|---|---|---|---|
| Insufficient GPU/VRAM | High | High | Develop and test individual modules with Qwen2.5-7B; use rented GPU infrastructure for production-grade Qwen3-32B. |
| Unauthorized email delivery / spam | Low | Medium | Require explicit approval for the exact outgoing message, validate recipients, apply sending limits, and log delivery attempts. |
| Benchmarking challenges | Medium | High | Define a reproducible test dataset, automatic acceptance checks, consistent configurations, repeated runs, and clearly reported results and limitations. |
| Prompt injection | Medium | Medium | Treat document and email contents as untrusted input; enforce tool permissions and risk-based policies outside the LLM, and test adversarial scenarios. |



## 10.5 Technical and Infrastructure Dependencies

| Dependency | Planned solution | Why it matters |
|---|---|---|
| Development hardware | Existing team computers | Coding, document-tool tests, backend tests and small-model development. Exact CPU/RAM/GPU specifications will be documented. |
| Development model | Qwen2.5-7B | Testing individual tools, features and core modules; not intended as the production model. |
| Integrated / production model | Qwen3-32B | Full-agent integration and final evaluation. Exact model variant and hardware requirements must be confirmed. |
| GPU capacity | Rented GPU server (for example, Runpod) or university infrastructure | Large-model inference, integration tests and benchmarking. VRAM requirements depend on model variant, quantization, context and concurrency. |
| Backend and database | FastAPI and PostgreSQL with pgvector | APIs, tasks, approvals, histories, metadata and basic RAG. |
| File storage | Access-controlled local filesystem | Uploaded documents and generated artifacts remain within controlled storage. |
| Email connectivity | Gmail API / Google OAuth 2.0, Microsoft Graph / OAuth 2.0 | Connecting users existing mail accounts and delivering approved correspondence. External connections are limited to authorized email providers. |
| Deployment and monitoring | Docker Compose, Prometheus, Grafana | Reproducible deployment and visibility into runtime health and resource usage. |
| CI/CD | GitHub Actions | Automated quality checks and tests for pull requests. |



## 10.6 Task Dependencies and Development Order

1. Confirm available development hardware and the large-model evaluation environment.
2. Define shared API/tool schemas and set up a minimal FastAPI → agent → LLM adapter → tool execution path.
3. Develop office tools and email integrations as independently testable modules.
4. Integrate task state, user approvals, policy checks, file storage, and the minimal React interface.
5. Connect RAG and the selected production-class model; perform integrated testing.
6. Run security tests and reproducible benchmarks, document results, and refine the system.

These dependencies do not require all tasks to run sequentially: office tools, policy tests, benchmarks and frontend components may be developed in parallel once their interfaces are defined.

## 10.7 Open Dependencies and Next Actions

For the hardware dependencies the team views an option for cloud-based infrastructure access from Kazakhstans providers. The local deployment can be done on team members own hardware, however switching to the production-state LLM and final polishing of functionality and delivery of the MVP requires additional hardware. For this the team will seek either renting cloud based infrastructure or coming by with mutual agreement by cloud provider companies in Astana for supporting students development and research.

The team also views an option to switch from YouTrack to Jira and Confluence. Jira and Confluence are considered industry standard tools for project management, team collaboration and documentation handling. 