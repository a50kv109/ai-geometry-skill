# AI Geometry Skill v1.0.0

> **System Version**: `v1.0.0`  
> **Status**: `FROZEN RESEARCH TOOL`  
> **Semantic Contract**: `v0.4`  
> **GSA Protocol**: `v0.1.0`  
> **PGS-2D Specification**: `v1.0`  
> **Core Principle**: `REASON OUTSIDE. VERIFY INSIDE.`

---

> **Canonical System Definition:**  
> **«AI Geometry Skill v1.0.0 is a deterministic, headless geometry verification and research instrument for external AI agents, with an explicit capability boundary and zero-trust inter-agent exchange.»**

*«The agent may be wrong. The stand must not be.»*

---

## 1. What is AI Geometry Skill?

The **AI Geometry Skill** provides external reasoning models (LLMs, AI coding agents, autonomous theorem provers) with a formal, deterministic, headless 2D Euclidean geometry sandbox. 

Instead of forcing language models to hallucinate floating-point arithmetic or guess spatial relationships, the Skill acts as an external **epistemic anchor**: the AI reasons about theorems, proof strategies, and hypotheses on the outside, while delegating Euclidean constructions, measurements, and predicate evaluations to a pure, deterministic kernel on the inside.

---

## 2. Who is it for?

1. **AI Reasoning Agents**: Autonomous models exploring Euclidean proofs, seeking counterexamples, or discovering geometric invariants.
2. **Multi-Agent Systems**: Collaborating agents exchanging verified geometric theorems via self-describing GSA artifacts under a strict Zero-Trust protocol.
3. **Researchers & Educators**: Humans and automated benches requiring formal verification of planimetric configurations without heavy interactive UI dependencies.

---

## 3. What problem does it solve?

- **LLM Hallucinations in Geometry**: Language models frequently guess angles, miscalculate Pythagorean triples, and hallucinate chord tangencies. The Skill evaluates relations analytically using IEEE 754 precision and fixed receiver-owned tolerances ($\varepsilon = 10^{-4}$).
- **Conflation of Fact and Narrative**: Conventional agent tool-calling often blurs the boundary between what the tool proved and what the model assumed. The Skill enforces a strict three-tier epistemic boundary:
  $$\text{TOOL-VERIFIED FACT} \neq \text{AGENT INTERPRETATION} \neq \text{AGENT HYPOTHESIS}$$
- **Untrusted Inter-Agent Claims**: In multi-agent pipelines, Agent A claiming a theorem does not make it true for Agent B. The Skill implements Zero-Trust exchange: $\text{Producer Claim} \neq \text{Receiver Truth}$.

---

## 4. What it CAN do (Supported Capabilities)

- **Primitives**: 2D analytical points, finite line segments, infinite lines, Euclidean circles.
- **Analytical Constructions**: Perpendicular lines, parallel lines, angle bisectors, perpendicular bisectors.
- **Exact Intersections**: Analytical solutions for Line $\times$ Line, Line $\times$ Circle, Segment $\times$ Segment.
- **Root Preservation**: Returns all analytical roots explicitly (`rootIndex: 0, 1`) without silent pruning.
- **Metric Measurements**: Segment lengths, radii, and point-to-point Euclidean distances.
- **Strict Predicate Verification**: `PERPENDICULAR_TO`, `PARALLEL_TO`, `EQUAL_LENGTH`, `POINT_ON_CIRCLE` (tolerance $\varepsilon = 10^{-4}$).
- **Kinematics & Dynamic Geometry**: Moving free vertices (`move()`) dynamically cascades dependent macro constructions.
- **Scratchpads & Rollbacks**: `fork()` and `rollback()` create 100% isolated sandboxes for speculative exploration.
- **GSA / PGS-2D Packaging**: Export and import self-describing solution envelopes with independent Zero-Trust verification.

---

## 5. What it CANNOT do (Explicit Capability Boundaries)

The Skill is **not an omniscient automated theorem solver** and explicitly documents the following boundaries:

- **NO 3D Stereometry**: Strictly limited to 2D Euclidean planimetry. Spatial polyhedra and 3D cross-sections are out of scope.
- **NO Arbitrary Angle Measurement**: Verifies orthogonality ($90^\circ$) and parallelism ($0^\circ / 180^\circ$); does not measure arbitrary angles (`ANGLE_VALUE` is a `CAPABILITY GAP`).
- **NO Compound Polygon Primitive**: Pentagons, trapezoids, etc., exist only as collections of segments (`POLYGON` is a `CAPABILITY GAP`).
- **NO Global Solution Branch Tree (`BranchTree`)**: `rootIndex` is a local parametric order ($t_0 < t_1$) along a directed line vector; reversing vector direction swaps root indices.
- **NO Automatic Intersection Recomputation**: Points from `intersect()` are static materialized snapshots, not live reactive macros.
- **NO Bit-Identical Artifact Hashes**: `artifactId` uses `Date.now()`, causing timestamp variation in the GSA envelope even when the PGS-2D geometry payload is bit-deterministic.

---

## 6. Architecture & System Boundary

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                             OUTSIDE BOUNDARY                                │
│                            AI REASONING AGENT                               │
│     (Reasoning, Hypothesis Formation, Proof Strategy, Agent Guidance/DRA)   │
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
│   ├── FullGeometryState (Authoritative State, single source of truth)       │
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

## 7. How `HeadlessGeometrySession` Works

```typescript
import { HeadlessGeometrySession } from './src/headless/HeadlessGeometrySession';

// 1. Initialize session on a blank canvas
const session = new HeadlessGeometrySession({ profile: 'blank' });

// 2. Create geometry
session.create_point(0, 0, { id: 'A' });
session.create_point(4, 0, { id: 'B' });
session.create_point(0, 3, { id: 'C' });

const sAB = session.connect('A', 'B', 'SEGMENT').created[0].id;
const sAC = session.connect('A', 'C', 'SEGMENT').created[0].id;

// 3. Verify relations deterministically
const check = session.verify(sAC, 'PERPENDICULAR_TO', sAB);
console.log(check.verified); // true (diff: 0.0, threshold: 0.0001)

// 4. Export solution artifact
session.recordCertifiedClaim('c1', 'PERPENDICULAR_TO', sAC, sAB);
const gsa = session.exportSolutionArtifact();
```

---

## 8. Deterministic Reasoning Anchor (DRA)

**DRA** is an agent-facing reasoning pattern, **not** an executable runtime API. It guides external LLMs on *when* and *how* to invoke the Stand:

1. **RECOGNIZE**: Identify formalizable geometric structures in the problem.
2. **DECIDE**: Evaluate if external deterministic verification materially strengthens reasoning. Conscious refusal (`DRA = NO`) is valid.
3. **VERIFY**: Project the minimal geometric fragment into the Stand.
4. **SEPARATE**: Strictly divide `TOOL-VERIFIED FACT` $\neq$ `AGENT INTERPRETATION` $\neq$ `AGENT HYPOTHESIS`.
5. **UPDATE**: Ground subsequent proof steps upon the verified anchor.

*Reference*: See [docs/AGENT_GUIDANCE_DRA.md](./docs/AGENT_GUIDANCE_DRA.md).

---

## 9. GSA / PGS-2D & Zero-Trust Verification

- **GSA v0.1 (Geometric Solution Artifact)**: A self-describing JSON container encapsulating resolver metadata, PGS-2D geometry payload, and certified claims.
- **Zero-Trust Rule**:
  $$\text{Producer Claim} \neq \text{Receiver Truth}$$
- **Independent Verification**: When Agent B receives an artifact from Agent A, Agent B mounts the geometry into a clean session and evaluates all claims against its own local Stand kernel. Any discrepancy results in status `'MISMATCH'` and state rollback.

*Reference*: See [docs/GSA_SPECIFICATION.md](./docs/GSA_SPECIFICATION.md).

---

## 10. Public API Quick Reference

