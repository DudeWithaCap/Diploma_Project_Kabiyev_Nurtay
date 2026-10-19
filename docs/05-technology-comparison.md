# 5. Technology Comparison and Selection

## 5.1 Purpose and Evaluation Criteria

The project aims to build a self-hosted AI agent that executes everyday office workflows through natural-language instructions. Its principal functions are document, spreadsheet, presentation and PDF processing; multi-step tool execution; and secure email drafting with explicit user approval before SMTP delivery.

Technologies are assessed against five common criteria:

1. Functional suitability
2. Integration and maintainability
3. Self-hosting and data control
4. Resource requirements and performance
5. Implementation complexity

## 5.2 Language Model Selection

The model must support local inference, instruction following, multilingual office communication, predictable structured output, and tool calling. Since the development hardware is limited, the project uses a smaller model for module tests and a larger model for complete-agent evaluation. Model-card specifications are not evidence of performance on our own office benchmark.

| Candidate | Parameters / Context | Advantages for Our Project | Limitations | Selection |
| --- | --- | --- | --- | --- |
| **Qwen2.5-7B** | 7.61B parameters; 32,768-token default context, up to 131,072 with YaRN; Apache-2.0 | Instruction-tuned; official documentation highlights JSON/structured output, understanding of tables, long-text processing, and support for Russian among 29+ languages. Its smaller size makes repeated local testing of tool schemas, DOCX/XLSX utilities, and agent state feasible. | Limited reasoning and multi-step tool reliability relative to larger candidates; long context requires special runtime configuration; Kazakh language and real task success must be measured. | Development / baseline model |
| **Qwen3-32B** | 32.8B parameters, dense; 32,768 native context, up to 131,072 with YaRN; Apache-2.0 | Larger capacity for longer, multi-step office tasks; Qwen documents agent/tool-use capabilities, 100+ languages, and selectable thinking vs. non-thinking modes. Good candidate for testing whether a stronger model improves tool selection, recovery, and cross-document tasks. | High GPU/VRAM cost and latency; thinking increases inference time; tool calling, Russian/Kazakh quality, and long-context behavior still require task-specific validation. | Preferred large-model candidate and will be used in production |
| **Llama 3.1 8B Instruct** | 8B parameters; 128K context; Llama 3.1 Community License | Comparably sized control for Qwen2.5-7B; mature local-serving ecosystem and documented tool-use format. | Meta's explicitly supported languages do not list Russian or Kazakh; license conditions differ from Apache-2.0. | Alternative development baseline |
| **Gemma 3 27B IT** | 27B parameters; 128K context; Gemma Terms of Use | Similar scale to the large Qwen candidate; multilingual coverage (140+ languages) and document-image understanding could be useful for future multimodal tasks. | Tool-calling behavior needs validation with our runtime and agent schema; license is not Apache-2.0; multimodality is not a core MVP requirement. | Alternative large model |
| **Mistral Small 3.1 24B Instruct** | 24B parameters; 128K context; Apache-2.0 | Strong alternative at a somewhat smaller size; model card documents function calling, JSON output, Russian support, and self-hosting. | Still requires substantial inference resources; Kazakh support and reliability on our document/email benchmark are not established. | Alternative large model |

**Why Qwen2.5-7B for module development.** 

The model offers a useful balance between hardware cost and the capabilities needed for early engineering: interpretable instructions, tabular-text understanding, JSON-oriented output and multilingual prompts. At approximately 7.61 billion parameters, its raw BF16 weight storage is about 15.2 GB, or 3.8 GB at a theoretical four-bit weight-only representation; a real deployment needs additional memory for quantization metadata, the KV cache and runtime. Development with a smaller model makes it practical to run many deterministic tests of tool contracts, error recovery and user-approval logic without paying for a large GPU on every iteration. It also provides a deliberately weaker baseline against which the proposed reliability layer can be evaluated.

**Why Qwen3-32B for integrated testing.**

 Qwen3-32B is a dense 32.8-billion-parameter instruction/reasoning model whose published features directly match our agent's difficult tasks: multi-step planning, interaction with tools, multilingual instructions and switching between thinking/non-thinking modes. We can test whether thinking improves planning but raises latency and use non-thinking mode for routine argument generation. Its raw BF16 weights require approximately 65.6 GB, with a theoretical four-bit weight-only size around 16.4 GB, before runtime overhead and context-cache memory. It therefore belongs on an adequately provisioned self-hosted or rented GPU system rather than being assumed to fit a typical laptop.

