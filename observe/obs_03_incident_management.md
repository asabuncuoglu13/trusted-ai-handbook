# Operational Incident Management for Enterprise AI

## Abstract

As AI models become embedded in core enterprise workflows, traditional IT incident management frameworks are facing structural failures. Current literature and policy frameworks predominantly define “AI incidents” through macro-level, societal harms (e.g., human rights breaches, mass disinformation). While essential for public policy, this definition offers little operational guidance for enterprise security and engineering teams tasked with maintaining operational continuity, compliance, and throughput.

In this article, we study the class of events that falls between enterprise IT incidents and AI incidents. These are **AI-tagged incidents**, an additive label for disruptions in which infrastructure is healthy but model quality, fairness, or human review capacity silently degrades.

This article introduces (i) a four-component system model to specify the incident boundary for AI-tags, (ii) a triage and containment framework with severity levels, (iii) explicit criteria for when an operational incident must be escalated to societal-harm governance, and (iv) a mapping of CSIRT services onto an ML-specific incident lifecycle.

## Introduction

Enterprises increasingly place machine-learning models inside core processes, from transforming existing business operations (e.g., drafting customer letters, scoring credit applications) to novel automated workflows such as executing actions through tool-using agents. When such a service degrades, it often does so silently. All operational resiliency metrics, such as endpoint status and latency, are within threshold, yet decisions are worse, biased, or wrong.

