# 3 Literature Review

## 3.1 Review methodology

This review examines ten research publications from 2024-2026 to identify methods relevant to a self-hosted AI Office Assistant. The sources cover document question answering, retrieval-augmented generation (RAG), tool use, model adaptation, multilingual processing and security. Section 3.5 separately considers two technical books and three Kazakhstan legislative acts as engineering guidance and a regulatory framework.

The review uses a structured narrative approach within the supplied collection, rather than a systematic search of the entire field. For each paper, four items are extracted: methodology, main contribution and results, limitations or untested capabilities, and possible application to the project. Empirical studies are assessed by their datasets, baselines and evaluation conditions. Surveys provide conceptual guidance rather than new benchmark results. Reported scores are interpreted within each study and are not ranked across different datasets.

## 3.2 Review of selected studies

### 3.2.1 Muludi et al. 2024 - Document question answering with RAG [1]

- Methodology: The authors combine GPT-3.5-turbo, LangChain, FAISS and a Streamlit interface. PDFs are split into chunks, embedded and retrieved to provide context for answers. Evaluation uses DocuQA, containing 20 documents and 100 questions, and PDF-converted SQuAD material.
- Contribution and results: They implement a document QA application and report 96% accuracy on DocuQA, alongside text similarity metrics and comparison with a commercial QA application.
- Limitations: The custom benchmark is small. The comparison does not isolate retrieval from differences in the underlying systems. Local inference, Office-file editing and approval-controlled actions are outside the evaluated scope.
- Project application: Use the ingestion-retrieval-generation workflow and prepare document-specific questions with reference answers. Replace external inference with a locally deployed model.

### 3.2.2 Khalib et al. 2026 - Chunking structured documents [2]

- Methodology: Four chunking strategies are tested through six configurations on 65 institutional documents and 28 Malay queries. Embeddings, reranking and local Gemma-3-4b-it generation remain fixed. Evaluation covers retrieval, answer quality and table integrity.
- Contribution and results: Structure-aware chunking gives the highest ROUGE-L, BLEU and BERTScore F1. Semantic chunking preserves tables better: integrity is 0.709 versus 0.535 for structure-aware chunking. No configuration wins every metric.
- Limitations: One institution, few queries and one generation per query limit generalisation. Heuristic table detection misses some layouts.
- Project application: Compare fixed, recursive and structure-aware chunks on office documents. Check whether values remain associated with their row labels and units, separately from retrieval accuracy.

### 3.2.3 Aljabri and Alnajim 2026 - Evidence-aware audit RAG [3]

- Methodology: Using design science, the authors develop ER2-RAG with risk-sensitive retrieval, claim-to-evidence mapping, validation and audit trails. Evaluation compares baseline RAG and component ablations on 25 de-identified audit questions.
- Contribution and results: Reported evidence coverage increases from 68.4% to 92.1%, while unsupported claims decrease from 18.7% to 4.3%. Removing evidence attribution weakens performance.
- Limitations: The evaluation is small and does not establish performance across diverse corpora or resistance to adversarial documents. Implementation details are insufficient for exact reproduction of the reported scores.
- Project application: Attach document and page references to answers, check citation support and record which sources informed an output. Unsupported requests should trigger an explicit insufficient-evidence response.

### 3.2.4 Lee et al. 2026 - Tool-augmented LLM agents [4]

- Methodology: Four API-based models are evaluated with and without metadata tools on 1,900 traditional Asian medicine benchmark items. A non-agentic RAG condition is additionally tested for GPT-5-mini. Tool iterations are bounded.
- Contribution and results: GPT-5-mini improves from 75.4% to 85.6% on TCMBench term-related items. Other model/task combinations show smaller gains or declines, demonstrating that tool access does not ensure better answers.
- Limitations: The medical benchmark does not test office workflows or local deployment. Answer-extraction failures reduce paired samples, and reported nominal significance does not survive the stated multiple-comparison correction.
- Project application: Compare the same local model without tools, with fixed retrieval and with agent-selected tools. Measure tool-call correctness, task success and latency; enforce execution limits.

### 3.2.5 Tugambayeva and Sakhipov 2026 - RAG versus fine-tuning [5]

- Methodology: Aviation question generation compares Mistral-7B with FAISS retrieval against LoRA-tuned T5-small. Evaluation uses 116 test examples; the authors also assess 15 questions per approach for educational quality.
- Contribution and results: RAG produces broader terminology coverage and more diverse questions. T5 achieves better reference similarity and faster inference. This illustrates why similarity scores alone can miss practical quality differences.
- Limitations: Model sizes differ substantially, so the study cannot isolate the effect of RAG versus fine-tuning. Human assessment is small and performed by the authors.
- Project application: Start with RAG for changing organisational documents. Evaluate factual correctness and task usefulness alongside automated metrics. Treat fine-tuning as a later option for a clearly defined task.

