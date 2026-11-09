# What do we want from AI?

[![hackmd-github-sync-badge](https://hackmd.io/JKx5FYy1T8S5X9p0AAm6KQ/badge)](https://hackmd.io/c4Y8CpBjQSqZIcRr3qVYzg)

_From "Making Better AI" to "Making AI Better"_

The dominant trajectory of AI has been focused on making better things: advancing computational models, scaling hardware, expanding datasets, and demonstrating proof of concepts (PoCs). From the early days of neural network design through various AI summers and winters, the primary benchmark was capability.

However, in mature engineering disciplines, a PoC is merely a starting line. Once a system is deployed into the wild, unpredicted failure modes inevitably surface. True engineering maturity requires moving from making better things to making things better.

From an engineering standpoint, "making things better" means optimising the entire solution space. Crucially, this optimization cannot be limited to corporate metrics like cost-effectiveness or short-term ROI. A macro-engineering perspective requires optimising for the broader ecosystem—an approach often termed "beyond-human design." Rather than treating safety, reliability, and human alignment as downstream constraints, we must embed these practices directly into the professionalisation of the discipline.

I think the challenge begins when we apply software engineering's distinction between functional and non-functional requirements to AI. In software engineering, teams often make the mistake of developing functional capabilities before addressing requirements such as security and resilience. This sequential approach also fails in AI: there is no static or universal definition of a "perfect" or "best" non-deterministic model, so capability and safety must be developed together.

```{mermaid}
graph LR
    subgraph Loop["Continuous Feedback Loop"]
        A["Making Better Things<br>(Capability & PoC)"] --> B["Making Things Better<br>(Refinement & Robustness)"]
        B --> A
    end
```

## AI as Digital Public Infrastructure

When Berners-Lee published the Contract for the Web [1](#contract-for-the-web) in 2019, it marked a turning point: an explicit recognition that the internet had become digital public infrastructure too vital to govern by default. The web required positive principles—such as universal access, privacy protection, and civic support—rather than mere technical specifications.

AI is undergoing a similar transition. Open-source communities are working to keep pace with developments, while governments worldwide seek to build sovereign AI ecosystems. Although AI is not always viewed as standalone physical infrastructure, it is increasingly embedded in systems we rely on daily, including healthcare, finance, hiring, and education.

AI governance approaches—including the EU AI Act, UNESCO's recommendations, and the UK's pro-innovation approach—address risks alongside other priorities. It is easy for discussions to focus on immediate risks as AI advances rapidly, but we need both near-term solutions and long-term strategic investment. We should not focus exclusively on speed limits; we must also consider where the roads ought to lead.

### Shift Toward Positive Governance & Actionable Frameworks

Moving past pure risk mitigation requires articulating what we *positively want* AI to do. Funding bodies and research initiatives are beginning to reflect this shift. For example, _ARIA's (UK)_ [Scaling Trust](https://aria.org.uk/opportunity-spaces/trust-everything-everywhere/scaling-trust) programme allocated £59 million toward developing quantitative safety guarantees for deployment in high-stakes domains like energy and healthcare. Another UK programme, BridgeAI, provides £32 million to drive adoption across construction, transport, and agriculture around beneficial applications. Since I live the in the UK, I am more familiar with the UK-initiated programmes, however we can find more programmes investing into similar initiatives worldwide.

>_... kill switches cannot be the whole safety strategy. Good design choices lead to solid engineering practice, then practical international standards, then balanced regulation._ Sir John Lazar CBE FREng. <https://asktheengineers.substack.com/p/an-ai-kill-switch-is-no-substitute>

## So, what do we want from AI?

When I searched for this question, I came across Fisher's article [2](#fisher-ai) of the same name! It proposes a framework of 18 principles for socially responsible AI:

| Cluster | Principles |
| --- | --- |
| **AI and Humans** | 1. Human value alignment<br>2. At least as good judgment as humans in critical situations, and the ability to improve when limitations or biases are detected<br>3. Collaboration with people<br>4. Provide cognitive help<br>5. The AI can step back<br>6. Helpful, Honest, Harmless (HHH) |
| **AI Characteristics** | 7. Trustworthy, quantifiable, and improving expertise<br>8. Dependable agents<br>9. Thoroughness and consistency<br>10. Confidence and uncertainty estimates plus explainability<br>11. Improving levels of moral and legal responsibility<br>12. Transparency |
| **AI in Practice** | 13. Ability to do risky and unpleasant tasks<br>14. Focus on widely useful applications<br>15. Focus on public sector benefits<br>16. Local impact of AI actions<br>17. Rigorous engineering methodology<br>18. Training data provenance |

Beyond these principles, we want:
> Digital brains!  _Imagine a digital lab assistant able to read the contents and understand the semantic context in these tens of millions of papers, and with whom I could have a technical conversation. Rather than relying on keyword-based queries, DARPA's Big Mechanism is enabling computers to understand, via machine-reading technology, the scientific landscape in an important area of cancer research._ https://ieeexplore.ieee.org/document/8012285 

Check out their paper: https://doi.org/10.1002%2Faaai.70042, it gives a great perspective on what do we want and not want from AI!

### Engineering the Future: A Call to Action

In the early days of personal computing and the web—from Nelson’s counterculture vision in *Dream Machines* [3](#nelson-computer-lib) to Weiser's ubiquitous computing [4](#weiser-computer), the computing community actively has been working on shaping the underlying concepts and infrastructure of _ambient intelligence_ through open, modular, and human-centric components.

AI requires a similar proactive engineering ethos today:

* Treat AI as infrastructure and its safety as a public good. Design systems with high obligations toward public utility, safety guarantees, and robust failure boundaries.
* Build functional and behavioural capabilities concurrently. Refuse to separate capability development from reliability and alignment.
* Measure what matters. Move beyond commercial metrics to evaluate systemic quality, fairness, and robustness across parent and child systems.

By framing AI not just as a product to be sold, but as public infrastructure to be engineered, we can move beyond relying on a handful of organisations to _make better AI_ and instead work together towards _making things better_.

# Relevant

> ... if we are to realise those benefits _(of AI: scientific discovery, productivity and economic prosperity)_ safely, we must answer one critical question: should society retain the ability to intervene, to establish the boundaries within which these systems operate and to revise those boundaries as the technology evolves? **by Andrew Bailey, BoE**. https://www.bankofengland.co.uk/bank-insights/2026/frontier-ai-and-the-question-of-governance

# References

(contract-for-the-web)=
1. *Contract for the Web*. (2026). Homepage. Retrieved 8 November 2026, from https://contractfortheweb.org/

(fisher-ai)=
2. Fisher, R. B. 2025. “ What do we want from AI?.” AI Magazine 46: e70042. https://doi.org/10.1002/aaai.70042

(nelson-computer-lib)=
3. Nelson, Ted. (1987). *Computer lib; Dream machines* (rev. ed.). Tempus. ISBN 0914845497.

(weiser-computer)=
4. Weiser, Mark. (1999). The computer for the 21st century. *ACM SIGMOBILE Mobile Computing and Communications Review, 3*(3), 3–11. https://doi.org/10.1145/329124.329126