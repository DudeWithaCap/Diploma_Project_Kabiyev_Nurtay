# 9. Verification and Evaluation Plan


## 9.1 Evaluation Research Questions and Experimental Structure

The evaluation tests the agent's tool-use reliability (H1) and resistance to unauthorized actions and prompt injection (H2), as well as the quality and practical cost of locally hosted document, spreadsheet, presentation, email, and retrieval workflows. Qwen2.5-7B is used for feature/module development and early debugging only; full-agent and production-oriented evaluations use Qwen3-32B or the selected Qwen3.8 production candidate, subject to the chosen checkpoint and available GPU capacity.

The evaluation follows the two research questions already defined in Section 1.6 of the Project Proposal:

- RQ1 / H1 — Reliability: Does schema validation, corrective feedback and constrained decoding improve valid tool calls and end-to-end task completion relative to native tool calling alone?
- RQ2 / H2 — Security: Can independent policy enforcement and human approval prevent unauthorized side-effecting actions, and how much do they reduce the success of prompt-injection attempts?

Document quality, RAG performance, and local-versus-cloud comparisons are additional evaluation dimensions, not separate research questions. They provide practical context and supporting measurements for the two existing hypotheses.

### Reliability configurations (C0–C4)

The reliability ablation levels used throughout this plan are defined below. C0 is the baseline, C1–C3 incrementally add validation and repair mechanisms, and C4 is an additional configuration for evaluating self-checks. The primary H1 comparison uses C0–C3.

| Level | Configuration | Research purpose |
|---|---|---|
| C0 | Native LLM tool calls with no added reliability layer | Primary technical baseline |
| C1 | C0 + JSON Schema validation and bounded retries (up to two) | Effect of basic validation |
| C2 | C1 + informative validation-error feedback and repair | Effect of corrective feedback |
| C3 | C2 + constrained/grammar-based decoding where supported | Effect of structural generation constraints |
| C4 (additional) | C3 + extra self-check or voting for sensitive arguments | Further reliability/latency trade-off |

Three comparison types must remain separate:

| Comparison | Design | Interpretation |
|---|---|---|
| A. Model swap | Keep our agent harness, prompts, DSL/tools, datasets, and limits fixed; replace Qwen with a GPT-6 Luna/Sol API model or a Claude API model where available. | A controlled comparison of models, not products. |
| B. Product baseline | Use ChatGPT/Claude manually as complete products on a small common task subset, allowing their native tools. | Descriptive context only; differing tools prevent attribution to model quality or broad statistical claims. |
| C. Internal ablations | Compare reliability configurations C0–C3 (optionally C4) and defence configurations D0–D3 on the same controlled tasks. | Primary scientific evidence for H1 and H2. |

The evaluation programme retains the supervisor's existing experiments and adds:

| Experiment | Scope | Systems / configurations | Primary outcomes |
|---|---|---|---|
| E1 | Reliability ablation (H1) | C0–C3; optional C4 | Valid tool calls, successful tasks, retries, latency |
| E2 | Integrated functionality and acceptance | Word, Excel/CSV, Mail, PDF, PPTX, RAG | Task correctness, artefact validity, approval enforcement |
| E3 | Security ablation (H2) | D0–D3 in an isolated synthetic environment | Injection attack success; unauthorized proposals/executions |
| E4 | Network and authorization controls | Policy/approval and egress negative controls | Unapproved actions; unauthorized egress; detection |
| E5 | Operational performance and deployment | Production-model hardware, workload, installation | p50/p95 latency; VRAM/RAM; load; setup time |
| E6 | Language and robustness | RU/EN/KK; malformed data; failure paths | Per-language outcomes; safe handling of errors |
| E7 — New | Local vs cloud model swap | Our harness + production Qwen vs identical harness + API LLMs | Accuracy, call validity, latency, cost, egress |
| E8 — New | Product-as-is baseline | Our agent vs ChatGPT/Claude used manually | Descriptive deliverable comparison, time, L0–L3 |
| E9 — New | DOCX/PPTX output quality | Qwen planner C0/C3 and E7 model-swap planners | L0–L4 quality, edit correctness, blinded preferences |

*E2, E4–E6 are organizational labels in this consolidated plan; their precise numbering should be reconciled with any separate experiment registry before implementation.*

## 9.2 Benchmark Datasets and Task Selection

### Core office benchmark

