---
source_path: /data/yantianwei/000_AutoResearch/10_mmKG/artifacts/idea/selected_idea.md
ingested: 2026-05-12
sha256: a70758f4df2acd61
---
# Selected Idea: BridgeKG-LLM

**Idea ID:** bridgekg-llm-v1
**Date selected:** 2026-05-09
**Stage:** Idea → ready for Experiment

---

## Two-Sentence Pitch

We adapt OKG-LLM to bridge structural health monitoring by constructing a Bridge Structural Knowledge Graph (BsKG) that encodes sensor-to-component-to-span relationships as KG triples, then using the KG to condition a frozen GPT-2 backbone on each sensor's structural role for multi-step natural frequency prediction. We show that KG-conditioned prediction residuals are more discriminative for detecting progressive structural damage in the Z24 international SHM benchmark than LLM-only (TimeLLM) and GNN-only (TPS-GNN) baselines.

---

## Falsifiable Claim

**Claim:** On Z24 natural frequency prediction at τ=8/16/32 hours, BridgeKG-LLM achieves lower MSE than TimeLLM (no KG) and DLinear, and prediction residuals during the 16 progressive damage scenarios show AUROC > 0.70 for damage detection.

**Direction:** Lower MSE is better; higher AUROC is better.

**Evidence required:** Numeric tables matching the OKG-LLM paper format (Table I style), ablation removing BsKG, ablation replacing BsKG with a trivial positional-only KG.

---

## Why Now

Bridge SHM produces growing volumes of sensor data but lacks methods that encode structural physics as relational knowledge for LLM conditioning. OKG-LLM (TKDE 2026) has just established the KG-LLM alignment paradigm for scientific domain TS prediction. TPS-GNN (SHM 2025) showed that spatiotemporal GNNs achieve up to 78% improvement over prior bridge prediction models, showing the field is competitive. The Z24 benchmark remains open with no KG+LLM method. This is the right moment to bring all three lines together.

---

## Contribution Frame

| Contribution | Type |
|---|---|
| First application of KG-augmented frozen LLM to bridge SHM sensor TS prediction | Capability |
| Bridge Structural KG (BsKG) schema encoding sensor, cross-section, span entities with structural relations | Resource |
| Empirical demonstration that structural KG context improves natural frequency prediction over LLM-only baseline | Performance |
| Z24 evaluation protocol linking prediction error to damage detection AUROC | Evaluation |

---

## Data and Evaluation Contract

| Item | Choice | Rationale |
|---|---|---|
| Primary dataset | Z24 Bridge (KU Leuven / EMPA, Switzerland) | International SHM benchmark; labeled damage scenarios; 12 months healthy + 16 damage levels; publicly recognized |
| Input signal | Hourly natural frequencies extracted from raw acceleration (5 modes from STFT or FDD) OR raw 10-min averaged acceleration at 16 sensor channels | Natural frequency: cleaner TS, compact (5 channels); raw acceleration: higher fidelity, 16 channels |
| Prediction task | Multi-step ahead prediction at τ=8/16/32 steps (hourly steps) | Mirrors OKG-LLM horizon structure; enables direct MSE table comparison |
| Primary metric | MSE (hourly natural frequency or sensor channel value) | Same as OKG-LLM; lower is better |
| Secondary metric | AUROC for damage detection (prediction residual vs. damage label) | Connects prediction to SHM application value |
| Baselines | DLinear, TimeLLM (no KG), OKG-LLM with trivial geo-KG, TPS-GNN-style (GNN only) | Covers non-KG, no-LLM, and partial-KG ablations |

---

## Code-Level Plan

### Step 1: Z24 Data Extraction (Week 1-2)
- Extract the 1-year ambient monitoring period from `data-z24.zip` (Z24ems1.zip, Z24ems2.zip, Z24ems3.zip; each contains hourly MAT files).
- Compute hourly natural frequencies using OMA (Operational Modal Analysis): apply Frequency Domain Decomposition (FDD) to 8-minute acceleration blocks per hour at 16 channels.
- Output: `dataset/z24_natural_freq.csv` — shape (N_hours, 5), where N_hours ~8760.
- Alternative shortcut: The Z24 dataset already provides pre-extracted environmental data (temperature, humidity) and in some public versions pre-extracted natural frequencies. Check if a frequency CSV is included in the Z24 EMS data.

