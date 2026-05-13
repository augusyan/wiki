---
source_path: /data/yantianwei/000_AutoResearch/10_mmKG/artifacts/idea/candidates.md
ingested: 2026-05-12
sha256: e1502919dd5f5b86
---
# BridgeKG-LLM: Research Direction Candidates

**Date:** 2026-05-09
**Stage:** Idea — divergence + convergence pass

---

## 1. Success Target Lock

| Dimension | Value |
|---|---|
| Primary metric | MSE (lower is better); also MAE |
| Strongest baseline | OKG-LLM (SST): MSE 0.1042/0.1334/0.1631 at τ=8/16/32 |
| Expected contribution type | **Performance + Capability** (new domain + improved structural alignment) |
| Problem importance | Bridge SHM sensors lack principled domain-knowledge-guided forecasting; natural frequency shifts are the most reliable structural health indicator; existing DL methods do not encode structural physics as relational knowledge |
| Main challenge / bottleneck | No bridge structural KG exists for this task; must construct one from available data; must establish a valid bridge SHM evaluation benchmark that has TS ground truth and structural context |

---

## 2. Bounded Divergence Pass (6-12 Raw Ideas)

Generated without judgment. Labeled by lens used.

| ID | Raw Idea | Lens |
|---|---|---|
| R1 | Treat each Z24 bridge accelerometer as a KG entity; encode physical coupling (same span → structurally_coupled; same cross-section → co-located; same pier → grounded_at) as relations; use OKG-LLM architecture to predict hourly natural frequency derived from STFT of raw acceleration. | Analogy transfer (ocean grid → bridge accelerometer) |
| R2 | Use bridge3.sql data (阅海大桥, multiple sensor types: strain, deflection, acceleration, cable force, temperature); build multi-type sensor KG; predict future sensor readings multi-step; leverage sensor_type as a KG entity class. | Abstraction down (from bridge to individual sensor type) |
| R3 | Extend OKG-LLM with a temporal KG: add time-stamped triples for seasonal temperature events, traffic peak periods, and known anomaly windows; align temporal KG triples to time-series context window. | Tension hunting (static KG vs. dynamic signal) |
| R4 | Use Z24 damage scenarios as a downstream evaluation: train BridgeKG-LLM on healthy-state natural frequency TS; use reconstruction error on damage periods as anomaly score; the KG provides structural context for which sensors are most diagnostic. | Negation (invert prediction into detection) |
| R5 | Replace TransE with RotatE on bridge KG to capture asymmetric structural relationships (sensor A is_upstream_of sensor B, but B is_not_upstream_of A). | Constraint manipulation (change KGE method) |
| R6 | Use a multi-resolution KG: span-level entities (4 entities for Z24), cross-section-level entities (12 per span), and sensor-level entities (3 per cross-section for Z24); hierarchical cross-attention over three KG levels. | Composition (decompose spatial resolution) |
| R7 | Use bridge3.sql (Yuehai Bridge, Ningxia, arch bridge); build a structural KG from the sensor naming convention (SST_G13_01_01 → span 13, section 01, sensor 01); the naming encodes hierarchy implicitly. | Simplicity test (naming = structural knowledge) |
| R8 | Use the bridge inspection/damage reports in bridge3.sql (analy_crack_outlier, disease info) to create a damage-aware KG with defect entities; predict sensor readings and jointly detect incipient damage. | Abstraction up (raw TS → health state) |
| R9 | Rather than a wholly new domain, extend OKG-LLM to the SST + auxiliary variable setting first (co-predict SST and sea surface height); use existing code; provides a simpler novelty claim. | Conservative route |
| R10 | Build a physics-informed bridge KG encoding Euler-Bernoulli beam theory (sensor at L/4 has known theoretical relationship to sensor at L/2 through mode shape equations); use physics equations as KG relation weights. | "Why now" / analogy from structural engineering |
| R11 | Focus on the alignment mechanism improvement: instead of per-channel cross-attention, use a two-stage alignment where sensors first attend to their local KG (same cross-section) then to a global KG (whole bridge); mirrors the OKG-LLM region-level vs. full-graph hierarchy. | Composition |
| R12 | Combine Z24 (clean benchmark, labeled damage, international recognition) with bridge3.sql (real-world Chinese bridge, more sensors, long time-series, multi-type sensors); use Z24 for primary evaluation and bridge3.sql for scalability demonstration. | Composition (two datasets) |

