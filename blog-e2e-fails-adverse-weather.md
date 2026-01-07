# Why Pure End-to-End Autonomous Driving Still Breaks (Especially in Adverse Weather)

End-to-end (E2E) autonomous driving has become one of the most exciting directions in robotics and vision: learn directly from raw sensor inputs to planned trajectories in a single differentiable system. But despite rapid progress, “pure” E2E systems still break in ways that matter—particularly under sensor degradation from rain, fog, snow, and low illumination.

This post is not an anti–end-to-end take. Many E2E papers are explicitly motivated by the shortcomings of modular stacks, and they have pushed the field forward significantly. The key question is narrower:

> **Do we have evidence (from the E2E literature itself) that pure E2E remains brittle—and why?**

The answer is yes, and it points toward reliability-aware, structured fusion designs.

---

## 1) E2E papers implicitly admit brittleness by adding “robustness modules”

A useful signal is what authors choose to *add* to make E2E work.

For example, CVPR 2025 work on momentum-aware planning explicitly incorporates a **“Robust Instance Denoising Module”** to increase robustness against environmental noise and perception errors. :contentReference[oaicite:0]{index=0}

That design decision is telling:
- If pure E2E were inherently robust, you would not need a dedicated denoising/robustification layer.
- The paper acknowledges that perception noise and errors can destabilize planning, so it introduces extra structure to counter it.

This pattern appears repeatedly across E2E driving: adding memory mechanisms, denoisers, stabilizers, and auxiliary tasks to reduce failure modes that arise when the world is messy.

---

## 2) Even “unified” E2E frameworks still have documented failure cases

UniAD (CVPR 2023) is a landmark: it proposes a planning-oriented unified framework spanning perception → prediction → planning, and explicitly argues that fragmented systems can suffer from accumulated errors and poor task coordination. :contentReference[oaicite:1]{index=1}

But importantly: UniAD also presents **failure case analyses** (e.g., cases where tracking/detection of certain agents is inaccurate), illustrating that unification alone does not eliminate long-tail breakdowns. :contentReference[oaicite:2]{index=2}

This is exactly the point: E2E can reduce certain modular failure cascades, yet still fails when the underlying sensory evidence is degraded or ambiguous—conditions that adverse weather amplifies.

---

## 3) The field is large because the problem is not “solved by scaling” (survey evidence)

A comprehensive survey in PAMI (“End-to-end Autonomous Driving: Challenges and Frontiers”) defines E2E driving as mapping raw inputs to planning/control and organizes a large body of work (250+ papers) around open challenges and frontiers—implicitly reflecting that reliability, generalization, and safety-critical robustness remain unresolved. :contentReference[oaicite:3]{index=3}

If E2E were already reliable in the wild, the research frontier would not be dominated by mechanisms to improve:
- robustness,
- interpretability,
- long-tail generalization,
- safety evaluation,
- and failure diagnosis.

---

## 4) “End-to-end” often still reintroduces structure because planning needs it

Several “E2E” methods end up preserving or reintroducing intermediate structure (explicit perception/prediction representations, BEV occupancy, memory banks, etc.) because planning benefits from stable scene representations.

For instance, recent E2E work on leveraging historical prediction explicitly critiques existing aggregation paradigms as inherited from detection and argues they can omit critical historical information or misalign with multi-step planning needs. :contentReference[oaicite:4]{index=4}

Likewise, UAD (2024) notes that many E2E driving models still mimic modular stacks with supervised subtasks feeding planning—again suggesting that “pure end-to-end” is often not the most workable design in practice. :contentReference[oaicite:5]{index=5}

---

## 5) Why adverse weather makes the E2E brittleness worse

Adverse weather creates exactly the conditions where E2E pipelines struggle:
- **Cameras:** low contrast, glare, fog scattering, night conditions
- **LiDAR:** backscatter and dropouts in rain/snow
- **Radar:** robust but sparse/noisy geometry and ambiguous object extent

In these regimes, failures aren’t just “a bit worse accuracy”—they change the *type* of error: missed agents, incorrect extents/headings, inconsistent scene interpretation, and unstable planning.

That is why I believe reliability-aware fusion is not optional:
> the system must reason about *which sensor evidence to trust right now*.

---

## 6) What this implies: reliability-aware, structured E2E (not purely end-to-end)

The best takeaway from UniAD and newer E2E driving papers is not “end-to-end is bad.”
It’s that **end-to-end needs structure** to be reliable.

This motivates frameworks like my thesis direction (SenseRegen):
- **Weather-adaptive sensor prioritization** (learn to weight modalities when others degrade)
- **Restoration-guided learning** (recover degraded representations using cross-modal cues)
- **Structured multi-task fusion** (HydraNet-style heads rather than a single opaque policy)

In other words:
> **E2E should be planning-oriented *and* reliability-aware.**

---

## References (selected)
- UniAD / Planning-oriented Autonomous Driving (CVPR 2023). :contentReference[oaicite:6]{index=6}  
- Don’t Shake the Wheel: Momentum-Aware Planning in End-to-End Autonomous Driving (CVPR 2025). :contentReference[oaicite:7]{index=7}  
- End-to-end Autonomous Driving: Challenges and Frontiers (PAMI 2024). :contentReference[oaicite:8]{index=8}  
- UAD: End-to-End Autonomous Driving without Costly Modularization and 3D Manual Annotation (2024). :contentReference[oaicite:9]{index=9}  
- Bridging Past and Future: End-to-End Autonomous Driving with Historical Prediction (CVPR 2025). :contentReference[oaicite:10]{index=10}