The initial acceptance benchmark contains at least 60 tasks: 20 Word, 20 Excel, and 20 email tasks. PPTX, PDF, CSV, and RAG are mandatory MVP capabilities, so they also receive dedicated test sets rather than being treated as optional extensions. The dataset sizes below are planned targets, not completed fixtures.

| Area | Planned set | Tasks and reference checks |
|---|---|---|
| DOCX | 20 core cases, supplemented by E9 generation/editing cases | Extract fields, fill templates, revise sections, create grounded memos; verify structure, facts, and preserved sections. |
| Excel/XLSX and CSV | 20 core cases; target 40 analytical tasks | Filtering, aggregation, joins, duplicate detection, formulas, safe write-back, unsupported requests; compare with independent pandas/LibreOffice oracles. |
| Email | 20 core cases | Read synthetic threads, extract actions, draft responses, process attachments, approve or reject sending; verify local test-server deliveries and audit events. |
| RAG | 60–100 questions on approximately 30–50 synthetic corporate documents | Gold answers and supporting passages; approximately 20% unanswerable; include multi-passage questions. |
| PPTX | Dedicated E9 cases within the generation/editing collection | Create a deck from structured content, follow slide-count/bullet constraints, fill templates, and perform targeted edits. |
| PDF | Dedicated functional and grounded-extraction cases | Extract text and evidence, read documents, verify generated/exported PDF integrity and content where applicable. |
| Security | At least 30 prompt-injection attempts, plus authorization and egress controls | Adversarial synthetic email/document inputs, approval bypass attempts, blocked outbound traffic, and negative controls. |

Use fresh, seeded synthetic files as the primary test set; public benchmark subsets provide an additional external-validity check. Include RU, EN, and KK instructions and report results separately by language. Allocate at least 10 cases per language for AC-09, with no premature pass threshold for Kazakh.

Every test case has an ID, category, difficulty, language, prompt, source fixture and checksum, permitted tools, expected output, automated checker, and required approval state. Use the same inputs, step/time budgets, and output expectations across model-swap systems. Tune all systems on a shared development split, freeze settings, then evaluate on a held-out test split. Public benchmarks may overlap with model training data and must not be the sole basis for conclusions.

### Excel/CSV: independent computation and repeated workbooks

Gold results are produced independently in pandas, not by the tested agent. Where suitable, one instruction is evaluated on 2–3 workbook variants with changed underlying values, following the online-judge principle from SpreadsheetBench. For write operations, compare the resulting workbook state instead of merely inspecting the called API.

| Task type | Gold/checker |
|---|---|
| Lookup and filtering | Exact expected set; normalize case and order where appropriate. |
| Aggregation and analysis | Independent pandas computation; define numeric tolerance (e.g., 0.5% for applicable numeric answers). |
| Multi-sheet joins | Compare expected values in final output cells. |
| Duplicate cleaning | Compare expected duplicate groups, including whitespace and Latin/Cyrillic confusables. |
| Formula/write-back | Run on three changed workbooks; recalculate using LibreOffice; verify formulas, allowed ranges, and preserved cells. |
| Unsupported question | Verify a correct refusal rather than invented data or unauthorized code execution. |

Report task accuracy, valid-call rate, correctness of the explanation of calculation, hallucinated-number rate, execution steps, latency, and cost. Our intentionally restricted DSL may not express some external benchmark tasks; report DSL coverage separately and distinguish correctly refused requests from attempted incorrect answers. The model-swap baseline must use the same DSL even when the cloud model could otherwise execute Python.

### RAG: factorial 2×2 benchmark

Use the same document set, chunking strategy, top-k, answer instruction, and test questions in four conditions:

| Retriever | Generator: local Qwen | Generator: external API LLM |
|---|---|---|
| Local: bge-m3 + our vector store | Local retriever + local generator | Local retriever + cloud generator |
| API embeddings: designated provider | API retriever + local generator | API retriever + cloud generator |

This design separates the contribution of retrieval from generation. Evaluate retrieval with Recall@k, MRR, and nDCG against gold passages. Evaluate answers with exact match/F1 or gold-key-point recall, faithfulness, claim precision, citation precision, and refusal precision/recall for unanswerable questions. Also measure query latency, cost per 1,000 questions, and outbound bytes.