Selection methodology and limitations. These are engineering candidates, not winners established by an experiment. Before the full agent is finalized, run the same synthetic Word/Excel/email tasks through Qwen2.5-7B, Qwen3-32B and at least one alternative, recording valid tool-call rate, task success, RU/EN/KK behavior, latency, throughput and peak VRAM. Freeze prompts, tool schemas and decoding settings where possible, and document model-specific chat-template differences. Both Qwen checkpoints are distributed under Apache-2.0, simplifying license evaluation compared with some alternatives. Switching models through \`LLMClient\`/Ollama/vLLM avoids rewriting business logic, but does not guarantee identical tool-call behavior. 

## 5.3 LLM Inference Engine

| Candidate | Advantages | Limitations | Decision |
| --- | --- | --- | --- |
| **Ollama** | Easy local installation and model management; accessible local HTTP API | Less control over advanced serving configurations compared with specialized high-throughput engines | Selected for development |
| **vLLM** | OpenAI-compatible serving API; optimized GPU inference and concurrency | More infrastructure configuration and hardware requirements | Preferred for GPU-hosted integrated testing/deployment |
| **llama.cpp** | Flexible quantized inference and CPU/GPU options | Production serving and throughput characteristics depend on configuration | Alternative |

**Rationale** 

The agent backend will use a provider abstraction compatible with standard chat/tool-calling interfaces. Ollama minimizes setup cost during development. vLLM is preferred for the larger model when suitable GPUs are available. Both must remain inside the approved infrastructure, and the inference endpoint must not be exposed publicly without appropriate access controls.

## 5.4 Agent Orchestration Framework

| Candidate | Advantages | Limitations | Decision |
| --- | --- | --- | --- |
| **LangGraph** | Explicit stateful workflows, checkpoints and human-in-the-loop interruption/resumption | Requires careful workflow and state design | Selected |
| **CrewAI** | Convenient role/task-oriented multi-agent abstraction | Multi-agent abstractions are unnecessary for the initial single-assistant MVP | Alternative |
| **AutoGen** | Flexible multi-agent interactions and tool-based workflows | Adds orchestration complexity for a narrowly scoped office assistant | Alternative |

**Rationale** 

The intended workflow is: interpret request -> prepare tool action -> validate parameters -> execute safe operations -> present drafts/results -> request approval for external actions -> execute authorized action -> log outcome. LangGraph is preferred because interrupted approval workflows can be resumed from saved state. An agent framework does not itself enforce permissions, tool access and approvals will also be checked in backend code.

## 5.5 Backend Framework and Programming Language

The comparison is not limited to Python web frameworks: Go and JavaScript/TypeScript can also build performant backends. The critical criterion is the total engineering complexity of integrating our Python-based LLM agent and office tools, not HTTP throughput in isolation.

| Candidate | Strengths | Trade-offs for this project | Decision |
| --- | --- | --- | --- |
| **FastAPI (Python)** | Type-hint/Pydantic validation, generated OpenAPI docs, async endpoints and direct access to Python AI/ML and office libraries | CPU-intensive or long-running tasks must use workers/processes; FastAPI alone does not provide a job queue | Selected |
| **Django + Django REST Framework (Python)** | Batteries-included authentication, admin and ORM; mature ecosystem | More application framework and configuration than required for a narrow API-first agent prototype | Alternative |
| **Flask (Python)** | Small, flexible core with a low entry barrier | Validation, API documentation and async/background infrastructure need more additional design or extensions | Alternative |
| **NestJS (Node.js / TypeScript)** | Structured modules, dependency injection, strong TypeScript tooling and asynchronous I/O | JavaScript LLM ecosystems exist, but the selected document-processing libraries, evaluation code and agent components are Python-oriented; would require a second Python service or reimplementation | Alternative |
| **Gin (Go)** | Compiled performance, low overhead, good concurrency and straightforward deployment | AI-agent frameworks, embedding tooling, document manipulation and scientific evaluation used here are primarily in Python; introduces inter-service communication or duplicated logic | Alternative |

**Why FastAPI specifically** 

The architecture is centered on Python LangGraph, Pydantic tool contracts, `python-docx`, `openpyxl`, `pandas`, PDF/PPTX tooling and evaluation scripts. FastAPI keeps HTTP endpoints, request validation and orchestration in one language and allows direct reuse of the same typed schemas for tool requests, approvals and API responses. Automatic OpenAPI generation also makes the frontend/backend interface easier to document and conduct tests.

**Why not Node.js/TypeScript or Go?** 

Both are technically capable of exposing secure, high-performance APIs, and either could be reasonable if HTTP serving were the primary task. In this project, however, most complexity lies in LLM/tool orchestration, Office-file operations, structured validation and security policy testing. Selecting NestJS or Gin would either split the system into a non-Python gateway plus Python AI/tool workers or increase implementation effort by replacing established libraries. That adds deployment, serialization and debugging boundaries without a demonstrated benefit for the single-organization MVP.

**Runtime boundary** 

FastAPI will handle authentication, file uploads, task creation, approval decisions, status and SSE updates. LLM inference runs in a separate local Ollama/vLLM service, while potentially lengthy agent tasks execute in controlled background workers rather than blocking API requests. FastAPI is chosen for ecosystem fit and maintainability, not because it is universally faster than Go or Node.js.

## 5.6 Database and Agent Memory

| Candidate | Advantages | Limitations | Decision |
| --- | --- | --- | --- |
| **PostgreSQL** | Relational integrity, transactions and structured storage for users, jobs, approvals and activity logs | Requires migrations and database administration | Selected |
| **MongoDB** | Flexible document schemas and nested records | Less aligned with the primarily relational core entities | Alternative |
| **SQLite** | Very low setup overhead | Less suitable for a concurrent multi-user server deployment | Alternative |

**Rationale**

 PostgreSQL will store users, sessions, chat metadata, task state, audit records, approval decisions and artifact metadata. LangGraph persistence can use a PostgreSQL-compatible checkpointer. Actual office files should be stored in access-controlled local/object storage, not embedded in the relational database. A basic local RAG pipeline is planned for the first version: local multilingual embeddings, retrieval and citations using pgvector in PostgreSQL. Retrieval quality and data isolation will be evaluated before expanding to more complex search strategies.

## 5.7 Office File Processing Libraries

| File type | Selected technology | Alternatives | Selection rationale / scope |
| --- | --- | --- | --- |
| DOCX | **python-docx** | LibreOffice headless, docxtpl | Read and modify common Word document structure; complex layout fidelity needs testing |
| XLSX | **openpyxl** | pandas, LibreOffice headless | Cell-level editing, formulas and workbook structure; formula *calculation* is not provided by openpyxl |
| CSV / analytical tables | **pandas** | Python `csv`, Polars | Filtering, aggregation and tabular analysis |
| PPTX | **python-pptx** | LibreOffice UNO, PptxGenJS | Creating and editing common slide objects; advanced fidelity is a limitation |
| PDF | **PyMuPDF** | pypdf, pdfplumber | Text extraction and selected operations; arbitrary PDF editing and scanned-file OCR are not guaranteed in MVP |

**Rationale** 

Direct Python libraries provide bounded, testable operations and allow the agent to call narrowly scoped tools instead of executing arbitrary generated Python. Each tool will validate file type, path, size, input arguments and output integrity. Generated files will be previewable/downloadable and isolated from original files where practicable. Complex formatting and compatibility will be explicit evaluation criteria.

## 5.8 Secure Email Integration

The AI assistant must support secure email correspondence using users' existing email accounts. Users should be able to connect their Gmail, Microsoft Outlook, or corporate email accounts, allowing the agent to read messages, generate drafts, manage attachments, and send emails on their behalf after explicit approval.

| Candidate | Advantages | Limitations | Decision |
|---|---|---|---|
| **Gmail API + Google OAuth 2.0** | Secure connection to Gmail accounts without storing user passwords; supports reading messages, managing drafts, attachments, and sending emails from the user's address | Requires Google Cloud configuration, OAuth permissions, and potentially application verification | Selected for Gmail integration |
| **Microsoft Graph API + OAuth 2.0** | Native integration with Outlook and Microsoft 365; supports reading, drafting, sending emails, and managing attachments | Requires Microsoft Entra application registration and appropriate permissions; organizational policies may restrict access | Selected for Outlook integration |
| **SMTP + IMAP** | Standard protocols compatible with many corporate email servers; SMTP handles sending while IMAP provides access to incoming messages | Authentication and supported features vary by provider; additional configuration may be required | Selected for generic corporate email integration |

**Rationale** 

The system will use provider-specific APIs for Gmail and Microsoft Outlook, allowing users to securely connect their existing accounts through OAuth 2.0 authorization. For other corporate email providers, SMTP and IMAP will provide standard email functionality.

The agent will support reading incoming correspondence, summarizing email threads, generating replies, composing new messages, and handling attachments. Before sending any email, the system must display the recipient, subject, body, and attachments for user review. Email delivery will occur only after explicit user approval, and the approved content must not be modified without requesting approval again.

OAuth tokens and other authentication credentials must be stored securely and never exposed to the LLM. The system must restrict email access to authorized accounts and record relevant actions in an audit log.

Unlike external AI inference services, Gmail, Outlook, and corporate mail servers are explicitly authorized communication endpoints. The system will process email content using the locally deployed LLM, while external connections will be limited to the configured email providers. This preserves the project's local AI processing architecture while enabling practical email correspondence.



## 5.9 Frontend Selection

| Candidate | Advantages | Limitations | Decision |
| --- | --- | --- | --- |
| **React + Vite** | Small component-based web client; convenient chat, upload and approval interfaces | Requires separate build and deployment workflow | Selected |
| **Vue** | Lightweight UI development | No major advantage for current team preferences | Alternative |
| **Server-rendered HTML/templates** | Minimal frontend dependencies | Interactive approval and task-state updates require more custom implementation | Alternative |

**Rationale.** The frontend will intentionally remain minimal: conversation interface, file upload/download, task status, generated email preview/edit/approve actions and history. A complex office editor is out of scope.

## 5.10 Deployment, CI/CD and Monitoring

| Decision | Selected | Alternatives | Reason |
| --- | --- | --- | --- |
| Container deployment | **Docker Compose** | Kubernetes, manual host services | Reproducible multi-service MVP with lower operational complexity |
| CI/CD | **GitHub Actions** | GitLab CI, Jenkins | Fits the GitHub repository and pull-request workflow |
| Metrics | **Prometheus** | OpenTelemetry Collector + other metrics backends, Zabbix | Scrape application/service metrics |
| Dashboards | **Grafana** | Kibana, built-in dashboards | Operational visualization of latency, error rate and resource use |

**Rationale** 

The initial stack will run the React frontend, FastAPI backend, PostgreSQL, model-serving process and supporting services on controlled infrastructure. Docker Compose is appropriate for a prototype; Kubernetes is not justified until deployment scale or availability requirements demand it. GitHub Actions will run linting, tests, dependency checks and image builds. Deployment credentials belong in protected secrets, never in the repository. GitHub-hosted runners must not be given confidential production documents as test fixtures.

## 5.11 Selected Technology Stack

| Layer | Selected technology |
| --- | --- |
| Development LLM | Qwen2.5-7B |
| Larger integrated LLM | Qwen3-32B (subject to comparative benchmark) |
| LLM serving | Ollama (development); vLLM (GPU deployment) |
| Agent orchestration | LangGraph |
| API backend | Python / FastAPI |
| Application database | PostgreSQL |
| Frontend | React + Vite (minimal UI) |
| File processing | python-docx, openpyxl, pandas, python-pptx, PyMuPDF |
| Email | SMTP (`smtplib` / `email`), explicit user approval |
| Agent memory / task state | PostgreSQL + LangGraph checkpointer |
| File storage | Access-controlled local filesystem, file metadata in PostgreSQL |
| Basic RAG | Local embeddings + PostgreSQL / pgvector |
| Containerization | Docker / Docker Compose |
| CI/CD | GitHub Actions |
| Monitoring | Prometheus + Grafana |

## 5.12 Architectural Fit and Security Considerations

The backend will expose a model-independent interface to the inference service. LangGraph coordinates tool calls and approval states, while deterministic Python tools manipulate office files and send approved email. PostgreSQL stores operational state; file content remains in controlled storage. React provides the user interaction surface. Prometheus/Grafana observe system performance without collecting document or email contents.

**Important safeguards:**

1. Separate the LLM from privileged tools; model-generated text must not directly grant permissions or execute arbitrary system commands.
2. Require authorization and explicit approval for email transmission and other externally visible or irreversible actions.
3. Restrict file access to the requesting user's workspace and validate uploads/outputs.
4. Use secure network transport, secret management, access controls and audit logging; self-hosting alone does not guarantee compliance.
5. Treat uploaded documents and incoming content as untrusted input; mitigate prompt injection at the tool/permission boundary.
6. Validate the larger model's tool-use reliability, output formats, latency and hardware requirements before finalizing deployment.

## 5.13 Conclusion

The chosen architecture balances ease of development, self-hosting, modularity and an auditable approval process. FastAPI, PostgreSQL and React provide the application foundation; Ollama/vLLM isolate model execution; LangGraph coordinates multi-step actions; and specialized Python libraries implement bounded office operations. SMTP enables approved outbound correspondence without relying on a third-party AI provider. Model selection and deployment capacity remain subject to practical measurements. This technology assessment meets the requirement to compare multiple architectural decisions with reasonable alternatives, but its claims must be verified during implementation and evaluation.