---

## 3. Five Ranked Research Directions

### Direction D1 — BridgeKG-LLM on Z24 Natural Frequency (Conservative)

**Targeted limitation:** Limitation 2 (ocean-specific KG schema) and Limitation 5 (data format).

**Problem:** No method combines structural-knowledge-encoded LLMs with bridge sensor TS for SHM. Natural frequency shifts are the gold-standard health indicator; their prediction from environmental + structural context is an open problem.

**Solution approach:** Adapt OKG-LLM to Z24: (1) extract hourly natural frequencies (5 modal frequencies) from the Z24 1-year ambient monitoring as the target TS; (2) build a Bridge Structural KG (BsKG) from the Z24 sensor layout with 3 entity types (sensor, cross-section, span) and 4 relation types (measured_at, part_of_span, adjacent_cross_section, same_span_as); (3) train TransE on BsKG; (4) wire existing OKG-LLM code with a new Dataset_Z24 and a BsKG entity file.

**Key technique:** Structural KG construction from sensor layout; natural frequency extraction from raw acceleration as the target TS.

**Code-level implementation sketch:**
- New file: `data_provider/data_loader_z24.py` → `Dataset_Z24` class, reads hourly natural frequency CSV.
- New file: `kg_data/bridge/` → `entities.dict`, `relations.dict`, `train.txt` for BsKG.
- New script: `pretrain_kg/run_pretrain_bridge.sh` → TransE training on BsKG.
- Modify: `run_main.py` → add `--data Z24` branch.
- Reuse: all of `models/OKGLLM.py`, `layers/`, `pretrain_kg/` with zero change.

**Metrics to watch:** MSE of hourly natural frequency prediction at τ=8/16/32 hours.

**Success threshold:** BridgeKG-LLM MSE < TimeLLM MSE on Z24 natural frequency prediction (analogous to OKG-LLM vs. TimeLLM in the ocean domain).

**Abandonment criteria:** If BridgeKG-LLM MSE is not statistically better than TimeLLM without KG after 3 ablation runs.

**Risks:** (1) Z24 data requires preprocessing to extract natural frequencies (STFT or operational modal analysis); adds 1-2 weeks of effort. (2) Z24 is 10GB compressed; preprocessing may require extracting a representative subset.

**ROI:** High. Minimal new code; clear benchmark; recognized dataset.

---

### Direction D2 — BridgeKG-LLM on bridge3.sql Multi-type Sensor (Higher-Upside)

**Targeted limitation:** Limitation 2 (KG schema) and Limitation 3 (channel independence).

**Problem:** Bridge monitoring systems have multi-type sensors (strain, deflection, acceleration, cable force, temperature) that are structurally related. No method encodes these structural relationships as a KG to condition cross-sensor LLM predictions.

**Solution approach:** Use bridge3.sql Yuehai Bridge data (arch bridge, 110 sensors from cmct_sensor, 2.4M readings in cmct_sensor_data since 2019); decode sensor naming convention (SST_G13_01_01 = sensor type SST, span G13, cross-section 01, sensor 01) to automatically construct a structural KG; include relation types: measured_at_section, part_of_span, same_type_as, adjacent_section, sensor_is_strain/deflection/vibration.

**Key technique:** Auto-construction of BsKG from structured sensor naming; multi-type sensor entities.

**Code-level implementation sketch:**
- New script: `kg_data/bridge_yuehai/build_kg_from_sql.py` → parse cmct_sensor naming, emit KG triples.
- New file: `data_provider/data_loader_bridge3.py` → reads extracted CSVs from bridge3.sql (via mysqldump parsing or PostgreSQL restore).
- Modify: data_factory.py to add bridge3 dataset entry.
- Reuse: all model and KG encoder code.

**Metrics to watch:** MSE of 10-minute-ahead sensor value prediction for strain and deflection channels.

