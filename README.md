# Records Deduplication Suite

A framework for determining whether records are duplicates, versions, corrections, supplements, independent observations, corroborating evidence, conflicting assertions, or otherwise related records. The suite is designed to support safe record linkage and reconciliation **without destroying source records or their provenance**.

## Core rule

> Similarity is evidence of possible identity, not identity itself. Deduplication consolidates representations; it does not erase provenance.

A matching subject, timestamp, location, or similar description is not sufficient by itself to establish that two records are duplicates. Where the evidence cannot distinguish duplication from legitimate independence, retain both records and mark the relationship unresolved or send it for review.

## Repository contents

| File | Purpose | Current role |
|---|---|---|
| [Suite overview](deduplication%20of%20records%20suite%20overview.txt) | Core framework, identity dimensions, evidence hierarchy, decision classes, checklist, merge policy, and proposed module set | Conceptual specification |
| [Record Relationship Determination procedure](Record%20Relationship%20Determination%20%28RRD%29%20Procedure.txt) | Determines how records relate before deciding whether deduplication is appropriate | Primary procedural specification |
| [DeduplicationRecordIR](deduplication%20record%20ir.txt) | Initial outline of the foundational machine-readable record representation | Preliminary data-model note |
| [Multiple-node recording discussion](multiple%20nodes%20in%20the%20same%20information%20system%20independently%20recording%20or%20analysing%20the%20same%20subjects.txt) | Discusses overlap and independent recording across distributed information-system nodes | Design input; review its broad claims against the suite's more cautious rules |

## Recommended processing pipeline

1. **Intake and preserve** — retain the source record, source identifiers, original values, timestamps, content hash where appropriate, and acquisition provenance.
2. **Normalize** — add normalized representations without overwriting original values; record the normalization method and version.
3. **Generate candidates** — use bounded blocking keys or indexes to find plausible pairs/groups and record why each candidate was selected.
4. **Determine relationships** — evaluate record, subject, observation, event, temporal, spatial, content, and provenance relationships separately.
5. **Assess evidence** — record supporting evidence, contradictions, missing data, uncertainty, and the limits of any score.
6. **Decide eligibility** — distinguish duplicate candidates from versions, corrections, supplements, independent observations, corroboration, and conflicts.
7. **Resolve and canonicalize** — create a linked canonical representation only when justified; preserve source records and alternate/conflicting values.
8. **Audit and re-evaluate** — retain the rule/model version, evidence snapshot, reviewer or process identity, rationale, and any later revalidation.

Relationship determination must precede deduplication. A relationship such as `SAME_SUBJECT`, `CORROBORATES`, `CONFLICTS_WITH`, `VERSION_OF`, or `DERIVED_FROM` does not, by itself, authorize a merge.

## Canonical outcome vocabulary

Use explicit, non-binary outcomes. The initial vocabulary proposed in the specifications includes:

- `DUPLICATE`, `LIKELY_DUPLICATE`, `POSSIBLE_DUPLICATE`, `UNRESOLVED`, `LIKELY_DISTINCT`, `DISTINCT`
- `VERSION_OF`, `CORRECTION_OF`, `SUPPLEMENT_OF`, `RELATED_TO`
- Relationship types such as `COPIED_FROM`, `DERIVED_FROM`, `AGGREGATE_OF`, `CORROBORATES`, `CONFLICTS_WITH`, and `INDEPENDENT_OBSERVATION`

Before implementation, these values should be consolidated into a versioned schema with definitions, allowed transitions, and compatibility rules. Do not treat the preliminary lists in the source documents as a finalized machine contract.

## Planned module boundaries

The suite overview proposes the following composable information representations (IRs):

- `DeduplicationRecordIR`
- `DeduplicationCandidateIR`
- `RecordComparisonIR`
- `TemporalComparisonIR`
- `SpatialComparisonIR`
- `EntityComparisonIR`
- `SemanticComparisonIR`
- `EvidenceIR`
- `DeduplicationAssessmentIR`
- `RecordClusterIR`
- `CanonicalRecordIR`
- `MergeDecisionIR`
- `ConflictIR`
- `ResolutionIR`
- `DeduplicationAuditIR`
- `DeduplicationRuleIR`
- `DeduplicationConfigurationIR`

These are a proposed design inventory, not a claim that each module is implemented. The first implementation milestone should define and validate the shared record and evidence schemas before building scoring or merge logic.

## Safety and integrity requirements