### 3.2.6 Kapizov and Kumar 2026 - Efficient adaptation of small models [6]

- Methodology: SmolLM2-1.7B is fine-tuned with LoRA for two mental-health text classification datasets. Full held-out evaluation is separated from comparisons with zero-shot GPT models on matched 200-example subsets.
- Contribution and results: Updating approximately 0.5% of parameters produces a competitive task-specific classifier. Matched comparisons favour the small model more clearly on binary stress detection than on multiclass classification.
- Limitations: Classification results do not demonstrate document generation or tool-calling ability. Comparators are zero-shot models, and fine-tuning uses an A100 GPU. Calibration and external-domain generalisation remain limitations.
- Project application: Consider a small adapted model for intent classification or task routing if sufficient labelled office data become available. Benchmark training and inference costs separately.

### 3.2.7 Tleubayeva and Shomanov 2024 - Kazakh-language QA [7]

- Methodology: The paper compares multilingual and Kazakh models, discussing adapter tuning and data augmentation. Its results combine previously published Kazakh QA scores with additional AYA and GPT results.
- Contribution and results: The comparison highlights language-specific adaptation and differing effects of fine-tuning across models.
- Limitations: Reused and newly reported results are mixed, and the abstract's model ranking conflicts with Table 3. The reported ranking is therefore not a reliable basis for choosing the project's model. Office tasks and cross-language retrieval are not evaluated.
- Project application: If Kazakh support is included, create a separate Kazakh office-task test set and inspect terminology and retrieval errors. Do not infer local language quality from English benchmarks or these rankings alone.

### 3.2.8 Yao et al. 2024 - LLM security and privacy survey [8]

- Methodology: A literature survey of 281 papers groups beneficial security applications, offensive uses, and model vulnerabilities with their defences.
- Contribution and results: It organises risks such as prompt injection, data poisoning and training-data extraction, and distinguishes architectural, training-time and inference-time protections.
- Limitations: It synthesises prior research rather than validating one deployed assistant. Its 2024 coverage does not establish the security of later agent systems or the proposed office application.
- Project application: Use the taxonomy to define threats and protection boundaries. Local hosting addresses dependence on external inference, while access permissions, restricted file operations and output checks still require implementation and testing.

### 3.2.9 Ait Hsine and Arabo 2026 - Privacy-aware security middleware [9]

- Methodology: Deterministic rules and a locally fine-tuned small model screen prompts before optional external analysis. Evaluation uses 120 prompts; routed outcomes are reconstructed from recorded component decisions.
- Contribution and results: The routed configuration reports 95.83% detection accuracy. However, two of 40 sensitive prompts still reach the external layer, and the false-positive rate is 12.5%.
- Limitations: These results do not establish guaranteed containment. Indirect and multi-turn attacks are unrepresented, and live routed end-to-end latency is not measured.
- Project application: Adapt local screening and separate confidentiality checks from attack detection. Keep inference and security analysis local for the project's no-external-AI requirement; detector approval must not authorise external forwarding.

### 3.2.10 Gulyamov et al. 2026 - Prompt injection in agent systems [10]

- Methodology: A review of 45 sources from 2023-2025 classifies attack vectors, agent vulnerabilities and defensive approaches. It proposes the five-layer PALADIN framework.
- Contribution and results: The framework combines input validation, context separation, behavioural monitoring, tool authorisation and sandboxing, and output verification. It connects untrusted retrieved content with risks from privileged tool access.
- Limitations: PALADIN is a proposed synthesis, not an end-to-end defence validated through a new controlled deployment experiment. The review does not establish an attack-prevention rate for the office project.
- Project application: Treat document contents as untrusted data, restrict tool permissions and require explicit approval for email sending. Test malicious instructions embedded in retrieved documents as well as direct user prompts.

## 3.3 Synthesis and research gap

The studies support three connected design choices. Document QA needs retrieval plus evidence checks, because finding a relevant passage does not establish answer correctness [1-3]. Tool use needs its own evaluation, because additional retrieval or autonomous calls can help some tasks and harm others [4-6]. Confidentiality and action control need application-level enforcement alongside model safeguards [8-10]. Multilingual capability also requires separate validation when included [7].

Within this reviewed collection, no study evaluates the combined workflow required here: a self-hosted assistant that retrieves organisational knowledge, creates or edits Office artifacts, preserves original files and sends email only after user approval. The gap is therefore integration and evaluation in the intended office setting. It does not imply that these individual techniques or comparable products do not already exist.