### Step 2: Bridge Structural KG Construction (Week 1)
- Schema design: Entity types = {sensor, cross_section, span}; Relation types = {measured_at, part_of_span, adjacent_to, same_span_as}.
- Z24 has: 4 spans (1 main span 30m + 2 side spans 14m, 1 approach span); 33 accelerometers at 16 locations (plus environmental sensors); 5 natural frequencies.
- Map modal frequencies to KG: frequency_mode_1 ... frequency_mode_5 as sensor entities; cross-sections as structural entities.
- Output: `kg_data/bridge_z24/entities.dict`, `kg_data/bridge_z24/relations.dict`, `kg_data/bridge_z24/train.txt`.
- Expected KG size: ~30-50 entities, ~100-200 triples (much smaller than OKG at 1829/4602; Z24 is a compact benchmark bridge).

### Step 3: TransE Pre-training on BsKG (Week 2)
- Use existing `pretrain_kg/run_pretrain.sh` with modified `--data_path kg_data/bridge_z24/` and reduced embedding dim if needed.
- Expected runtime: < 1 hour on RTX PRO 5000 (tiny KG).
- Output: `pretrain_kg/emb_bridge_z24.pth`.

### Step 4: New Dataset_Z24 Loader (Week 2)
- Add `Dataset_Z24` class to `data_provider/data_loader.py` (or a new file `data_provider/data_loader_z24.py`).
- Input: `z24_natural_freq.csv`; shape: (N_hours, N_freq_modes).
- entity-to-channel mapping: mode_1 → entity index 0, ..., mode_5 → entity index 4.
- Split: train (first 60%), val (next 10%), test (last 30% including damage period if possible, or test on damage period separately).
- Register in `data_factory.py` as `data_dict['Z24'] = Dataset_Z24`.

### Step 5: Run BridgeKG-LLM Training (Week 3)
- Copy `scripts/OKGLLM_SST.sh` → `scripts/OKGLLM_Z24.sh`, modify: `--data Z24`, `--root_path dataset/`, `--data_path z24_natural_freq.csv`, `--num_ent <bridge_kg_size>`, `--kg_path kg_data/bridge_z24/`, `--pred_len 8/16/32`.
- Run on single RTX PRO 5000 (48GB VRAM; easily fits GPT-2 40M + small data).
- Expected training time: < 30 min per run (5 channels vs. 1716 channels in SST; much smaller data).

### Step 6: Evaluation and Ablation (Week 3-4)
- Record MSE/MAE at τ=8/16/32 for: BridgeKG-LLM, TimeLLM (no KG), DLinear, BridgeKG-LLM without KG (ablation).
- Compute damage detection AUROC: use test-period prediction residuals vs. binary damage label from Z24 damage scenario metadata.
- Generate spatial error visualization: which modal frequencies benefit most from KG context.

---

## Bridge Structural KG Design (BsKG)

### Entity Types and Examples
```
sensor:       mode_1_freq, mode_2_freq, ..., mode_5_freq  (natural frequency channels)
cross_section: cs_L0 (abutment), cs_L14 (14m span midpoint), cs_L30 (main span midpoint), cs_R14, cs_R0
span:          span_left_approach (14m), span_main (30m), span_right_approach (14m)
pier:          pier_left, pier_right
material:      post-tensioned_concrete, box_girder_2cell
environment:   temperature_sensor, humidity_sensor
```

### Relation Types
```
measured_at:     mode_1_freq  measured_at  cs_L30   (mode 1 most sensitive at main span midpoint)
part_of_span:    cs_L30       part_of_span  span_main
adjacent_to:     cs_L14       adjacent_to   cs_L0
structurally_similar_to: span_left_approach  structurally_similar_to  span_right_approach
influenced_by:   mode_1_freq  influenced_by  temperature_sensor
material_is:     span_main    material_is   post-tensioned_concrete
```

