# AI Geometry Skill (v1.0.0)
## Canonical Navigational Entry Point

> **System Version**: `v1.0.0`  
> **Status**: `FROZEN RESEARCH TOOL`  
> **Semantic Contract Version**: `v0.4`  
> **GSA Protocol Version**: `v0.1.0`  
> **PGS-2D Specification**: `v1.0`  
> **Core Principle**: `REASON OUTSIDE. VERIFY INSIDE.`

---

## 1. Purpose & Identity

The **AI Geometry Skill** is a deterministic, headless geometric verification and research instrument designed for external AI reasoning agents. It provides a formal analytical sandbox where external models can construct Euclidean geometry, observe topological and metric states, test derivation hypotheses, and exchange verified solutions under a zero-trust model.

*«The agent may be wrong. The stand must not be.»*

---

## 2. Architectural Blueprint

The architecture strictly decouples external epistemic reasoning from internal analytical truth:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                            AI REASONING AGENT                               │
│        (Reasoning, Hypothesis Formation, Proof Strategy, DRA Guidance)      │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       │ calls Headless API
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                       HEADLESS GEOMETRY SKILL FACADE                        │
│                         (HeadlessGeometrySession)                           │
│   ├── Actions: create_point, connect, create_circle, construct, intersect   │
│   ├── Kinematics: move, erase, reset, fork, rollback                        │
│   ├── Observations: observe(COMPACT | FULL | OBJECT | PROVENANCE)           │
│   ├── Measurements: measure(SEGMENT_LENGTH)                                 │
│   └── Verification: verify(PERPENDICULAR_TO, PARALLEL_TO, EQUAL_LENGTH...)  │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       │ executes on SSOT
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                     GEOMETRY CORE & KERNEL (Pure SSOT)                      │
│   ├── FullGeometryState (Authoritative State)                               │
│   ├── Analytical Solvers (geometryIntersections.ts: line, circle, segment)  │
│   ├── Macro Reducer (constructionCore.ts: perpendicular, bisectors)         │
│   └── Strict Verification Engine (Fixed receiver tolerance: eps = 1e-4)     │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       │ exportSolutionArtifact()
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                    GSA v0.1 INTER-AGENT EXCHANGE ENVELOPE                   │
│   ├── Metadata & Canonical Resolver Reference (GitHub Stand Repository)     │
│   ├── PGS-2D Geometric Payload (Analytical objects, roles, coordinates)     │
│   └── Certified Claims & Measurements (Subject to Local Zero-Trust Check)   │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Public API & Verification Contract

- **Authoritative State**: `FullGeometryState` is the sole source of geometric truth.
- **Fixed Receiver Tolerance**: All predicate verifications enforce $\varepsilon = 10^{-4}$ owned by the stand; external agents cannot loosen verification thresholds.
- **Deterministic Compute**: Analytical intersection solvers and root ordering $P(t_0) < P(t_1)$ along the directed line vector are 100% deterministic.
- **Multiple Roots Preservation**: Equations returning multiple roots (e.g. secants) return all roots explicitly (`rootIndex: 0, 1`). No silent pruning.
- **State Inviolability**: Input validation errors ($NaN$, $\pm\infty$, missing IDs) reject commands before execution, leaving state 100% unmutated.

*Reference*: See [ENVIRONMENT_CONTRACT.md](./ENVIRONMENT_CONTRACT.md) and [VERIFICATION.md](./VERIFICATION.md).

---

## 4. Agent Guidance & Deterministic Reasoning Anchor (DRA)

The skill explicitly guides agents on *when* and *how* to use the tool without becoming an executable runtime solver:

1. **RECOGNIZE**: Identify formalizable geometric relations in the problem.
2. **DECIDE**: Assess if external deterministic verification materially strengthens reasoning. Conscious refusal (`DRA = NO`) is valid.
3. **VERIFY**: Project the minimal geometric fragment into the Stand.
4. **SEPARATE**: Strictly divide `TOOL-VERIFIED FACT` $\neq$ `AGENT INTERPRETATION` $\neq$ `AGENT HYPOTHESIS`.
5. **UPDATE**: Ground subsequent proof steps upon the verified anchor.

*Reference*: See [AGENT_GUIDANCE_DRA.md](./AGENT_GUIDANCE_DRA.md).

---

## 5. Geometric Solution Artifact (GSA) & Zero Trust Transfer

- **Envelope**: Self-describing JSON artifact combining resolver metadata, PGS-2D payload, and certified claims.
- **Zero Trust Rule**: $\text{Producer Claim} \neq \text{Receiver Truth}$. Receiving agents reconstruct geometry into an isolated blank session and independently re-evaluate all certified claims against their local kernel.
- **Mismatch Rollback**: Any claim failure results in `status: 'MISMATCH'` and complete state rollback.

*Reference*: See [CONFIG_PASSPORT](./CONFIGURATION_PASSPORT.md).

---

## 6. Known Limitations & Capability Gaps

The following boundaries are documented as non-blocking capability gaps for v1.0.0:

1. **Solution Branch Identity**: `rootIndex` is a local parametric order along the line vector and does not constitute a persistent topological branch ID. `BranchTree` is not implemented.
2. **Materialized Intersection Snapshots**: Points created via `intersect()` are static snapshots; they do not automatically recompute upon moving parent lines (unlike macro-derived objects).
3. **Artifact Determinism**: `artifactId` uses `Date.now()` timestamping, causing varying envelope hashes for identical geometric states.
4. **Epistemic VANISHED State**: The internal transition `VERIFIED ➔ VANISHED ➔ VERIFIED` is maintained in the internal knowledge graph, but is `NOT REPRESENTABLE` via the public boolean `verify()` method.
5. **Angular Predicates**: Only orthogonal/parallel predicates are supported; arbitrary angle measurement (`ANGLE_VALUE`) is unsupported.

*Reference*: See [DEVELOPMENT.md](./DEVELOPMENT.md) and [RESEARCH_MODE.md](./RESEARCH_MODE.md).

---

## 7. Canonical Document Map

| Document | Purpose |
|:---|:---|
| [AI_GEOMETRY_SKILL.md](./AI_GEOMETRY_SKILL.md) | **This document**: Unified entry point and release map |
| [AGENT_GUIDANCE_DRA.md](./AGENT_GUIDANCE_DRA.md) | Recommended agent reasoning pattern (DRA) |
| [ENVIRONMENT_CONTRACT.md](./ENVIRONMENT_CONTRACT.md) | Environment contract and invariants |
| [VERIFICATION.md](./VERIFICATION.md) | Verification engine and receiver-owned tolerances |
| [RESEARCH_MODE.md](./RESEARCH_MODE.md) | Experimental methodology and blast radius rules |
| [patterns/](./patterns/) | Geometric Reasoning Pattern Palette (GRPP) |
