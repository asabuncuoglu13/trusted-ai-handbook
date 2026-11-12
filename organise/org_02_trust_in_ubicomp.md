---
title: "Trust in ubicomp"
---

> Issue link: https://github.com/asabuncuoglu13/trusted-ai-handbook/issues/3

Ubiquitous computing (ubicomp) integrates computation into the physical environment, making information processing accessible everywhere while remaining largely invisible to the user. AI has long been regarded as the key enabler of this vision, and here we are, the necessary foundations are largely in place!

Mark Weiser coined the term at Xerox PARC almost three decades ago [1](#weiser-91), framing it as the third wave of computing: first came mainframes (many people sharing one computer), then personal computers (one computer per person), and finally ubicomp (many computers per person, embedded seamlessly into everyday objects).

In ubicomp, information moves fluidly between central systems and peripheral devices, allowing tasks to recede from constant, conscious attention (known as calm technology). Systems sense their surroundings—such as location, time, lighting, and user proximity—and adapt automatically, without explicit commands. Hardware becomes smaller and embeds in walls, clothing, appliances, and infrastructure, fading from direct notice.

Although calm technology aims to improve everyday activities involving computers, its defining qualities also bring challenges. When technology becomes ubiquitous and recedes behind a natural interface, two core problems emerge [2](#nelson-93):
* _Disembodiment_ (losing natural cues about what you're broadcasting) and
* _Dissociation_ (losing the connection between actions and their consequences in mediated environments).

These challenges have started emerging in the early 90s, and have grown with the spread of mobile devices, internet connectivity, and, more recently, AI. AI agents can further reduce the cognitive effort required to use technology, but this convenience may introduce new challenges that erode trust. This page focuses on two foundational principles for building trust: feedback and control.

* _Feedback_ means informing people when and what information about them is being captured, and who is receiving it.
* _Control_ means empowering people to stipulate what information they project and who can access it. 
 
**TODO:**

- [ ] Deepen HCI foundation
- [ ] Operationalize Bellotti & Sellen's framework: capture, construction, accessibility, and purpose.
- [ ] Contrast seamlessness vs. seamful design: why hiding seams damages mental models and how appropriate friction preserves trust.
- [ ] Connect human accountability and context-awareness to human oversight ([ops_14_human_oversight.md](../operationalise/ops_14_human_oversight.md)).
- [ ] Specify actionable control mechanisms: ambient indicators, override capabilities, reversibility, boundaries, etc. And link it to: ([ops_16_runtime_controls.md](../operationalise/ops_16_runtime_controls.md)).

# References

(weiser-91)=
1. Weiser M. The computer for the 21st century. SIGMOBILE Mob Comput Commun Rev. 1999;3(3):3-11. doi:10.1145/329124.329126

(nelson-93)=
2. Bellotti V, Sellen A. Design for Privacy in Ubiquitous Computing Environments. In: De Michelis G, Simone C, Schmidt K, eds. Proceedings of the Third European Conference on Computer-Supported Cooperative Work 13–17 September 1993, Milan, Italy ECSCW ’93. Springer Netherlands; 1993:77-92. doi:10.1007/978-94-011-2094-4_6