### Mode-Structure Physical Mapping (from Z24 literature)
- Mode 1 (bending ~3.9 Hz): most sensitive to stiffness at main span; influenced by temperature (bridge deck expansion/contraction).
- Mode 2 (torsion ~5.1 Hz): sensitive to torsional stiffness; pier conditions.
- Mode 3 (~10 Hz): sensitive to local cross-section properties.
- Modes 4-5: higher modes, more sensitive to local damage scenarios.

This physics-grounded mapping from mode to structural entity is the core KG knowledge that differentiates BridgeKG-LLM from pure data-driven methods.

---

## Minimal Experiment (Gate 1: Proof of Concept)

**Target:** Get BridgeKG-LLM running on Z24 natural frequency data with BsKG, within 2 weeks.

**Success criterion (gate 1):** BridgeKG-LLM MSE < DLinear MSE on Z24 frequency prediction for at least one horizon.

**If gate 1 passes:** Run all baselines, ablations, damage detection evaluation.
**If gate 1 fails:** Diagnose whether the KG is too small to be informative; consider augmenting with additional KG entities (neighboring bridges, structural standards, mode-to-physics rules).

---

## Literature Relation and Evidence Pointers

| Claim | Evidence pointer |
|---|---|
| KG + frozen LLM outperforms non-KG LLM for scientific TS | OKG-LLM (P1, TKDE 2026): 7.5-15.1% MSE improvement |
| Z24 is a valid international SHM benchmark | Z24 DNN paper (P5), GAN digital twin Z24 (P6), unsupervised Z24 (P12) |
| Natural frequency as primary SHM indicator | TFT for SHM (P7), Environmental factors paper (multiple) |
| Spatial sensor topology matters for bridge prediction | TPS-GNN (P9): 78% improvement using spatial distribution |
| Bridge KG for LLM reasoning is feasible | BMKG-DCoT (P3): 1165-entity bridge KG + LLM achieves F1=0.95 |
| No prior work combines KG + LLM + bridge sensor TS | Literature survey gap finding (Section 4) |

---

## Strongest Alternative Hypothesis

The performance improvement of BridgeKG-LLM over TimeLLM on Z24 may come primarily from the RevIN normalization and patch-based encoding (which TimeLLM also has) rather than from the BsKG. If ablation shows removing the KG has minimal impact, the paper's claim about structural knowledge alignment would be weakened. Mitigation: design ablation with a "random KG" (random triples, same size) to separate structural vs. noise KG effects.

---

## Strongest Likely Objection

"The Z24 bridge is very small (5 modal frequencies, 3-span box girder). A real bridge SHM system has hundreds of sensors with complex spatial relationships. The BsKG on Z24 is trivially small (< 50 entities) and cannot demonstrate that KG alignment provides non-trivial structural context."

**Counter-argument:** (1) OKG-LLM's ocean KG also has well-characterized entity types; the contribution is in the alignment mechanism, not the KG size. (2) The paper should include bridge3.sql Yuehai Bridge (110 sensors, arch bridge) as a second dataset with a richer KG to demonstrate scalability. (3) Z24 is specifically chosen because it has labeled damage scenarios — a unique advantage for SHM evaluation that richer datasets without labels cannot provide.

---

## Decision: SELECTED for Experiment Stage

**Primary experiment:** BridgeKG-LLM on Z24 natural frequency prediction (C1 + C3 combined).

**Secondary experiment (if time allows, or as second paper section):** BridgeKG-LLM on bridge3.sql Yuehai Bridge multi-sensor prediction (C2).

**Branching decision:** NOT branching. C1+C3 is the primary line. C2 is a stretch goal for scalability demonstration.

**Branching rejection rationale:** C2 alone has no labeled benchmark and weaker novelty positioning. C1+C3 first, then C2 if C1+C3 results are strong.