**Success threshold:** BridgeKG-LLM MSE < DLinear and TimeLLM on bridge3 sensor prediction.

**Abandonment criteria:** If SQL extraction and preprocessing exceeds 2 weeks; if data quality is too poor (too many null/incorrect readings as indicated by analy_sensor_incorrect table).

**Risks:** (1) bridge3.sql is MySQL format, and the dw_monitor processed tables appear empty (INSERT counts = 0); raw data is in cmct_sensor_data. (2) Data may have gaps; analy_sensor_incorrect shows 349K incorrect readings. (3) This is a proprietary Chinese bridge dataset with no published baselines — harder to position against peers.

**ROI:** Medium-high. Higher novelty (first real-world multi-bridge application) but higher engineering cost.

---

### Direction D3 — Z24 Damage-Aware BridgeKG-LLM with Anomaly Detection Head (Elegance)

**Targeted limitation:** Limitation 3 (channel independence) + Limitation 4 (generic LLM).

**Problem:** The most impactful SHM task is not just predicting future sensor readings, but using prediction residuals to detect structural damage. A KG-conditioned prediction model naturally separates "expected signal given structural context" from "actual signal deviating from expectation" — making KG context a principled source of structural prior for anomaly scoring.

**Solution approach:** Train BridgeKG-LLM (D1 architecture) on Z24 healthy-period natural frequencies; add a lightweight anomaly scoring head: anomaly score = L2 distance between predicted and observed natural frequency; evaluate on Z24's 16 progressive damage scenarios. Compare against: TimeLLM (no KG), TFT (no KG), autoencoder baseline (from Z24 literature).

**Key technique:** Prediction-residual-based anomaly scoring is zero additional code (just evaluate prediction error during damage periods). The KG conditioning hypothesis is that a model with structural knowledge will better predict the expected healthy-state frequency, making damage deviations more clearly anomalous.

**Code-level implementation sketch:**
- Build on D1 exactly.
- Add evaluation script: `scripts/eval_damage_z24.py` → loads test predictions on damage period, computes residuals vs. damage scenario labels.
- No new model code needed.

**Metrics to watch:** AUROC of anomaly detection across 16 damage scenarios, in addition to natural frequency MSE.

**Success threshold:** AUROC > 0.75 for damage detection (competitive with Z24 autoencoder baselines from P6).

**Abandonment criteria:** If Z24 raw data extraction and modal frequency computation takes > 3 weeks; if structural damage signal is too small relative to environmental variability.

**Risks:** Environmental effects on natural frequency are large (temperature-driven frequency shifts can exceed damage-driven shifts in Z24 — a well-documented challenge). The KG may not capture enough temperature context without explicit temperature entities. This may require adding temperature as a KG entity or as an exogenous input.

**ROI:** Medium. High scientific value (bridges KG + LLM into SHM detection), but added risk on damage detection evaluation being confounded by environmental variability.

---

### Direction D4 — Pure Sensor Forecasting on bridge3.sql without Z24 (Alternative)

**Targeted limitation:** Limitation 5 (data format).

**Problem:** If Z24 preprocessing is too costly, bridge3.sql provides multi-year sensor data from a real Chinese highway bridge.

**Solution approach:** Extract strain time-series from cmct_sensor_data for the 110 sensors on the Yuehai Arch Bridge (单sensor_type=4, strain); aggregate to hourly means; build a minimal BsKG from the sensor naming scheme; train and evaluate BridgeKG-LLM.

**Key technique:** SQL extraction + sensor naming decoding.

**Metrics to watch:** MSE of hourly strain prediction at τ=8/16/32 hours.

**Abandonment criteria:** If the extracted time-series has too many gaps (>50% missing) making meaningful evaluation impossible.

**ROI:** Lower (no public benchmark, no international recognition of bridge3 dataset, harder to defend novelty without a comparable baseline).

---

### Direction D5 — Multi-variable Ocean + Bridge Cross-Domain Generalization (Ambitious)

**Targeted limitation:** Limitation 1 (static single-domain KG).

**Problem:** Demonstrate that the KG-LLM alignment paradigm generalizes across fundamentally different physical domains by training variants on SST (ocean) and Z24 (bridge) with different KGs but the same model architecture.

