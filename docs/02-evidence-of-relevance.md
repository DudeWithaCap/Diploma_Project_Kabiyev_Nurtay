# 2. Evidence of Relevance

## 2.1 Research Methodology

To investigate how artificial intelligence is used in organizational environments and what restrictions affect its adoption, two semi-structured interviews were conducted with professionals working in Kazakhstan. The respondents represented different sectors:

- Respondent 1: Software developer at a fintech company.
- Respondent 2: Healthcare professional working at a hospital.

Both respondents answered eight open-ended questions concerning current AI use, types of solutions, organizational policies, risks, data restrictions, internally deployed solutions, implementation challenges, and changes in AI adoption.

The research was supplemented by a first-hand observation from an internship at a quasi-governmental company within the Samruk-Kazyna group. The interviews and observation were intended to explore existing practices and constraints, not to measure market demand for a specific product.

## 2.2 Interview Findings

### 2.2.1 Respondent 1: Fintech Sector

The first respondent, a software developer at a fintech company, confirmed that AI tools are actively used within the organization. These include a corporate deployment of a solution used for software development and certain IT operations, including SRE and DevOps-related activities and production log analysis. The name of the solution is to remain undisclosed.

However, the respondent emphasized that AI adoption is governed by established internal security policies and organizational rules. Particular attention is given to protecting confidential information and preventing unauthorized disclosure of customer data. According to the respondent, customer information must not be transferred to external AI services, and ensuring that organizational data is not used to train external AI models is an important security consideration.

The respondent also mentioned that certain teams reportedly deployed locally hosted AI solutions, including internal chatbots. Such solutions are intended for an organization's internal needs and may allow it to process information on infrastructure under its own control rather than transmitting it to an external AI provider. The respondent did not participate in these deployments and could not provide detailed information about their technical architecture or data handling.

The respondent additionally noted that some developers are still adjusting to AI-assisted workflows. Overall, this interview illustrates that active AI adoption can coexist with strict restrictions on sensitive data and interest in internally hosted alternatives.

### 2.2.2 Respondent 2: Healthcare Sector

The second respondent, a healthcare professional, reported that their hospital had recently introduced a trial use of a tool referred to as Defect AI. According to the respondent, the system processes medical histories of previously treated patients to review records and identify potential deficiencies.

The respondent also described the use of DAMUMED as a medical information system for working with medical documentation. DAMUMED was discussed as part of healthcare digitalization. Its use should not, by itself, be treated as evidence of an AI-based system but rather showing digitalization and how software is being adopted within healthcare sector, serving as a basis for driving software development and its adaptation in daily use.

Confidentiality and protection of personal data were identified as key concerns. The respondent indicated that employees acknowledge confidentiality obligations when they are granted access to medical information systems. The respondent did not identify a specific internal rule defining which categories of data may be submitted to external AI services, however this should not be interpreted as confirmation that such transfers are permitted.

The respondent described organizational and technical difficulties during the introduction and development of digital systems, including frequent updates at earlier stages and the later establishment of stricter access rights, permissions and monitoring. They reported no substantial recent change in AI use and were unaware of specific future plans.

This interview shows the importance of controlled access and confidentiality when digital and AI-assisted systems are used in environments that handle sensitive personal information.

## 2.3 Personal Experience During Internship

Additional first-hand evidence comes from an internship at a quasi-governmental company within the Samruk-Kazyna group in Kazakhstan.

During the internship, the student reviewed the organization's internal information security policies. These policies restricted the disclosure of confidential information and the use of internal organizational information outside authorized work purposes and approved tools.

In particular, uploading internal company documents to external AI services was prohibited, including when the intended purpose was document analysis, identifying errors, or other seemingly routine assistance. This created a practical boundary between the potential usefulness of AI for office work and the tools that employees were authorized to use with organizational information.

This observation reflects the policies encountered at one organization during one internship. It does not establish that all quasi-governmental organizations follow identical rules, or that all forms of AI use were prohibited there.

## 2.4 Legislative Evidence

The legal framework of Kazakhstan provides further context for the confidentiality, data protection and oversight concerns identified in the interviews and internship observation. The following selected provisions from three legislative acts are relevant to the proposed Self-Hosted AI Office Assistant. The project responses represent proposed engineering measures rather than technologies explicitly required by legislation.

### 2.4.1 Law on Personal Data and Their Protection [13]

Law of 21 May 2013 No. 94-V.

| Article | Relevant provision | Relevance to the proposed project |
| --- | --- | --- |
| 11 | Confidentiality of personal data | Restrict access to uploaded files, conversations and derived information to prevent unauthorized disclosure. |
| 12(2) | Storage of personal data in a database and/or digital object located in Kazakhstan | Keep applicable personal-data storage, including relevant backups and derived stores, within Kazakhstan. |
| 16 | Conditions governing cross-border transfers of personal data | Evaluate external services and transmission paths when personal data may leave the country. |
| 22 | Duties concerning personal-data protection | Introduce authorization, user-level access isolation and documented security measures. |