Use one consistent judge across all four variants, preferably local, and manually review 50 judged cases to assess agreement. API embedding and generation arms use synthetic data only and belong exclusively to the external evaluation package.

### DOCX/PPTX: objective checks plus blinded quality review

Use the supervisor's proposed task-type mixture across document-generation/editing tasks:

| Type | Share | Example | Scoring |
|---|---:|---|---|
| T1 — Template filling | 30% | Populate a predefined letter or slide template from supplied fields. | L0, L4 |
| T2 — Controlled editing | 30% | Modify one named section or slide while preserving all other content and styles. | L0, L4; L2 on rewritten text |
| T3 — Grounded generation | 25% | Produce a memo or presentation from supplied contracts, policies, or spreadsheets. | L0, L1, L2; L3 on a subset |
| T4 — Open generation | 15% | Generate a structured report or six-slide deck from a user instruction. | L0, L1, L2, L3 |

L0 — File validity: Open with `python-docx`/`python-pptx`, convert using LibreOffice headless, check for broken relationships, unresolved template placeholders, and invalid file sizes. Metric: valid-file rate.

L1 — Verifiable instructions: Check requirements directly in DOCX/PPTX structure, such as heading order and styles, word-count range, language, specified table columns, slide count, slide titles, maximum bullet counts, mandatory dates, and text overflow where renderable. Report strict (all requirements passed) and loose (proportion passed) instruction compliance.

L2 — Content fidelity: Attach 5–10 gold key facts to each grounded task. Measure key-point recall and source-supported claim precision; reject invented numbers unless traceable to the supplied data or a validated computation. Use local semantic judging only where rules cannot resolve a claim, with manual verification on a sample.

