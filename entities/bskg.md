---
title: BsKG (Bridge Structural Knowledge Graph)
created: 2026-05-08
updated: 2026-05-12
type: entity
tags: [bskg, structural-kg, knowledge-graph, shm, bridge-monitoring]
sources: [raw/articles/bridgekg-quest-plan.md, raw/articles/selected-idea.md]
confidence: medium
---

# BsKG — Bridge Structural Knowledge Graph

## Overview

BsKG is a domain-specific knowledge graph encoding the structural relationships of the Z24 Bridge, built for KG-enhanced natural frequency prediction.

## Structure

### Entities (19)
- **Modal frequencies**: mode_1_freq through mode_5_freq
- **Physical components**: cross-sections (CS1-CS5), spans (S1-S3), piers (P1-P4)
- **Materials**: concrete, steel
- **Sensors**: accelerometers at measurement points

### Relations (6)
- `measured_at` — sensor → cross-section
- `part_of_span` — cross-section → span
- `adjacent_to` — span ↔ span
- `structurally_similar_to` — cross-section ↔ cross-section
- `influenced_by` — mode ↔ cross-section (based on modal sensitivity)
- `material_is` — cross-section → material

### Triples
28 triples encoding the bridge's structural physics. Example:
- `mode_1_freq influenced_by CS3` (mode 1 most sensitive to mid-span)
- `CS3 part_of_span S2` (cross-section 3 in span 2)
- `CS3 material_is concrete`

## Versions

- **v1**: Original design, 19 entities, 6 relations, 28 triples
- **v2**: Enhanced with additional sensor-component relationships

## Performance

Neither v1 nor v2 showed significant improvement over no-KG baseline on Z24 frequency prediction. The KG is too sparse (28 triples vs OKG's 4,602) to provide meaningful conditioning signal for the frozen LLM.

See also: [[bridgekg-llm]], [[z24-bridge]]