These provisions highlight the importance of maintaining control over how organizational data is stored, accessed and transmitted. They are particularly relevant when AI tools process employee or customer information.

### 2.4.2 Law on Artificial Intelligence [14]

Law of 17 November 2025 No. 230-VIII.

| Article | Relevant provision | Relevance to the proposed project |
| --- | --- | --- |
| 8 | Accountability and continuing oversight | Define responsibility for AI-assisted operations and provide user review and intervention controls. |
| 10 | Data protection and confidentiality | Prevent unauthorized access and unlawful processing of data handled by the agent. |
| 16 | Rights and duties of AI system users | Provide clear usage information and enforce relevant access restrictions. |
| 17 | Risk, autonomy and usage regimes | Evaluate the applicable system classification and control autonomous operations. |
| 18 | Lifecycle risk management | Identify system risks, document mitigations and verify their effectiveness during development and operation. |

These articles are relevant to the proposed agent because it processes files and may perform actions on behalf of users. The planned user approval step for actions such as email delivery is a project-level safety measure aligned with the broader objective of human oversight.

### 2.4.3 Digital Code of the Republic of Kazakhstan [15]

Code of 9 January 2026 No. 255-VIII.

| Article | Relevant provision | Relevance to the proposed project |
| --- | --- | --- |
| 16 | Digital data and special rules for protected information | Apply appropriate confidentiality and personal-data protections to files and extracted content. |
| 38 | Obligations of owners and holders to protect rights and prevent unlawful access, copying, alteration or deletion | Apply permissions and controlled file operations, and protect original documents from unauthorized changes. |

Together, these provisions reinforce the practical importance of data confidentiality, controlled storage, access management and oversight. The proposed self-hosted architecture is intended to support these objectives while allowing organizations to use AI tools within their controlled infrastructure.

## 2.5 Key Findings and Implications

The interviews, internship observation and selected legislative provisions suggest several relevant patterns:

| Finding | Evidence | Implication for the proposed project |
| --- | --- | --- |
| AI tools are already used in organizational workflows. | Fintech respondent reported organizational use of vendor solutions, healthcare respondent described a trial of Defect AI. | An office AI assistant should address real workflows rather than assuming that organizations do not use AI at all. |
| Data confidentiality and access control are central concerns. | Fintech respondent identified restrictions on customer data, healthcare respondent emphasized confidentiality, internship policies prohibited uploading internal documents to external AI services. Articles 11 and 22 of the Law on Personal Data and Their Protection are also relevant. | Data handling, authorization, and deployment boundaries should be explicit design considerations. |
| Some organizations explore internally hosted AI solutions. | Fintech respondent reported that other teams had deployed internal chatbots. | Self-hosted deployment is a relevant architectural approach to evaluate, although the reported installations were not independently inspected. |
| Organizations differ in their AI practices and restrictions. | The two respondents described different tools, use cases, and levels of knowledge about specific policies. | The system should not assume that every company has the same needs or governance rules. |
| Kazakhstan's legal framework includes obligations relevant to AI-supported document processing. | Selected provisions of the Law on Personal Data and Their Protection, Law on Artificial Intelligence and Digital Code [13–15]. | The system design should account for applicable data storage, confidentiality, access control, oversight and risk management requirements. |

### Verified Observations and Remaining Assumptions

The interview findings are based on statements made by two respondents and a first-hand account of one internship. They constitute qualitative evidence of reported practices and encountered restrictions, not independent audits of organizational systems or policies. The legislative provisions provide a separate source of evidence concerning the regulatory context.

It remains unverified whether the interviewed organizations would adopt the proposed Local AI Agent, which office tasks they would prioritize, or what deployment and integration requirements they would impose. These questions require further validation through additional interviews, stakeholder feedback, or prototype evaluation.

## 2.6 Limitations

The study provides valuable insights into the practical use of AI and the challenges associated with its adoption in corporate environments. Although the research includes two interviews and one personal observation, the participants represent three distinct sectors in Kazakhstan, offering different perspectives on organizational AI adoption, information security policies, and data confidentiality requirements.

The findings highlight common concerns regarding the protection of sensitive information and demonstrate how organizations approach AI adoption under existing security restrictions. These observations provide practical evidence supporting the relevance of the Local AI Agent project and its focus on secure, internally controlled AI capabilities.

The legislative analysis further strengthens this evidence by identifying relevant provisions of Kazakhstan's legislation concerning personal data protection, confidentiality, AI governance, and digital information security. Together, the interviews, personal experience, and legal framework establish a foundation for the project's requirements and architectural decisions.

Nevertheless, further research is necessary to gain a more comprehensive understanding of AI adoption, internal security policies, and implementation challenges across government institutions, quasi-governmental organizations, fintech, and big tech companies in Kazakhstan. Additional interviews with industry professionals, analysis of organizational practices, and practical evaluation of the proposed solution will help validate and expand the findings of this initial study.