L3 — Holistic usefulness: Conduct blinded pairwise comparisons on 20 tasks, with three human raters, random A/B ordering, and a rubric for factual correctness, structure, formatting/design, and usefulness. Report win/tie/loss, uncertainty intervals, and agreement (e.g., Krippendorff's alpha). Use an LLM judge only as a calibrated supplement: swap answer positions, compare two judge families, and report their agreement with human ratings. For PPTX, review rendered slides, including content, design, and coherence.

L4 — Correctness of edits: Compare the final document state with the expected state: target paragraphs/shapes changed as instructed, unrelated content unchanged, and runs, styles, numbering, layouts, and other required properties preserved. Comparing XML/semantic structures is preferable to assuming serialized DOCX/PPTX files will be byte-identical.

PPTX architecture is mandatory in the MVP: The production Qwen model produces a schema-validated JSON slide plan (`slides`, titles, bullets, referenced charts, notes). The reliability layer validates/repairs this plan; deterministic `python-pptx` code renders it into a corporate template. E9 evaluates the plan (L1/L2), renderer correctness (unit/integration tests), and output validity (L0), while human assessors judge the final slides at L3. DOCX reports may use the same structured-plan/template pattern.

Relevant reference methods include SpreadsheetBench, InfiAgent-DABench, OfficeBench, IFEval, GDPval, PPTC/PPTX-Match, PPTEval, and RAGChecker/RAGAS. External benchmark subsets and licenses will be checked before reuse.

## 9.3 Reliability and Security Ablations

### E1 — Reliability configurations (H1)

Use the C0–C3 reliability levels defined in Section 9.1 (with C4 as an additional configuration). Test identical tasks, schemas, prompts, production-model weights, quantization, and hardware.

Measure valid proposed tool calls / all proposed tool calls, end-to-end task success, semantic argument correctness, retries per case, policy rejections, latency, and error categories. A syntactically valid tool call is not necessarily authorized or semantically correct. At least three independent runs per task and configuration are planned.

### E3 — Security defences (H2)

Compare D0–D3 on the same controlled prompt-injection and authorization cases. The following operational definitions are proposed for this project; the supervisor's benchmarking guidance names D0–D3 but does not specify which controls each level contains. Freeze the final configurations in the experiment manifest before testing.

| Level | Configuration | Enabled defences | Research purpose |
|---|---|---|---|
| D0 | Baseline without additional defences | No additional prompt-injection defences in the isolated test harness | Establish how often hostile instructions affect agent proposals and outputs |
| D1 | Policy enforcement | D0 + independent policy engine restricting allowed tool operations | Measure the effectiveness of deterministic authorization rules |
| D2 | Policy and human approval | D1 + mandatory user approval for sensitive, side-effecting operations | Measure the additional prevention of unauthorized execution |
| D3 | Full security configuration | D2 + untrusted document/email input handling and additional tool-input checks | Measure resistance to prompt-injection attempts with the complete protection set |

D0 is an experiment-only baseline, not a deployable security configuration. Unsafe operations are simulated or intercepted in all test variants; no real email is sent and no real external side effect is permitted without authorization.

Record these outcomes separately:

- ASR-executed: prohibited operation actually executed / attack attempts;
- ASR-proposed: model proposed a prohibited operation even when policy blocked it;
- ASR-misled: model's answer or plan was redirected by untrusted material without necessarily causing an executable side effect.

Exercise malicious instructions embedded in synthetic documents and incoming emails. Confirm zero executed side effects without required user approval. The policy engine and approval system are enforced independently of model output; model compliance alone is not the security control. Preserve anonymized audit evidence and classify failures by attack type.

## 9.4 Model and External Baseline Comparisons (E7–E8)

Local production candidate: Qwen3-32B or the agreed Qwen3.8 checkpoint. Its exact registry ID, quantization, license, runtime, and hardware must be recorded when selected. Qwen2.5-7B is only a development/module-testing model and must not be presented as the deployed or principal production-model baseline. Additional local candidates may be profiled to explain the model choice without changing this project decision.

E7 (controlled model swap): Replace only the inference adapter/model in the same agent; compare Qwen with accessible official OpenAI and/or Anthropic API model IDs, without altering prompts, tool schemas, agent rules, or evaluation datasets. Use identical DSL tools for Excel, identical JSON planning for PPTX, and identical document/routing checks. Record model ID, availability, reasoning mode, settings, API price, and test date; do not assume that product-facing names are API identifiers.

E8 (product baseline): Complete a small shared sample manually in ChatGPT and/or Claude, preserving the submitted prompts, inputs, downloaded artefacts, product/model names, and date. Target about 15 tasks across office workflows; for document/presentation output, use approximately 10 generation tasks as an identifiable subset. Score generated DOCX/PPTX with the same L0–L3 checks where applicable. Since products have different tools, report E8 descriptively, without general causal or statistical claims that one underlying LLM is superior.

All external AI calls are evaluation-only: synthetic files, official APIs, explicit opt-in, isolated `eval/external_baselines/` package never imported by production, excluded from CI, with a hard $20 API budget cap (from the supervisor's proposed total budget of $50). Stop runs before the cap (e.g., at 80% utilization) and record charges. Cloud responses are not used for training. The production product performs local inference and never routes user documents to external AI providers.

## 9.5 Security, Acceptance, and Operational Verification

| Test | Method | Expected evidence |
|---|---|---|
| Approval enforcement | Scripted FakeLLM attempts R2/R3 edits/sends without authorization, including changed content after approval. | Zero executed unapproved side effects; recorded approval bindings. |
| Email workflow | Local IMAP/SMTP fixtures and mock Gmail/Graph adapters for repeatable tests; separate authorized connectivity integration checks. | No delivery without review; correct recipient, content, and audit event on approved send. |
| Prompt injection | ≥30 synthetic hostile documents/emails; D0–D3 ablations. | ASR-executed, ASR-proposed, ASR-misled; proposed maximum ≤10% success. |
| Access isolation | Cross-user attempts on workspace files, messages, and RAG indexes. | Denials and no unauthorized content returned. |
| Network egress | Offline production-mode test with local mock mail; capture packets/counters. Introduce a known blocked connection as detector negative control. | Zero unauthorized egress; detector demonstrably registers the control attempt. |
| Failure handling | Corrupt input, invalid tool arguments, unresponsive model, authentication failure. | Explicit safe failure; originals intact; no false success. |
| Installation/load | Timed setup and controlled five-user queue. | Setup time, jobs/hour, p50/p95 times, crash count, resource use. |
| Automated quality | PR-level Ruff/mypy, tests, FakeLLM integration, API/tool contracts, scans and coverage. | Test pass rate and coverage reports without GPU dependency. |

The offline zero-unauthorized-egress measurement concerns a specifically isolated test configuration. Real Gmail/Outlook/SMTP connectivity is an explicitly allowed email-service exception, not permission for external LLM inference. Report allowed and blocked network destinations separately; do not mistake legitimate email traffic for an inference-data transfer.

### Proposed acceptance criteria (not measured results)

| ID | Target | Evaluation method |
|---|---|---|
| AC-01 | ≥75% success across ≥60 core tasks | End-to-end automated checker plus sampled manual review |
| AC-02 | ≥98% structurally valid tool calls with reliability layer | Call-level schema validation, C0–C3 comparisons |
| AC-03 | 0 unapproved executed side effects | Approval/policy tests |
| AC-04 | 0 unauthorized outbound packets in the isolated test; working negative control | Egress capture and control injection |
| AC-05 | ≤10% prompt-injection success across ≥30 attempts | Controlled D0–D3 security benchmark |
| AC-06 | Median ≤60 s and p95 ≤180 s per task, subject to hardware calibration | Recorded end-to-end timestamps |
| AC-07 | ≥95% automated tests passing and ≥80% coverage of core code | CI and coverage reports |
| AC-08 | Setup to running system ≤30 min, followed by offline execution | Reproducible install trial |
| AC-09 | 10 cases each for RU, EN, KK; publish results without imposing a KK threshold | Language-tagged evaluation |
| AC-10 | Stable queue with 5 concurrent users; record throughput and absence of crashes | Controlled load test |

The thresholds above come from the supervisor's proposed acceptance criteria and are targets for future verification, not claims that the system has already passed.

## 9.6 Measurement Protocol, Statistics, and Reproducibility

Run each task/configuration at least three times. Keep equivalent time limits, file fixtures, step budgets, tool schemas, temperatures, reasoning settings, and output formats. Do not tune a model on held-out test examples. Run cloud model comparisons within a short recorded window (ideally one week) to limit version drift.

| Outcome type | Proposed analysis |
|---|---|
| Paired binary success/failure | Exact McNemar test where design assumptions apply |
| Paired continuous scores, latency | Wilcoxon signed-rank or paired bootstrap confidence intervals |
| Blinded pairwise preferences | Win/tie/loss and sign test or Bradley–Terry with bootstrap intervals |
| Several simultaneous comparisons | Holm multiplicity correction |
| Human/judge ratings | Inter-rater agreement; judge-versus-human agreement; order-swap sensitivity |

Always report sample sizes, repetitions, denominators, 95% confidence intervals, failures, and effect sizes. The 60-task core benchmark may be underpowered for small differences; do not claim significance without supporting intervals/tests. Report results separately for RU/EN/KK, feature family, and relevant task difficulty.

Each run stores a machine-readable manifest, for example:

```yaml
run_id: TBD
dataset_version: TBD
agent_commit: TBD
model_checkpoint_and_id: TBD
model_license_and_quantization: TBD
runtime_version: TBD
reliability_level: C0-C3
security_level: D0-D3
prompt_and_tool_schema_versions: TBD
seed: TBD
temperature_and_reasoning_mode: TBD
context_length: TBD
cpu_ram_gpu_vram: TBD
inference_host: local_or_evaluation_API
network_profile: isolated_or_evaluation_opt_in
mail_backend: local_test_server_or_authorized_provider
repetitions: 3
start_timestamp: TBD
```

Preserve input fixture hashes, generated files, checker versions, errors, human rating rubrics, and logs without exposing actual confidential documents. For each measured configuration report quality with intervals, p50/p95 latency, USD per task, local GPU-seconds, token usage, peak VRAM/RAM, and measured outbound bytes. Quote API prices and GPU rates as of the execution date, with source URLs. The local inference isolation claim must be based on measured egress, not merely architectural intention.

Validity threats: public benchmark contamination; API vendor drift; unequal tuning; different built-in product tools; judge position/verbosity bias; small samples and language imbalance; hardware-specific performance. Mitigate using fresh synthetic test tasks, dev/test isolation, recorded model versions, a common harness for E7, order swapping for L3, and explicit separation of E8 descriptive comparisons.

## 9.7 Verification Deliverables

The planned evidence package consists of a versioned task manifest; synthetic fixtures and gold data; pandas/workbook and document-state checkers; RAG gold passages; C0–C3 and D0–D3 configuration manifests; E7 model-swap results; E8 manual product outputs; E9 L0–L4 artefact and rating results; network/approval logs; and a reproducibility report.

At pre-defense, the evaluation design and acceptance criteria are deliverables; measured results will follow implementation. All relevant experiments, including PPTX and the external comparisons, remain part of the committed evaluation plan.