- Never delete or overwrite source records as a side effect of deduplication.
- Preserve original values alongside normalized values.
- Never infer duplicate identity from subject, timestamp, location, or text similarity alone.
- Treat independent observations and independent provenance as potentially valuable evidence, not noise to be collapsed.
- Do not automatically resolve conflicting assertions by choosing the newest timestamp without an explicit, domain-appropriate authority rule.
- Keep candidate generation, relationship classification, merge eligibility, and merge execution as separate stages.
- Make every automated decision explainable and reproducible.
- Require review or abstain when evidence is insufficient, contradictory, or high-impact.
- Use synthetic or appropriately authorized test data for development and validation.

## Development roadmap

### Phase 1 — Specification consolidation
- [ ] Define a versioned JSON Schema for `DeduplicationRecordIR`.
- [ ] Define shared identifier, timestamp, location, entity-reference, provenance, uncertainty, and evidence primitives.
- [ ] Reconcile the terminology in the overview and RRD procedure.
- [ ] Define a versioned relationship/outcome vocabulary and decision table.

### Phase 2 — Candidate comparison
- [ ] Implement normalization that preserves originals.
- [ ] Implement bounded candidate generation and explainable comparison results.
- [ ] Add temporal, spatial, entity, document/content, and provenance comparison modules.
- [ ] Keep similarity scores distinct from calibrated probabilities and authoritative decisions.

### Phase 3 — Resolution and audit
- [ ] Implement immutable assessment and review records.
- [ ] Implement duplicate clusters and canonical projections without source deletion.
- [ ] Preserve conflicts, alternate values, version history, and provenance links.
- [ ] Add revalidation when source evidence or rules change.

### Phase 4 — Verification and integration
- [ ] Add unit, property-based, and regression tests for false-positive and false-negative cases.
- [ ] Test copied, transformed, aggregated, corrected, superseded, and independently observed records.
- [ ] Add fixtures for missing data, conflicting identifiers, clock skew, imprecise locations, and schema mismatch.
- [ ] Document API contracts, persistence requirements, migration strategy, and interoperability with the Records Search Engine and related libraries.

## Current status

This repository currently contains conceptual and procedural text, including a substantial suite overview and RRD procedure. This README organizes the existing material and identifies the next steps; it does **not** imply that an executable deduplication engine, validated schemas, or automated test suite already exists.

## Related projects

These are potential integration points to evaluate, not established dependencies:

- [records-search-engine](https://github.com/bradgeigermanager-sketch/records-search-engine) — indexed document retrieval, source/version navigation, and reviewed cross-repository relationships.
- [Data-Source-Mapping-And-Overlap-Comparison-Suite](https://github.com/bradgeigermanager-sketch/Data-Source-Mapping-And-Overlap-Comparison-Suite) — data-source comparison and overlap metrics.
- [Multi-Temporal-Lineage-Comparison-And-Originality-Derivation-Engine](https://github.com/bradgeigermanager-sketch/Multi-Temporal-Lineage-Comparison-And-Originality-Derivation-Engine) — temporal lineage and originality analysis.
- [Data-Types-and-Templates-Library](https://github.com/bradgeigermanager-sketch/Data-Types-and-Templates-Library) and [Forms-and-Templates-Library](https://github.com/bradgeigermanager-sketch/Forms-and-Templates-Library) — candidate homes for shared schema and form templates.

Integrations should be established through explicit versioned contracts and reviewed links, rather than inferred from repository names alone.


## Draft machine-readable artifacts

- [DeduplicationRecordIR JSON Schema](schemas/deduplication-record-ir.schema.json) — initial structural contract; Draft 2020-12 meta-schema validation is included in CI.
- [Relationship taxonomy](docs/relationship-taxonomy-v1.md) — relationship codes and default merge eligibility rules.
- [Independent observations example](examples/independent-observations.json) — illustrates why shared subject identity does not establish duplicate observations.

- [Relationship assessment JSON Schema](schemas/record-relationship-assessment.schema.json) — draft contract for evidence-backed pairwise relationship assessments.
- [Independent-observation assessment example](examples/relationship-assessment-independent-observations.json) — example assessment intended to retain two independent observations separately.
- [Schema validation workflow](.github/workflows/validate-schemas.yml) — checks Draft 2020-12 schemas and validates the assessment example on relevant changes.

- [Relationship decision table v1](docs/relationship-decision-table-v1.md) — conservative default actions separating relationship labels from deduplication eligibility.

- [Valid DeduplicationRecordIR fixture](examples/deduplication-record-valid.json) — synthetic record fixture for schema validation; contains no real-world record data.
