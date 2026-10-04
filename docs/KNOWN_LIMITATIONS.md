# Known Limitations & Capability Gap Register

> **System Version**: `v1.0.0`  
> **Status**: `FROZEN`  
> **Policy**: Explicit Documentation over Premature Claims

---

The following capability gaps and non-blocking architectural constraints are formally documented for AI Geometry Skill v1.0.0:

### 1. Solution Branch Identity & BranchTree
- **Classification**: `CAPABILITY GAP`
- **Behavior**: `rootIndex` is a local parametric ordering ($t_0 < t_1$) along the directed vector from $P_1$ to $P_2$. If the vector order is reversed ($P_2 \to P_1$), `rootIndex` values swap. A persistent global `BranchTree` is not implemented in v1.0.0.

### 2. Materialized Intersection Snapshots
- **Classification**: `IMPLEMENTATION BEHAVIOR / GAP`
- **Behavior**: Points created via `intersect()` are static snapshots in `state.points`. Displacing parent lines or circles via `move()` does not automatically recompute previously created intersection coordinates.

### 3. Artifact Identity Timestamp Determinism
- **Classification**: `CAPABILITY GAP`
- **Behavior**: The `artifactId` field in the GSA envelope uses `gsa_${Date.now()}`. Re-exporting identical geometric states yields different string envelope IDs, although the underlying PGS-2D payload remains 100% analytically deterministic.

### 4. Epistemic VANISHED State Visibility
- **Classification**: `NOT REPRESENTABLE THROUGH CURRENT PUBLIC SKILL API`
- **Behavior**: Truth decay is tracked internally within the graph layers, but the public `verify()` method returns only a boolean status (`verified: false`). The discrete status `VANISHED` is not exported through the public interface.

### 5. GSA Legacy Triangle Stand Namespace Collision
- **Classification**: `IMPLEMENTATION BEHAVIOR / GAP`
- **Behavior**: If an external agent creates free points with exact literal IDs `'A'`, `'B'`, and `'C'`, `exportSolutionArtifact()` invokes the legacy Triangle Stand projector (`projectStateToPgsPassport`), causing segment ID and center point mismatches during Zero-Trust import. Agents must use arbitrary or prefixed IDs (e.g. `'P_A'`, `'P_B'`).

### 6. Arbitrary Angular Measurement
- **Classification**: `CAPABILITY GAP`
- **Behavior**: The system evaluates perpendicularity ($90^\circ$) and parallelism ($0^\circ / 180^\circ$), but does not provide an arbitrary angular measurement predicate `ANGLE_VALUE`.

### 7. Stereometry (3D Solid Geometry)
- **Classification**: `CAPABILITY GAP / OUT OF SCOPE`
- **Behavior**: The system operates strictly within $\mathbb{R}^2$ Euclidean planimetry. Spatial polyhedra and 3D cross-sections cannot be represented.
