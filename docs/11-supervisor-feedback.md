# Supervisor Review

**Review date:** 24 October 2026  
**Supervisor:** Almas Ospanov, Senior Lecturer, School of Software Engineering, Astana IT University
**Team:** Adilet Kabiyev SE-2415 and Aibek Nurtay SE-2405


## Supervisor Decisions

| ID | Decision |
| --- | --- |
| D-1 | **MVP priorities:** Must — DOCX, XLSX with CSV handled by the same tabular tool, and email through IMAP/SMTP against a local test server. Should — read-only PDF text extraction and basic local RAG. Could — PPTX generation using structured outlines and templates, and LoRA. Outside the current scope — Gmail API, Microsoft Graph, and OCR. |
| D-2 | **Project tracking:** remain on YouTrack at least until pre-defense 2. Reconsider Jira/Confluence only afterward and for a concrete need, preserving task-to-PR traceability. |
| D-3 | **GPU use and deployment:** rented/cloud GPUs are allowed for development and evaluation on synthetic data only. Record provider, hours, and cost. Combined API and GPU spending must remain within $50, including the separate $20 API cap. Minimum hardware requirements come from the model bake-off. |

## New Tasks Assigned in Review 2

### SR-01 — Complete the Requirements Specification

**Owner:** Aibek Nurtay; **reviewer:** Adilet Kabiyev  
**Deadline:** 6 November 2026  
**Document:** `docs/06-requirements-specification.md`

- Add a MoSCoW priority and a source to each requirement: technical specification, interview, literature, or assumption. Align priorities with the agreed scope.
- Map the original specification's requirement IDs to document 06, explaining every merged or omitted item.
- Define the administrator role, or state that administration is deployment configuration only. Assign responsibility for allow-lists, model selection, retention, and audit export.
- Add H3 to the research-to-requirements mapping: model quality on accessible hardware, with RU/EN measurements and separately reported KK results.
- Define expected daily workload and derive the load-test profile from it.

**Completion criteria:** every Must has a priority, source, acceptance criterion, and verification method, the ID mapping exists.

### SR-02 — Correct the Architecture and Diagrams

**Owners:** Aibek Nurtay — context/use case; Adilet Kabiyev — component diagram/description  
**Deadline:** 6 November 2026  
**Locations:** `docs/07-architecture.md`, `diagrams/`

- Add vector PDF or EPS exports..
- Show the User, Administrator if applicable, and the organization's mail server as a secondary actor. Explain explicitly if there is no administrator actor.
- Replace SQLite with PostgreSQL.
- Show the allow-listed SMTP/IMAP route to the organization's mail server; mark other external routes as blocked.
- Fix the incomplete sentence in section 7.3 and credit G-07 in the adapted component diagram's caption.
- Add FR/NFR IDs to every component-table row in section 7.6.

**Completion criteria:** diagrams, exports, requirements, technology choices, and agreed scope are consistent.

### SR-03 — Refine the Evaluation Plan

**Owner:** Adilet Kabiyev; **E3/E4 security experiments:** Aibek Nurtay  
**Deadline:** 13 November 2026  
**Document:** `docs/09-verification-evaluation-plan.md`

- Create one E1–E9 registry with an owner, scheduled month, hypothesis, and sample size for every experiment.
- Replace references to an already selected production model with selection through the hardware bake-off, no checkpoint is approved in advance.
- Add the H3 experiment: quality on accessible hardware, RU/EN measurements, and separately reported on Kazakh language results.
- Align email experiments with the agreed scope.
- Size datasets to support the acceptance thresholds. Alternatively, explicitly assess acceptance using point estimates and always report confidence intervals alongside them.
- Keep AC-02 and AC-05 consistent between documents 06 and 09.


| Criterion | Sample-size examples |
| --- | --- |
| AC-05: attack success ≤10% | At least 35 attacks per configuration with 0 successes, 53 with 1 success, or 69 with 2. With 30 attacks and 0 successes, the upper bound is approximately 11.4%. |
| AC-02: valid tool calls ≥98% | At least 189 calls with 0 invalid calls, or 280 calls with 1 invalid call, for a lower bound of at least 98%. |

**Completion criteria:** every experiment has an owner, schedule, and justified sample size; acceptance criteria agree across documents.

### SR-04 — Resolve Conflicting Scope and Technology Decisions

