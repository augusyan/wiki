---
title: Wiki Schema
created: 2026-05-12
updated: 2026-05-13
type: concept
tags: [schema, meta]
---

# Wiki Schema

## Domain

AI/ML Research: Knowledge Graph-Enhanced Time Series Forecasting, Structural Health Monitoring, Information Extraction, and Disinformation Defense.
This wiki covers research on KG+LLM integration, scientific time-series prediction, NLP/IE methods, and related topics.

## Conventions

- File names: lowercase, hyphens, no spaces
- Every wiki page starts with YAML frontmatter
- Use `[[wikilinks]]` to link between pages (minimum 2 outbound links per page)
- When updating a page, always bump the `updated` date
- Every new page must be added to `index.md`
- Every action must be appended to `log.md`

## Frontmatter

```yaml
---
title: Page Title
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: entity | concept | comparison | query | template
tags: [from taxonomy below]
sources: [raw/...]
confidence: high | medium | low
---
```

## Tag Taxonomy

### ML Methods
`kg-enhanced-forecasting`, `llm-patch-reprogramming`, `time-series`, `dlinear`, `timellm`, `okg-llm`

### NLP / IE
`information-extraction`, `ner`, `relation-extraction`, `event-extraction`, `acl2025`, `emnlp2025`

### Domains
`shm`, `bridge-monitoring`, `ocean-sst`, `scientific-ml`, `disinformation`, `misinformation-defense`, `social-media`

### Data
`z24-bridge`, `noaa-sst`, `modal-analysis`, `natural-frequency`

### KG
`knowledge-graph`, `transe`, `kg-embedding`, `structural-kg`, `bskg`

### Research Practice
`experiment-result`, `literature-survey`, `ablation`, `negative-result`, `nsfc`, `inoculation-theory`

### Writing & Visualization
`paper-writing`, `imrad`, `abstract`, `rebuttal`, `drawing`, `figure-design`, `visualization`

### Meta
`meta`, `schema`, `template`, `index`

## Page Thresholds

- Create a page when an entity/concept appears in 2+ sources or is central to one source
- Add to existing page when a source mentions something already covered
- DON'T create a page for passing mentions
- Split pages exceeding ~200 lines

## Update Policy

When new info conflicts with existing:
1. Check dates — newer generally supersedes
2. If contradictory, note both with dates
3. Flag for review