The project's proposed contribution is an evaluated implementation combining these capabilities under controlled deployment. It does not require a new language model or a claim of universal superiority over existing systems.

## 3.4 Implications for implementation and evaluation

| Project decision | Literature basis | What the project must verify |
| --- | --- | --- |
| Local RAG with source references | [1-3] | Answer correctness, citation support and behaviour when evidence is missing |
| Preserve document and table structure | [2] | Row-label and value associations, units and numerical accuracy |
| Bound and validate tool execution | [4, 10] | Correct tool selection and arguments, execution limits and error recovery |
| Use RAG before task-specific adaptation | [5, 6] | Quality and resource use on actual office tasks; need for labelled training data |
| Keep processing local and authorise sensitive actions | [8-10] | External AI traffic, file-access boundaries, prompt injection resistance and email approval enforcement |

Evaluation should use identical tasks and input files for the compared configurations. Retrieval quality, answer accuracy, artifact correctness, task completion, latency and resource use should be measured separately. Repeated runs should capture variability. Model-only, fixed-RAG and tool-enabled configurations can isolate the contribution of retrieval and agent orchestration [2, 4]. These are proposed project experiments, not completed results.

## 3.5 Books and legal framework

### 3.5.1 Technical books

The following descriptions use the publisher's public summaries and tables of contents. They identify useful engineering topics without claiming a full-text review or treating the books as experimental evidence.

#### Addy Osmani - Agentic Engineering [11]

The available preview discusses software development with coding agents: setting goals, providing context and tools, supervising execution, and verifying results. Its outline includes agent execution environments, memory, context engineering, specifications and observability.

For this project, these topics can guide the separation of the language model from the execution layer, bounded task loops, clear tool permissions and reviewable outputs. The book's software-development focus requires adaptation to office tasks. The publisher marks the contents as not final and lists September 2027, so this source is cited as an online preview, not a completed 2026 edition.

#### Chip Huyen - AI Engineering [12]

The book explains how to build applications using foundation models. Its contents cover model selection, evaluation, prompt engineering, RAG, agents, fine-tuning, dataset preparation, inference optimisation and application architecture.

It can help structure the assistant's development: begin with a measurable office use case, select a suitable local model, add retrieval and tools where needed, and evaluate components as well as complete workflows. Its evaluation and optimisation topics are relevant to task correctness, latency and resource use. These are implementation guides; the project's performance and privacy properties still require its own tests.

### 3.5.2 Kazakhstan legal framework

The supplied legal summary selects eleven priority articles across three acts. The tables retain that selection. Legislative provisions and proposed engineering responses are distinguished: the acts do not prescribe a particular LLM, RAG framework or software stack. Applicability depends on the deployment, data and functions involved.

#### Law on Personal Data and Their Protection [13]

Law of 21 May 2013 No. 94-V. The selected provisions below follow the accompanying legal summary and its statutory references.

| Article | Relevant provision | Proposed project response |
| --- | --- | --- |
| 11 | Confidentiality of personal data | Restrict access to uploaded files, conversations and derived data; prevent unauthorised disclosure. |
| 12(2) | Personal-data storage in a database and/or digital object located in Kazakhstan | Locate applicable personal-data storage in Kazakhstan, including relevant backups and derived stores. |
| 16 | Conditions governing cross-border transfers | Assess external services and transmission paths before transferring personal data. |
| 22 | Duties concerning personal-data protection | Implement authorisation, access isolation and documented protective measures. |

Local hosting is a design choice supporting data control. It does not establish compliance by itself, and cross-border transfer is not an unconditional prohibition.

#### Law on Artificial Intelligence [14]

Law of 17 November 2025 No. 230-VIII.

| Article | Relevant provision | Proposed project response |
| --- | --- | --- |
| 8 | Accountability and continuing oversight | Assign responsibilities; provide review and intervention controls. |
| 10 | Data protection and confidentiality | Prevent unlawful processing and unauthorised access. |
| 16 | Users' rights and duties | Explain usage terms and relevant outputs; enforce access rights. |
| 17 | Risk, autonomy and usage regimes | Assess deployment classification; constrain autonomous actions. |
| 18 | Lifecycle risk management | Maintain and update a risk register; test mitigations. |

Article 17(4) defines local AI systems through operation and training within the operator's infrastructure without public telecommunications connectivity. Self-hosting alone does not establish that status. Email connectivity must be considered when defining system boundaries. User approval before sending is the project's chosen control, not a verbatim statutory email requirement.

#### Digital Code of the Republic of Kazakhstan [15]

Code of 9 January 2026 No. 255-VIII.

