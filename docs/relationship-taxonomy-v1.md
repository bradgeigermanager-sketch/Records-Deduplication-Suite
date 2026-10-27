# Record Relationship Taxonomy — Draft 1.0.0

**Status:** Initial proposal for review; not yet adopted as a cross-repository contract.  
**Scope:** Distinguishes what records represent from what actions a system may take.

## 1. Keep three decisions separate

1. **Relationship classification:** what relationship is supported by evidence?
2. **Deduplication eligibility:** does that relationship permit considering consolidation?
3. **Resolution/action:** what should the system do, given evidence, policy, retention requirements, and review needs?

A relationship classification alone must never trigger destructive merging.

## 2. Relationship codes

| Code | Meaning | Default merge eligibility |
|---|---|---|
| `EXACT_DUPLICATE` | Exact same content/record representation, usually supported by a stable identifier or cryptographic hash | Candidate for safe de-duplication of representations; preserve source/provenance |
| `RECORD_DUPLICATE` | Distinct stored representations of the same underlying record | Eligible for reviewed canonical linkage |
| `OBSERVATION_DUPLICATE` | Multiple records represent the same observation, rather than merely the same subject | Eligible only when observation identity is supported |
| `COPIED_FROM` | One record is copied from another | Link records; optionally collapse duplicate display |
| `TRANSFORMED_FROM` | One representation was transformed from another | No automatic merge; retain lineage |
| `DERIVED_FROM` | One record was analytically derived from one or more records | No automatic merge; retain lineage |
| `AGGREGATE_OF` | A record summarizes or combines multiple records | No automatic merge |
| `VERSION_OF` | Same logical record with a distinct version | Retain versions; optionally show a current projection |
| `CORRECTION_OF` | Explicit correction of an earlier record | Retain both and link; apply authority rules separately |
| `SUPERSEDES` | A later record explicitly supersedes another within a defined scope | Retain history; mark current status only under an applicable rule |
| `SUPPLEMENT_OF` | Adds information without replacing the earlier record | Retain both |
| `SAME_SUBJECT` | Concerns the same entity, but may describe a distinct observation/event | Not sufficient for merge |
| `SAME_EVENT` | Concerns the same event, but may be a separate observation or source | Not sufficient for merge |
| `INDEPENDENT_OBSERVATION` | Independently created observation, potentially corroborating another | Do not merge away independence |
| `CORROBORATES` | Independently supports some assertion in another record | Retain separately |
| `CONFLICTS_WITH` | Contains a materially incompatible assertion | Retain both; surface conflict |
| `PARTIAL_OVERLAP` | Some assertions or segments overlap, others differ | No automatic merge |
| `COMPLEMENTARY` | Contains distinct information about the same matter | Retain and link |
| `RELATED_TO` | Meaningful relationship not more specifically classified | No automatic merge |
| `UNRELATED` | No material relationship found within the assessed scope | Retain separately |
| `INDETERMINATE` | Available evidence is insufficient or contradictory | Abstain; retain separately and optionally review |

## 3. Independent dimensions

A relationship assessment should separately represent:

- **Record identity:** same underlying recording or distinct recordings.
- **Subject identity:** same entity or different entities.
- **Observation identity:** same observation or separate observations.
- **Event identity:** same event or different events.
- **Provenance:** copied, replicated, transformed, derived, aggregated, or independent.
- **Content:** exact, normalized-equivalent, semantically similar, partially overlapping, complementary, contradictory, or unknown.
- **Time and space:** compatible, incompatible, imprecise, or unknown under type-specific rules.

Do not collapse these into one label. For example, two records can be about the same subject and event while documenting independent observations.

## 4. Evidence and confidence

Each material evidence item should include an evidence ID, the compared fields, the comparison result, method/rule version, source record references, and any known limitations. Preserve contradictory and missing evidence.

Use confidence only when its meaning is documented. A score is not a probability unless calibrated and evaluated against a representative, labeled test set. Deterministic evidence such as a verified source record ID or cryptographic hash must be distinguished from heuristic similarity.

## 5. Resolution actions

Suggested action codes:

- `RETAIN_SEPARATELY`
- `LINK_AS_DUPLICATE`
- `LINK_AS_VERSION`
- `LINK_AS_CORRECTION`
- `LINK_AS_SUPPLEMENT`
- `LINK_AS_SAME_SUBJECT`
- `LINK_AS_SAME_EVENT`
- `LINK_AS_CORROBORATION`
- `LINK_AS_CONFLICT`
- `LINK_AS_DERIVED`
- `MARK_SUPERSEDED`
- `SEND_TO_REVIEW`
- `DEFER`
- `NO_ACTION`

A system should not physically delete or overwrite source records as part of ordinary deduplication. Canonical records should be projections that retain links to all source records, alternate values, conflicts, and decisions.

## 6. Timestamp and authority safeguards

A newer timestamp is not, by itself, proof that a record is more authoritative or that an older record is false. Distinguish event time, observation time, source creation time, modification time, publication time, and ingestion time. Apply a precedence rule only when the relevant domain defines its authority, scope, and effective period.

## 7. Versioning and compatibility

This document is draft `1.0.0`. Before using these codes as an API/database contract:
- define stable machine-readable identifiers and descriptions;
- define whether each relationship is directional or symmetric;
- define allowed inverse relationships;
- define state transitions and correction/supersession semantics;
- publish examples and negative examples;
- add validation and regression tests;
- review interoperability with the Records Search Engine and related projects.