| Action / Capability | Public Method | Return Type | Description |
|:---|:---|:---:|:---|
| **Create Point** | `create_point(x, y, options)` | `CompactActionResponse` | Adds an analytical point $(x, y)$ |
| **Connect** | `connect(p1, p2, type)` | `CompactActionResponse` | Constructs a `SEGMENT` or infinite `LINE` |
| **Create Circle** | `create_circle(center, radius)` | `CompactActionResponse` | Constructs a circle $C(O, R)$ |
| **Construct Macro** | `construct(type, params)` | `CompactActionResponse` | Constructs perpendiculars, bisectors |
| **Intersect** | `intersect(id1, id2)` | `CompactActionResponse` | Returns array of analytical root points |
| **Move** | `move(pointId, x, y)` | `CompactActionResponse` | Displaces vertex; cascades dependent macros |
| **Observe** | `observe(mode, targetId?)` | `StructuredObservation` | Modes: `COMPACT`, `FULL`, `OBJECT`, `PROVENANCE` |
| **Measure** | `measure(id, 'SEGMENT_LENGTH')`| `SemanticQuantity[]` | Returns exact metric length |
| **Verify** | `verify(sub, rel, ref)` | `VerificationResult` | Fixed tolerance verification ($\varepsilon = 10^{-4}$) |
| **Fork / Rollback** | `fork()`, `rollback(id)` | `string` / `boolean` | Sandbox management |
| **Export GSA** | `exportSolutionArtifact()` | `GSASolutionArtifact` | Exports solution container |
| **Import GSA** | `importSolutionArtifact(gsa)` | `GSAImportReport` | Zero-Trust import and verification |

---

## 11. Local Setup, Testing & Verification

### Prerequisites
- Node.js $\ge 18$
- npm $\ge 9$

### Reproducible Workflow

```bash
# 1. Install dependencies
npm install

# 2. Run the complete test suite (27/27 test suites)
npm run test:all

# 3. Run TypeScript typecheck (0 errors)
npm run lint

# 4. Compile production build
npm run build

# 5. Run the minimal standalone example
npx tsx examples/basic-verification/index.ts
```

### Confirmed Audit Baseline
- **Autonomous Kernel Tests**: 74 / 74 PASS (100%)
- **Environment Contract Tests**: 18 / 18 PASS (100%)
- **Headless Skill PoC Tests**: 11 / 11 PASS (100%)
- **Total Test Suites**: **27 / 27 PASS (100%)**
- **Typecheck (`tsc --noEmit`)**: **0 errors**
- **Production Build**: **Vite build clean**

---

## 12. Canonical Documentation Index

All documentation is located in the [`docs/`](./docs/) directory using relative Markdown links:

- [`docs/AI_GEOMETRY_SKILL.md`](./docs/AI_GEOMETRY_SKILL.md) — Canonical navigational entry point.
- [`docs/AI_GEOMETRY_SKILL_PASSPORT.md`](./docs/AI_GEOMETRY_SKILL_PASSPORT.md) — Immutable Skill Passport v0.1 (identity, capabilities, boundaries, policies).
- [`docs/SKILL_CONTRACT.md`](./docs/SKILL_CONTRACT.md) — Semantic Contract v0.4 specifications.
- [`docs/CAPABILITY_MODEL.md`](./docs/CAPABILITY_MODEL.md) — Supported vs unsupported capabilities.
- [`docs/AGENT_GUIDANCE_DRA.md`](./docs/AGENT_GUIDANCE_DRA.md) — Recommended agent reasoning pattern (DRA).
- [`docs/GEOMETRY_RESEARCH_METHODOLOGY.md`](./docs/GEOMETRY_RESEARCH_METHODOLOGY.md) — Autonomous experimental protocol.
- [`docs/PROVENANCE_EPISTEMIC_MODEL.md`](./docs/PROVENANCE_EPISTEMIC_MODEL.md) — Epistemic truth tiers and lineage.
- [`docs/GSA_SPECIFICATION.md`](./docs/GSA_SPECIFICATION.md) — GSA envelope and Zero-Trust verification.
- [`docs/PATTERN_LIBRARY.md`](./docs/PATTERN_LIBRARY.md) — Audited Geometric Reasoning Pattern Palette.
- [`docs/KNOWN_LIMITATIONS.md`](./docs/KNOWN_LIMITATIONS.md) — Unified capability gap register.

---

## 13. Project Lineage

AI Geometry Skill was developed from the Geometry Reasoning Stand research architecture and extracted as a standalone agent-facing instrument. Historical developmental materials, evolution notes, and earlier milestone documents are preserved in [`docs/archive/`](./docs/archive/) for academic reproducibility and lineage tracing.

---

## 14. License

Distributed under the **MIT License**. See [`LICENSE`](./LICENSE) for details.