| Article | Relevant provision | Proposed project response |
| --- | --- | --- |
| 16 | Digital data and special rules for protected information | Apply the relevant confidentiality and personal-data rules to files and extracted content. |
| 38 | Owners' and holders' duties to protect rights and prevent unlawful access, copying, alteration or deletion | Enforce permissions, preserve originals and control file operations. |

Together, these provisions support requirements for controlled data access, appropriate storage, human oversight and risk management. Their translation into architecture and tests belongs in Parts 6, 7 and 9; the present review identifies the legal basis without asserting completed compliance.

## References

[1] Muludi, K., Fitria, K. M., Triloka, J., and Sutedi. (2024). Retrieval-Augmented Generation Approach: Document Question Answering using Large Language Model. International Journal of Advanced Computer Science and Applications, 15(3).

[2] Khalib, S. S. I., Abd Rahman, A. H., and Zakaria, L. Q. (2026). Effective Chunking for Retrieval-Augmented Generation over Structured Institutional Documents. International Journal of Advanced Computer Science and Applications, 17(8).

[3] Aljabri, T. F., and Alnajim, M. A. (2026). An Evidence-Aware and Risk-Sensitive Retrieval-Augmented Generation Framework for Internal Auditing. International Journal of Advanced Computer Science and Applications, 17(3).

[4] Lee, W.-Y., et al. (2026). Benchmark Evaluation of a Tool-Augmented Large Language Model Agent Using Traditional Asian Medicine Metadata. Applied Sciences, 16, 3377. [DOI](https://doi.org/10.3390/app16073377).

[5] Tugambayeva, A., and Sakhipov, A. (2026). AI-Based Question Generation for Aviation Training: Comparing Retrieval-Augmented Generation and Fine-Tuned Models. Scientific Journal of Astana IT University, 25. [DOI](https://doi.org/10.37943/25VYNZ9998).

[6] Kapizov, D., and Kumar, P. (2026). Fine-Tuning Small Language Models for Mental Health Text Classification: A Parameter-Efficient Approach Outperforming Zero-Shot Large Language Models. Scientific Journal of Astana IT University, 26. [DOI](https://doi.org/10.37943/XSAJ1029).

[7] Tleubayeva, A., and Shomanov, A. (2024). Comparative Analysis of Multilingual QA Models and Their Adaptation to the Kazakh Language. Scientific Journal of Astana IT University, 19. [DOI](https://doi.org/10.37943/19WHRK2878).

[8] Yao, Y., Duan, J., Xu, K., Cai, Y., Sun, Z., and Zhang, Y. (2024). A Survey on Large Language Model (LLM) Security and Privacy: The Good, The Bad, and The Ugly. High-Confidence Computing, 4, 100211. [DOI](https://doi.org/10.1016/j.hcc.2024.100211).

[9] Ait Hsine, A., and Arabo, A. (2026). A Privacy-Preserving Middleware Architecture for Detecting Prompt Injection and Sensitive Data Exposure in Large-Language-Model Interactions. Electronics, 15, 3554. [DOI](https://doi.org/10.3390/electronics15163554).

[10] Gulyamov, S., et al. (2026). Prompt Injection Attacks in Large Language Models and AI Agent Systems: A Comprehensive Review of Vulnerabilities, Attack Vectors, and Defense Mechanisms. Information, 17, 54. [DOI](https://doi.org/10.3390/info17010054).

[11] Osmani, A. Agentic Engineering. O'Reilly Media. Online preview; contents marked not final, publisher lists September 2027. [Publisher description and contents](https://www.oreilly.com/library/view/agentic-engineering/0642572392291/). Accessed 21 October 2026.

[12] Huyen, C. (2024). AI Engineering. O'Reilly Media, online edition listed December 2024. [Publisher description and contents](https://www.oreilly.com/library/view/ai-engineering/9781098166298/). Accessed 21 October 2026.

[13] Republic of Kazakhstan. (2013). Law on Personal Data and Their Protection, 21 May 2013, No. 94-V. Articles 11, 12, 16 and 22. [Official text](https://adilet.zan.kz/rus/docs/Z1300000094). Discussion follows the supplied legal summary; the applicable consolidated version must be confirmed for deployment.

[14] Republic of Kazakhstan. (2025). Law on Artificial Intelligence, 17 November 2025, No. 230-VIII. Articles 8, 10, 16, 17 and 18. [Official text](https://adilet.zan.kz/rus/docs/Z2500000230).

[15] Republic of Kazakhstan. (2026). Digital Code of the Republic of Kazakhstan, 9 January 2026, No. 255-VIII. Articles 16 and 38. [Official text](https://adilet.zan.kz/rus/docs/K2600000255).