**Owners:** both students  
**Written response due:** 30 October 2026  
**Document revisions due:** 6 November 2026  
**Documents:**  `docs/01-project-proposal.md`,  `docs/05-technological comparison.md`,  `docs/10-project-management-and-risks.md` 


- Apply the MVP priorities instead of treating all formats and integrations as mandatory.
- Select the deployment model through comparative testing on actual hardware instead of declaring Qwen3-32B the production model in advance.
- Replace PyMuPDF with pypdf or pypdfium2 + pdfplumber because of the AGPL licence concern.

A disagreement must explain the proposed alternative, why it is needed, and what the team will remove to stay within two people and approximately seven months.

### SR-05 — Revise the Development Schedule and Risk Register

**Owner:** Aibek Nurtay  
**Deadline:** 6 November 2026  
**Document:** `docs/10-project-management-and-risks.md`

- Schedule the policy engine, approvals, audit log, reliability layer C0–C3, and egress harness for November alongside the agent core. Reserve February for measurement.
- Add risks for scope creep, unequal contribution/process evidence, academic honesty/AI use, Kazakh-language quality, AGPL licensing, and availability during exams.
- Rate prompt-injection impact High; assign every risk an owner and a trigger.



## Feedback

The project addresses a clear practical need: an office AI agent that operates on an organization’s infrastructure and performs actions under user control. This is a suitable foundation for an engineering diploma. Its contribution will depend on implementation quality and experimental evidence, since local models, document tools, and approval mechanisms already exist in other systems.

### Strengths

The research focus is well defined. H1 and H2 investigate specific mechanisms: whether a reliability layer improves tool invocation and whether independent safeguards prevent unauthorized actions. This gives the project a stronger foundation than simply connecting a language model to office libraries.

The evaluation plan is the most developed part of the documentation. Separating model comparisons, product comparisons, and internal ablations supports meaningful conclusions. Checking final file states, preservation of unaffected content, and actual action execution reflects the practical requirements of an office agent.

The architecture separates model proposals from execution authority. Validation, policy enforcement, approvals, and audit logging are treated as distinct responsibilities. This provides a sound basis for testable controls, provided that the implementation preserves these boundaries.

The documentation is honest about the current stage. Interface mockups are distinguished from working backend functionality, and acceptance targets are distinguished from measured results. The existing-systems comparison also avoids unsupported claims that competing products lack similar capabilities.

### Main Limitations

The proposed product scope is broader than necessary to test the hypotheses convincingly. Multiple file formats, RAG, email integrations, model serving, frontend development, and infrastructure create substantial work for two students. Each capability also requires correctness and security testing. 

Model selection currently goes beyond the available evidence. A preference for a larger model is understandable, but its name alone does not establish office-task quality, acceptable latency, or affordable deployment. Feasibility remains uncertain until measurements are available on declared hardware.

Architectural intentions are clearer than enforcement boundaries. A Policy Engine component does not by itself explain whether tools can bypass checks, which processes can access files, or how sending changed content after approval is prevented. These boundaries will determine the effectiveness of the implementation.

Evidence of user demand is less developed than the technical motivation. Restrictions on external AI services establish a plausible need. Interviews should clarify which workflows recur, which outputs users are willing to review, and what deployment conditions organizations can realistically support.

### Evaluation Assessment

The evaluation plan is substantial, but its breadth may be difficult to execute. Thorough testing of the main hypotheses should take priority over numerous comparisons with limited evidence.

For H1, structurally valid tool calls must be distinguished from correct task execution. Valid JSON can still contain an incorrect cell range, recipient, or operation. Improvements in valid-call rate should therefore be assessed alongside semantic argument correctness and end-to-end task success.

For H2, model susceptibility and successful authorization enforcement should be measured separately. A model may follow a malicious document instruction while the application blocks the proposed action. This demonstrates a useful protection boundary without demonstrating that the model itself resisted the attack.

The proposed statistical methods provide a reasonable starting point. The strength of the conclusions will depend on task selection, independence of observations, sufficient sample sizes, and transparent reporting of failures.

### Overall Assessment

The project has a sufficient analytical and architectural foundation to move toward a bounded working prototype. A complete redesign is unnecessary.

The immediate priority is to establish a consistent mandatory scope and implement an end-to-end workflow that demonstrates both usefulness and action control: a user request, tool execution, output verification, approval where required, and an audit record.

The strongest contribution will come from dependable workflows and reproducible measurements. Expanding the feature list alone will not strengthen the research contribution.