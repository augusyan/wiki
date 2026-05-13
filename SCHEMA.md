---
title: Wiki Schema
created: 2026-05-12
updated: 2026-05-12
type: concept
tags: [schema, meta]
---

# Wiki Schema

## Domain

AI/ML Research: Knowledge Graph-Enhanced Time Series Forecasting & Structural Health Monitoring.
This wiki covers research on integrating Knowledge Graphs (KG) with Large Language Models (LLM)
for scientific time-series prediction, domain adaptation, and related topics.

## Conventions

- File names: lowercase, hyphens, no spaces (e.g., `bridgekg-llm.md`)
- Every wiki page starts with YAML frontmatter
- Use `[[wikilinks]]` to link between pages (minimum 2 outbound links per page)
- When updating a page, always bump the `updated` date
- Every new page must be added to `index.md` under the correct section
- Every action must be appended to `log.md`

## Frontmatter

```yaml
---
title: Page Title
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: entity | concept | comparison | query | summary
tags: [from taxonomy below]
sources: [raw/papers/source-name.md]
---
```

## Tag Taxonomy

- **Methods**: kg-enhanced-forecasting, llm-patch-reprogramming, time-series, dlinear, timellm, okg-llm
- **Domains**: shm, bridge-monitoring, ocean-sst, scientific-ml
- **Data**: z24-bridge, noaa-sst, modal-analysis, natural-frequency
- **KG**: knowledge-graph, transe, kg-embedding, structural-kg, bskg
- **Tasks**: forecasting, damage-detection, domain-adaptation
- **Meta**: experiment-result, literature-survey, ablation, negative-result

Rule: every tag on a page must appear in this taxonomy. Add new tags here first.

## Page Thresholds

- **Create a page** when an entity/concept appears in 2+ sources OR is central to one source
- **Add to existing page** when a source mentions something already covered
- **DON'T create a page** for passing mentions
- **Split a page** when it exceeds ~200 lines

## Entity Pages

One page per notable entity (paper, method, dataset, person). Include:
- Overview, key facts, relationships, source references

## Concept Pages

One page per concept. Include: definition, current state of knowledge, open questions, related concepts.

## Comparison Pages

Side-by-side analyses with dimensions of comparison, verdict, and sources.

## Update Policy

When new information conflicts with existing content:
1. Check dates — newer sources generally supersede
2. If contradictory, note both positions with dates
3. Flag for review