**Solution approach:** Implement D1 (Z24 variant), then run OKG-LLM (SST) and BridgeKG-LLM (Z24) back-to-back. Analyze whether knowledge-enhanced LLM alignment provides consistent gains across both physical domains. Could also include domain adaptation: pre-train KG encoder on OKG, fine-tune on BsKG.

**Key technique:** Cross-domain evaluation; potentially KG encoder transfer.

**Risks:** High. Two full pipelines required. Cross-domain KG transfer is architecturally non-trivial. This is a multi-paper story.

**ROI:** Low for a first paper; more appropriate for a follow-up.

---

## 4. Compact Candidate Set (3 candidates)

| Candidate | Direction | Risk level | Code change | Benchmark quality |
|---|---|---|---|---|
| **C1 (Conservative)** | D1: Z24 natural frequency prediction | Low | Small | High (international benchmark) |
| **C2 (Higher-upside)** | D2+D3 combined: bridge3.sql multi-sensor + anomaly detection on Z24 | Medium-High | Medium | Medium-High |
| **C3 (Elegance)** | D3: Z24 damage detection as evaluation on top of C1 | Low | Zero additional model | High |

Note: C1 and C3 share the same model training; C3 adds only an evaluation step on top of C1. Therefore C1+C3 is the natural first experiment.

---

## 5. Candidate Scoring

| Criterion | C1 (D1) | C2 (D2+D3) | C3 (D3 only, on top of C1) |
|---|---|---|---|
| Relevance to limitation | 5/5 | 5/5 | 5/5 |
| Feasibility in current codebase | 5/5 | 3/5 | 5/5 |
| Expected upside | 3/5 | 4/5 | 4/5 |
| Clarity of 2-sentence pitch | 5/5 | 3/5 | 4/5 |
| Falsifiability | 5/5 | 4/5 | 5/5 |
| Implementation cost | 5/5 | 3/5 | 5/5 |
| Evaluation clarity | 5/5 | 3/5 | 5/5 |
| Novelty headroom | 4/5 | 5/5 | 4/5 |
| Research value | 4/5 | 5/5 | 5/5 |
| **Total** | **41/45** | **35/45** | **42/45** |

**Winner: C1 + C3 combined = BridgeKG-LLM on Z24 for natural frequency prediction + damage detection evaluation.**

---

## 6. Quality Gate

### C1 + C3 combined:

**Two-sentence pitch:** We adapt OKG-LLM to bridge structural health monitoring by constructing a Bridge Structural Knowledge Graph encoding sensor-component-span relationships, and using it to condition frozen GPT-2 on each sensor's structural context for natural frequency prediction. We show that KG-conditioned prediction errors are more discriminative for detecting progressive damage in the Z24 bridge benchmark than LLM-only and GNN-only baselines.

**Strongest likely objection:** "The Z24 bridge has only 5 natural frequency channels; this is too simple a prediction task to justify KG complexity — TPS-GNN or TFT already solves it without a KG."

**Counter-argument:** The KG provides semantic structural context beyond what topology alone conveys (e.g., which mode shape is most sensitive to damage at a specific cross-section); the LLM pre-trained sequence modeling captures long-range temporal dependencies that pure GNNs cannot; and the same architectural contribution generalizes to richer multi-sensor bridge settings.

**Why now:** Bridge SHM produces unprecedented volumes of sensor data; the OKG-LLM paradigm (TKDE 2026) has just established that KG-conditioned LLMs beat GNNs and plain LLMs for scientific domain TS prediction; Z24 remains an open international benchmark with unsolved problems (TPS-GNN just showed 78% improvement over prior GNNs in 2025, showing the field is rapidly advancing and competitive results are achievable).

**Novelty verdict:** Confirmed gap (zero published papers combine KG + LLM + bridge sensor TS). Novelty is application novelty + mechanism novelty (structural KG schema is new; structural knowledge as alignment prior is new).

**Falsification path:** If BridgeKG-LLM MSE >= TimeLLM MSE on Z24 natural frequency, the KG alignment hypothesis for bridge SHM is falsified for this data.

**Quality gate score:** 9/10. PASS.