Established incident management practice and site reliability engineering (e.g., [1](#ref-1), [2](#ref-2), [3](#ref-3), [4](#ref-4)) are organised around availability, confidentiality, and integrity of enterprise IT and critical infrastructure. Meanwhile, much of the AI-incident literature focuses on macro-level events where the deployment of AI leads directly to severe societal harms ([5](#ref-5), [6](#ref-6), [7](#ref-7), [8](#ref-8)). Existing literature offers little guidance for enterprise incident management models when underlying infrastructure remains healthy and models function normally, yet output quality quietly degrades.

Traditional enterprise IT systems also fail silently (e.g., gray failures [9](#ref-9)), so the distinction is one of degree. For ML services, correctness is statistical and ground truth is often delayed [10](#ref-10), so silent degradation is the *default* failure mode. Detection therefore depends on quality, fairness, and human-review signals that conventional operational resilience practices do not monitor. We introduce the concept of **AI-tagged incidents**—events recorded within an enterprise incident management database that maintain healthy compute infrastructure but exhibit probabilistic failure requiring specialised ML diagnostic, containment, and recovery workflows.

* Section [Illustrative Enterprise AI Disruption Scenarios and Friction Points](#illustrative-enterprise-ai-disruption-scenarios-and-friction-points) presents illustrative enterprise AI disruption scenarios and outlines key operational friction points. 
* Section [System Model and Taxonomy](#system-model-and-taxonomy) formalises the system model, definitions, operational taxonomy, and the triage decision workflow. 
* Section [Adapting CSIRT Services for Enterprise AI](#adapting-csirt-services-for-enterprise-ai) details how CSIRT services are adapted into proactive, reactive, and organisational learning pillars for enterprise AI. We present a conceptual contribution; the model and framework have not yet been validated empirically.

## Background and Related Work

### AI incident documentation and reporting

The AI Incident Database [5](#ref-5) and related work examine how AI incidents are documented ([11](#ref-11), [12](#ref-12)), how responsibility is distributed after harms [13](#ref-13), how reporting regimes could be structured ([6](#ref-6), [29](#ref-29)), how flaws could be disclosed [8](#ref-8), how agent incidents can be analysed [14](#ref-14), and what problems remain open in incident governance [7](#ref-7). This work is essential for policy, but it is aimed mainly at documentation, reporting, and accountability rather than at the hour-by-hour decisions of the teams operating the system.

### Incident management and security response

NIST SP 800-61r3 [1](#ref-1), ISO/IEC 27035 [2](#ref-2), and the CSIRT Handbook [3](#ref-3) provide mature response processes, and SRE practice [4](#ref-4) provides operational vocabulary such as SLOs and postmortems. A separate line of work uses ML to improve incident management for cloud services [23](#ref-23). We address the converse: managing incidents in ML-enabled services.

### AI risk management and ML monitoring

Risk-management and management-system guidance ([15](#ref-15), [16](#ref-16)), AI-specific security knowledge bases and guidance ([17](#ref-17), [18](#ref-18), [19](#ref-19)), and the literature on dataset shift and concept drift ([20](#ref-20), [21](#ref-21), [22](#ref-22)) cover important parts of the problem. To our knowledge, they do not provide an incident-level decision process that combines routing, severity, containment choice, decision rights, and the capacity of the human fallback. That gap is our focus.

## Illustrative Enterprise AI Disruption Scenarios and Friction Points

The following three illustrative scenarios are stylised to show potential disruption scenarios:

**Scenario A: Silent Distributional Drift and Review Bottleneck.** An enterprise complaint-handling application pre-drafts response letters. Historically, human reviewers accept generated outputs directly at a 95% rate. Following an unannounced macroeconomic shift, customer vocabulary evolves. The acceptance rate drops to 83%. While compute infrastructure remains nominal, reviewer workload quadruples, creating a 3-day backlog and breaching customer SLAs.

**Scenario B: Regulatory Compliance and Fairness Metric Shift.** A credit scoring pipeline processes loan applications. A recent adjustment to credit bureau reporting criteria causes the model’s disparate impact ratio across protected demographic categories to drop below regulatory mandates. Approvals continue uninterrupted, but the enterprise incurs immediate legal non-compliance exposure.

**Scenario C: Agentic Tool Misdirection (Hallucination Cascade).** An internal LLM agent parses support tickets to execute automated refund requests via an accounting API [14](#ref-14). A subtle shift in ticket layout triggers structural hallucinations in JSON tool arguments, producing valid payloads that disburse incorrect financial amounts while reporting HTTP 200 execution success.

Mapping traditional operational paradigms to AI-driven enterprise applications introduces profound friction. In systems like Scenario A or B, the service remains 100% online with standard operational monitoring displaying a nominal status. Yet, the underlying probabilistic model generates low-quality, biased, or hallucinated decisions that silently degrade enterprise operations.

### Operational Friction Points

Four operational friction points appear when we try to respond to these disruptions via existing enterprise incident mechanisms:

**MTTR vs. Retraining Lifecycles.** Standard operational resilience targets measure Mean Time to Resolution (MTTR) in hours [4](#ref-4). For probabilistic AI systems, root-cause remediation requires dataset re-curation, feature engineering, re-training, alignment red-teaming, and fairness verification—a process taking days or weeks.

**The Fallback and Kill-Switch Challenges.** Disabling an AI service forces immediate fallback to manual human processing or crude rule-based heuristics. Because human staffing is calibrated for automated assistance, activating an AI “kill switch” instantly overloads human reviewers, transforming a software quality issue into an operational bottleneck.

**Containment Under Infinite Scenario Spaces.** Deterministic software exhibits finite failure paths. Non-deterministic AI failure modes exhibit near-infinite scenario spaces. Offline evaluation suites test known static vectors, whereas real-world enterprise incidents introduce emergent behaviours during live execution.

**Human-in-the-Loop Reviewer Fatigue.** Enterprise workflows rely on human oversight as a safety layer. However, gradual degradation in model accuracy induces automation bias and cognitive fatigue. Reviewers either approve degraded outputs automatically or become overwhelmed by queue spikes, rendering human oversight ineffective precisely when model performance degrades.

These friction points demonstrate that conventional operational resilience models are built for deterministic infrastructure and fixed SLAs, and that it is hard to absorb the stochastic, long-tail failure modes of operational AI. Resolving these bottlenecks requires an operational model designed specifically for continuous monitoring, dynamic containment, and non-linear remediation cycles.

To address these challenges, this article explicitly decouples societal-level AI harms from operational enterprise AI disruptions. **AI-tagged incidents** become a useful entity for explicitly exhibiting failures in the AI model and mobilising specialised ML diagnostic, containment, and recovery workflows.

## System Model and Taxonomy

To construct an actionable enterprise framework, we define core entities, service boundaries, and operational incident categories.

### Definition 1: AI System as an Enterprise IT Service

Aligning with both regulatory perspectives (such as the EU AI Act) and operational service paradigms, this article views an Enterprise AI System as a composite service comprising four interdependent operational components:

1. **Compute Infrastructure:** Hardware, cloud hosting, database storage, and deterministic execution runtimes.
2. **Inference Component (Model):** Probabilistic model architecture, parameter weights, system prompts, guardrails, and agentic execution logic.
3. **Data Distribution:** Continuous real-time operational inputs and runtime feature vectors processed by the service.
4. **Human Operational Workspace:** Human-in-the-loop reviewers, auditors, and business unit personnel responsible for validating outputs and handling queue exceptions.

Here, both the model and system definitions align closely with the FSSCC Oversight Group’s *AI Lexicon* [24](#ref-24):

> **AI Model:** *A component of an information system that implements AI technology and uses computational, statistical, or machine-learning techniques to produce outputs from a given set of inputs.*
>
> **AI System:** *Any data system, software, application, tool, or utility that operates in whole or in part using dynamic or static machine learning algorithms or other forms of artificial intelligence.*

### Definition 2: Standard IT Incident

An unplanned event where service disruption, performance degradation, or availability loss is caused strictly by resource exhaustion or deterministic failure within the *Compute Infrastructure*. Managed via standard Security/DevOps procedures (e.g., container restarts, hardware scaling, database re-indexing).

### Definition 3: AI-Tagged Incident

An unplanned disruption, SLA breach, operational queue bottleneck, or regulatory non-compliance event where the *Compute Infrastructure* remains healthy, but system performance degrades due to probabilistic model failure, distributional drift, or human workspace fatigue. Resolution requires specialised MLOps diagnostics, prompt adjustments, data re-curation, model fine-tuning, or temporary fallback deployment.

To illustrate how AI-tagged incidents would look in practical application, Table 1 presents representative example scenarios across different operational contexts.

**Table 1. Illustrative operational incidents in enterprise AI services.** Currently, AI-tagged incidents generally stay within MLOps teams, causing limited risk visibility across the enterprise.

| Illustrative Event Category | Primary Root Cause | Operational Classification |
|---|---|---|
| API Endpoint 503 / Timeout | Compute capacity exhaustion | Standard IT / Availability Incident |
| Direct Prompt Injection Attack | Adversarial text input | Cybersecurity / SecOps Incident |
| Deprecation of Upstream LLM API | Vendor breaking update | Vendor / Supply Chain Incident |
| Silent Distribution Drift | Shift in customer behaviour | **AI-Tagged Incident** |
| Fairness Metric Breach | Disparate impact threshold failure | **AI-Tagged Incident** |
| Cascading Hallucination | Agentic loop payload corruption | **AI-Tagged Incident** |

### Operational Triage Decision Workflow

Having established clear baseline definitions for AI models and AI systems, we now turn to their practical application in operational governance. Translating these theoretical concepts into actionable incident response requires a structured triage workflow. In this workflow, representatives from enterprise SOC and MLOps teams must follow a structured, multi-tier process when evaluating incoming system anomalies:

1. **Multi-Stream Telemetry Ingestion:** Simultaneously pull infrastructure telemetry (e.g., HTTP status codes, server latency, memory utilisation), probabilistic model telemetry (e.g., drift distance, guardrail flags, payload error rates), and human workspace telemetry (e.g., reviewer rejection rate, queue backlog).

2. **Infrastructure and Security Gate Check:**
   - If infrastructure telemetry indicates service downtime or deterministic execution errors (e.g., HTTP 503), route immediately to **SRE / Infrastructure** as a *Standard IT Incident*.
   - If the event exhibits adversarial input manipulation (e.g., jailbreaks, prompt injections), route immediately to **SOC / Application Security** as a *Cybersecurity Incident*.

3. **AI-Tagged Incident Classification:** If infrastructure telemetry is nominal but model drift or other inference-compute telemetry exceeds defined risk thresholds, or human rejection rates spike, formally classify the event as an **AI-Tagged Incident** and assign ownership to **MLOps / ML Engineering**.

4. **Immediate Blast-Radius Containment:** When an operational incident or anomaly is detected, the immediate priority is to minimise blast radius and disruption before detailed root-cause analysis begins. Containment relies on two complementary mechanisms: Kill Switches and Recovery Modes.
   - *Kill Switches* act as deterministic circuit breakers designed to immediately halt compromised model execution, isolate failing data pipelines, or terminate system access when safety thresholds are breached. Creating kill switches is not easy, and heavily depends on how business processes are structured around the AI system.
   - *Recovery Modes* define pre-configured fallback operational states—such as dropping to rules-based heuristics, rolling back to previous model versions, routing to a cached static output, or gracefully degrading to human-in-the-loop validation—ensuring business continuity while full system isolation or remediation takes place.

   Depending on the nature of the detected anomaly, these mechanisms trigger specific operational containment actions across different system layers. Some illustrative examples are:
   - *Agentic/Tool Anomalies:* Temporarily mask or restrict active tool execution privileges (a selective kill switch) to prevent corrupted payload disbursements [25](#ref-25).
   - *Model Drift / Bias Shifts:* Inject temporary input/output regex or classification guardrails, or route high-risk inference traffic to deterministic rule-based engines (a recovery fallback) ([20](#ref-20), [21](#ref-21), [22](#ref-22)).
   - *Adversarial / Jailbreak Attacks:* Engage input-filtering kill switches to block malicious session IDs and revert user interaction interfaces to static fallback responses [17](#ref-17).
   - *Data Stream Poisoning / Corruption:* Sever real-time retrieval-augmented generation (RAG) connectors or ingestion pipelines and fall back to verified, immutable cached vector snapshots [26](#ref-26).
   - *Latency / Infrastructure Saturation:* Execute automatic load-shedding kill switches on non-critical background jobs and route primary traffic to lightweight, lower-parameter surrogate models [27](#ref-27).
   - *Unauthorized Data Leakage:* Immediately terminate model egress channels and redirect affected queries to mandatory human-in-the-loop approval queues [28](#ref-28).

5. **Human Queue Rebalancing & Escalation:** If human-in-the-loop reviewers are overwhelmed by model accuracy degradation, dynamically scale reviewer allocation or activate business-level fallback policies to prevent SLA breaches.

**Escalation to societal-harm governance.** Operational and societal harms are not always easily separable. For example, it is easy to classify Scenario B as a societal harm (systematic bias) as well as an enterprise incident (regulatory breach). Therefore, explicit triggers should be established to hand these incidents to AI-governance and reporting channels (informed by [6](#ref-6), [29](#ref-29), [8](#ref-8), [13](#ref-13)):

- **E1:** Individuals are affected in ways that are hard to reverse or that concern rights or safety.
- **E2:** Protected groups are disproportionately affected.
- **E3:** A regulatory threshold or reporting duty may be triggered.
- **E4:** Harm extends to third parties or the public.

**Severity.** The incident severity can be defined in four levels:

- **Critical S1 incidents** are triggered by confirmed regulatory breaches, irreversible harm to individuals, or losses exceeding authorised limits.
- **High S2 events** cover large-scale material degradation or projected SLA breaches.
- **Medium S3 / Minor S4 issues** involve limited, reversible degradation down to unconfirmed threshold warnings, which are best left for evaluation during scheduled reviews. Based on enterprise risk profile and use-case materiality, minor issues can be escalated as medium.

**Metrics beyond MTTR.** A single MTTR conflates actions with very different time scales. With new triage steps, we should introduce new reporting durations. Some suggestions are: *time-to-detect* (TTD), *time-to-contain* (TTC), *time-to-mitigate* to acceptable quality (TTM), and *time-to-restore* baseline quality (TTR). TTD, TTC, and often TTM are hours; TTR may be days or weeks and should not be the measure of operational responsiveness. These metrics should be calibrated based on the capacity and risk profile of the enterprise.

While the triage workflow provides a structured decision path for isolating probabilistic anomalies, executing this workflow within conventional enterprise architectures introduces operational friction points covered in [Operational Friction Points](#operational-friction-points). Because cybersecurity incident response frameworks (such as the *Handbook for Computer Security Incident Response Teams (CSIRTs)* [3](#ref-3)) are specifically structured to handle dynamic, probabilistic threats under high uncertainty, they offer a natural blueprint for enterprise AI governance. In this section, we adapt established CSIRT services into an ML-centric operating model structured across three core operational pillars: proactive preparedness, reactive containment, and continuous organisational learning.

## Adapting CSIRT Services for Enterprise AI

A Computer Security Incident Response Team (CSIRT) serves as a single point of contact and centralised capability for preventing, processing, analysing, and responding to computer security incidents within a specified domain [3](#ref-3). Analogous to a traditional fire department, a CSIRT balances reactive operations (answering emergency calls to contain active threats and restore operations) with proactive measures (offering security awareness, vulnerability advisories, and system hardening guidance) to mitigate future risk. The ML-centric operating model proposed below closely follows the structure established in the *Handbook for CSIRTs* [3](#ref-3), spanning three core pillars: Proactive, Reactive, and Maintenance services.

### Proactive Services

Infrastructure-hardening measures and continuous monitoring designed to detect vulnerabilities and anomalous behaviour before safety breaches or exploitation occur.

- **Continuous Evaluation Pipelines:** Running non-static evaluation benchmarks against production inference logs to detect performance decay prior to user reporting.
- **Distributional Drift Monitoring:** Tracking feature-level, prediction-level, and concept drift using statistical metrics such as Wasserstein Distance and Kullback-Leibler ($KL$) Divergence.
- **Shadow Testing & Red-Teaming:** Continuously evaluating candidate fallback models against mirrored production traffic.

### Reactive Services

Event-triggered actions aimed at handling live incidents, containing active model exploits, executing dynamic mitigation, and restoring normal operational parameters.

- **Dynamic Triage and Blast Radius Containment:** Rapidly determining whether an anomaly requires complete service suspension, routing to rule-based fallbacks, or feature masking (e.g., restricting specific tool calls in an LLM agent).
- **Targeted Guardrail Injection:** Deploying temporary input/output guardrail filters (e.g., regex, secondary safety classifiers) as patches while retraining occurs.
- **Cross-Functional Response Coordination:** Synchronising actions across ML Platform Teams, Data Engineering, Legal/Risk, and Business Units.

### Maintenance and Organisational Learning

Advisory functions that leverage incident insights to improve overall enterprise defence. Examples include security consulting, risk analysis, business continuity planning, and staff awareness or training. These services are structured across three dimensions ([30](#ref-30), [11](#ref-11), [12](#ref-12)):

1. **Reflective Learning:** Analysing how system design, dataset curation, and model alignment assumptions contributed to the incident.
2. **Embedded Learning:** Automated integration of incident root-cause inputs back into CI/CD evaluation benchmark suites.
3. **Prospective Learning:** Updating the enterprise AI risk register, safety policies, and model lineage documentation for future development cycles.

## Discussion and Limitations

Integrating non-deterministic machine learning models into traditional architectures introduces a fundamental governance challenge for enterprises. The conceptualisation of the *AI-tagged incident* bridges the gap between high-level AI ethics/governance frameworks and practical enterprise IT operations. By decoupling operational, micro-level system disruptions from macro-societal harms, enterprise security and engineering teams gain an actionable locus of control. However, operationalising this framework introduces specific architectural trade-offs, organisational dynamics, and failure modes that warrant deeper examination.

A critical insight of this work is that AI-tagged incidents cannot be resolved within traditional, siloed incident response models. This change requirement creates immediate structural friction across enterprise stakeholders:

- **ML Engineering and Platform Teams** require extended isolation windows to perform root-cause diagnostics and retraining.
- **Business Unit Operations** actively resist “kill switches” or system suspensions because instant fallbacks to under-staffed manual queues create catastrophic operational backlogs and SLA breaches.
- **Legal, Risk, and Compliance Units** demand immediate service halts upon detecting metric shifts (such as disparate impact breaches) to prevent continuous regulatory non-compliance exposure.

Without a pre-negotiated, cross-functional governance framework, organisations default to ad-hoc decision-making during live disruptions. Implementing a structured framework for *AI tags* in incident management resolves this, aligning competing operational, technical, and legal incentives before a crisis hits.

Although this article’s framework can be utilised for a broader definition of *AI incidents*, the scope is intentionally defined with narrow boundary conditions, which introduces specific scope limitations:

1. **No empirical validation yet.** The framework is conceptual. Severity levels, time targets, and the example rules are illustrative defaults.
2. **Downstream Integrators vs. Frontier Developers:** Our taxonomy and triage workflows are specifically tailored for enterprise downstream adopters integrating third-party or fine-tuned models into operational workflows. We do not address the foundational alignment, systemic safety, or training-phase governance required by frontier model developers.
3. **Operational Continuity vs. Statutory Compliance:** This model optimises internal operational resilience, queue rebalancing, and technical containment. While it generates audit trails relevant to regulatory accountability, it does not replace or execute mandatory public reporting regimes required by emerging legal frameworks (e.g., the EU AI Act or sector-specific financial mandates).

## Conclusion

Enterprise AI services can fail while every infrastructure dashboard is green. We proposed the *AI-tagged incident* as an additive label and a framework for handling such events. By defining and implementing the **AI-Tagged Incident** classification, enterprise teams can correctly triage non-deterministic failures and deploy specialised MLOps workflows without misallocating traditional IT or cybersecurity resources. As probabilistic models become central to enterprise architecture, operational resilience will depend on aligning incident response models directly with the realities of machine learning lifecycles.

## Use of AI Statement

AI-assisted technologies (Gemini and Claude) were used during the preparation of this work solely as supportive writing and editing tools. The AI was employed to refine prose, improve readability and grammatical precision, enhance structural flow, and suggest phrasing alternatives. All conceptual frameworks, core research, underlying arguments, and final editorial decisions were developed independently by the author(s).

## References

<a id="ref-1"></a>1. Nelson, A., Rekhi, S., Souppaya, M., & Scarfone, K. (2025). *Incident response recommendations and considerations for cybersecurity risk management: A CSF 2.0 community profile* (NIST SP 800-61r3). National Institute of Standards and Technology. https://doi.org/10.6028/NIST.SP.800-61r3

<a id="ref-2"></a>2. ISO/IEC. (2025). *ISO/IEC 27035-1:2023: Information technology—Information security incident management—Part 1: Principles and process*. ISO. https://www.iso.org/standard/78973.html

<a id="ref-3"></a>3. West-Brown, M. J., Stikvoort, D., Kossakowski, K.-P., Killcrece, G., Ruefle, R., & Zajicek, M. (2004). *Handbook for Computer Security Incident Response Teams (CSIRTs)*. CMU/SEI.

<a id="ref-4"></a>4. Google. (2026). *Google SRE—Site reliability engineering book*. https://sre.google/sre-book/table-of-contents/

<a id="ref-5"></a>5. McGregor, S. (2020). Preventing repeated real-world AI failures by cataloging incidents: The AI Incident Database. *arXiv:2011.08512*. https://doi.org/10.48550/arXiv.2011.08512

<a id="ref-6"></a>6. Lee Dixon, R. B., & Frase, H. (2025). *AI incidents: Key components for a mandatory reporting regime*. Center for Security and Emerging Technology (CSET).

<a id="ref-7"></a>7. Sidhu, H. K., Scholefield, R., Annan, N., Hernandez, K., Hou, I. N., Alshaikhi, A., Chin, Z. S., & Gipiškis, R. (2026). Open problems in AI incident governance. *arXiv:2607.05163*. https://doi.org/10.48550/arXiv.2607.05163

<a id="ref-8"></a>8. Longpre, S., Zhu, E., Ezell, C., Ghosh, A., McGregor, S., Paeth, K., Klyman, K., Kapoor, S., Bommasani, R., Appel, R., Strom, G., McIlvenny, L., Jaycox, M. M., Slattery, P., Butters, N., Narayanan, A., Liang, P., & Pentland, A. (2026). *FLARE-AI: Flaw reporting for AI*. arXiv:2606.31567. https://doi.org/10.48550/arXiv.2606.31567

<a id="ref-9"></a>9. Huang, P., Guo, C., Zhou, L., Lorch, J. R., Dang, Y., Chintalapati, M., & Yao, R. (2017). Gray failure: The Achilles’ heel of cloud-scale systems. In *Proceedings of the 16th Workshop on Hot Topics in Operating Systems* (pp. 150–155). ACM. https://doi.org/10.1145/3102980.3103005

<a id="ref-10"></a>10. Lee, H. (2026). *Hidden technical debt of AI systems: Agent evaluation infrastructure*. Han, Not Solo. https://leehanchung.github.io/blogs/2026/06/13/hidden-technical-debt-agent-evaluation-infra/

<a id="ref-11"></a>11. Turri, V., & Dzombak, R. (2023). Why we need to know more: Exploring the state of AI incident documentation practices. In *Proceedings of the 2023 AAAI/ACM Conference on AI, Ethics, and Society* (pp. 576–583). ACM. https://doi.org/10.1145/3600211.3604700

<a id="ref-12"></a>12. Paeth, K., Atherton, D., Pittaras, N., Frase, H., & McGregor, S. (2024). *Lessons for editors of AI incidents from the AI Incident Database*. arXiv:2409.16425. https://doi.org/10.48550/arXiv.2409.16425

<a id="ref-13"></a>13. Richards, I., Benn, C., & Zilka, M. (2025). *From incidents to insights: Patterns of responsibility following AI harms*. arXiv:2505.04291. https://doi.org/10.48550/arXiv.2505.04291

<a id="ref-14"></a>14. Ezell, C., Roberts-Gaal, X., & Chan, A. (2025). *Incident analysis for AI agents*. arXiv:2508.14231. https://doi.org/10.48550/arXiv.2508.14231

<a id="ref-15"></a>15. National Institute of Standards and Technology. (2023). *Artificial Intelligence Risk Management Framework (AI RMF 1.0)* (NIST AI 100-1). https://doi.org/10.6028/NIST.AI.100-1

<a id="ref-16"></a>16. ISO/IEC. (2023). *ISO/IEC 42001:2023: Information technology—Artificial intelligence—Management system*. ISO. https://www.iso.org/standard/42001

<a id="ref-17"></a>17. MITRE. (2026). *MITRE ATLAS: Adversarial threat landscape for artificial-intelligence systems*. https://atlas.mitre.org

<a id="ref-18"></a>18. OWASP Foundation. (2025). *OWASP Top 10 for Large Language Model Applications*. https://genai.owasp.org/llm-top-10/

<a id="ref-19"></a>19. Cybersecurity and Infrastructure Security Agency. (2025). *AI Cybersecurity Collaboration Playbook*. CISA / Joint Cyber Defense Collaborative.

<a id="ref-20"></a>20. Gama, J., Žliobaitė, I., Bifet, A., Pechenizkiy, M., & Bouchachia, A. (2014). A survey on concept drift adaptation. *ACM Computing Surveys, 46*, 1–37. https://api.semanticscholar.org/CorpusID:201087802

<a id="ref-21"></a>21. Webb, G. I., Hyde, R., Cao, H., Nguyen, H. L., & Petitjean, F. (2016). Characterizing concept drift. *Data Mining and Knowledge Discovery, 30*(4), 964–994. https://doi.org/10.1007/s10618-015-0448-4

<a id="ref-22"></a>22. Rabanser, S., Günnemann, S., & Lipton, Z. C. (2019). *Failing loudly: An empirical study of methods for detecting dataset shift*. arXiv:1810.11953. https://arxiv.org/abs/1810.11953

<a id="ref-23"></a>23. Chen, Z., Kang, Y., Li, L., Zhang, X., Zhang, H., Xu, H., Zhou, Y., Yang, L., Sun, J., Xu, Z., Dang, Y., Gao, F., Zhao, P., Qiao, B., Lin, Q., Zhang, D., & Lyu, M. R. (2020). Towards intelligent incident management: Why we need it and how we make it. In *Proceedings of the 28th ACM Joint Meeting on European Software Engineering Conference and Symposium on the Foundations of Software Engineering* (pp. 1487–1497). ACM. https://doi.org/10.1145/3368089.3417055

<a id="ref-24"></a>24. Financial Services Sector Coordinating Council. (2026). *FSSCC AIEOG AI Lexicon*. https://fsscc.org/wp-content/uploads/2026/02/AIEOG-AI-Lexicon-February-2026.pdf

<a id="ref-25"></a>25. Shi, T., He, J., Wang, Z., Li, H., Wu, L., Guo, W., & Song, D. (2026). *Progent: Securing AI agents with privilege control*. arXiv:2504.11703. https://doi.org/10.48550/arXiv.2504.11703

<a id="ref-26"></a>26. Zou, W., Geng, R., Wang, B., & Jia, J. (2024). *PoisonedRAG: Knowledge corruption attacks to retrieval-augmented generation of large language models*. arXiv:2402.07867. https://doi.org/10.48550/arXiv.2402.07867

<a id="ref-27"></a>27. Chen, L., Zaharia, M., & Zou, J. (2023). *FrugalGPT: How to use large language models while reducing cost and improving performance*. arXiv:2305.05176. https://doi.org/10.48550/arXiv.2305.05176

<a id="ref-28"></a>28. Xie, C., Dhurandhar, A., & Li, B. (2026). *PRIGUARDAGENT: Context-aware privacy guardrails for agentic systems*. ICLR 2026. https://research.ibm.com/publications/priguardagent-context-aware-privacy-guardrails-for-agentic-systems

<a id="ref-29"></a>29. Agarwal, A., & Nene, M. J. (2024). Addressing AI risks in critical infrastructure: Formalising the AI incident reporting process. In *2024 IEEE International Conference on Electronics, Computing and Communication Technologies (CONECCT)* (pp. 1–6). https://doi.org/10.1109/CONECCT62155.2024.10677312

<a id="ref-30"></a>30. Rezazade Mehrizi, M. H., Nicolini, D., & Mòdol, J. R. (2022). How do organizations learn from information system incidents? A synthesis of the past, present, and future. *MIS Quarterly, 46*(1), 531–590. https://doi.org/10.25300/MISQ/2022/14305
