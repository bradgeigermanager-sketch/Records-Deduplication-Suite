# Record Relationship Decision Table v1

**Status:** Initial policy draft. This table guides review; it is not an automatic merge authorization.

## Decision sequence

1. Define the record boundary and preserve each original record.
2. Compare record identity, subject identity, observation identity, event identity, and provenance independently.
3. Classify the relationship using the relationship taxonomy.
4. Determine eligibility separately from relationship classification.
5. Record supporting and contradicting evidence, uncertainty, rule version, and rationale.
6. If the evidence is incomplete, contradictory, high-impact, or dependent on an unverified authority rule, abstain or require review.
7. Execute any permitted consolidation only through a separate, auditable action that preserves source records and provenance.

## Relationship-to-action defaults

| Relationship class | Default eligibility | Default action | Rationale / guardrail |
|---|---|---|---|
| `EXACT_DUPLICATE` | Review evidence | `MERGE_WITH_PROVENANCE` only when identity and provenance support it | Exact content alone is not proof; byte-identical independent observations may still be distinct records. |
| `RECORD_DUPLICATE` | Review evidence | `LINK_ONLY` pending policy or `MERGE_WITH_PROVENANCE` after approval | Confirm both are representations of one underlying recording. |
| `OBSERVATION_DUPLICATE` | Review evidence | `LINK_ONLY` or approved consolidation | Establish same observation, not merely same subject/event/time. |
| `COPIED_FROM`, `TRANSFORMED_FROM`, `DERIVED_FROM`, `AGGREGATE_OF` | Not eligible from relationship alone | `LINK_ONLY` | Preserve lineage; transformed/derived content may add information or change meaning. |
| `VERSION_OF`, `SUPERSEDES` | Not eligible from relationship alone | `CREATE_VERSION_LINK` | Preserve earlier versions and effective-time distinctions. |
| `CORRECTION_OF` | Not eligible from relationship alone | `CREATE_CORRECTION_LINK` | Retain corrected record and correction provenance; do not erase the original assertion. |
| `SUPPLEMENT_OF`, `COMPLEMENTARY`, `PARTIAL_OVERLAP` | Not eligible from relationship alone | `SUPPLEMENT_CANONICAL` or `LINK_ONLY` | These may contain non-overlapping information. |
| `SAME_SUBJECT`, `SAME_EVENT` | Not eligible from relationship alone | `RETAIN_SEPARATELY` or `LINK_ONLY` | Shared subject/event identity does not establish shared record or observation identity. |
| `INDEPENDENT_OBSERVATION` | `NOT_ELIGIBLE` by default | `RETAIN_SEPARATELY` | Independence may be valuable corroborating evidence. |
| `CORROBORATES` | `NOT_ELIGIBLE` by default | `RETAIN_SEPARATELY` or `LINK_ONLY` | Corroboration is a relationship between evidence, not proof of duplication. |
| `CONFLICTS_WITH` | `NOT_ELIGIBLE` by default | `RETAIN_SEPARATELY` and review | Preserve competing assertions; do not resolve by timestamp alone. |
| `RELATED_TO` | `UNDETERMINED` | `LINK_ONLY` or review | Relationship is too general to authorize consolidation. |
| `UNRELATED` | `NOT_ELIGIBLE` | `NO_ACTION` | Keep distinct. |
| `INDETERMINATE` | `UNDETERMINED` | `DEFER` or `ESCALATE_FOR_REVIEW` | Abstain when evidence cannot support a defensible classification. |

## Evidence requirements before a duplicate decision

A reviewer or future rule engine should consider:

- **Record identity:** stable source record IDs, transaction/document IDs, hashes, version identifiers, and replication identifiers.
- **Observation/event identity:** observation IDs, observer/source node, event identifiers, time windows, location, methods, and measurement context.
- **Provenance:** whether records share an origin, are copied/replicated, or were independently generated.
- **Content:** exact, normalized, semantically similar, conflicting, or complementary assertions.
- **Temporal semantics:** distinguish event, observation, creation, modification, ingestion, publication, and replication times.
- **Authority and integrity:** provenance integrity, source reliability, known corruption, and applicable domain rules.
- **Uncertainty:** missing fields, clock skew, coordinate precision, ambiguous entity matches, and alternative explanations.

No single score, timestamp, shared subject, location, or text-similarity result should automatically authorize a merge.

## Machine-schema alignment

The [relationship assessment schema](../schemas/record-relationship-assessment.schema.json) enforces a narrow first guardrail: selected relationship classes cannot be marked `ELIGIBLE`. This is intentionally conservative and does not yet encode every row in this table. Future schema versions should use explicit policy versions and controlled transitions rather than assuming relationship labels alone decide eligibility.

## Required assessment record

For every pair assessed, store at minimum:

- assessment ID and both record IDs;
- relationship type and separate deduplication eligibility;
- recommended action;
- evidence with direction and limitations;
- confidence (if meaningful and calibrated);
- rationale, review requirement, rule version, and assessment time.

Keep source records immutable. A merge should create a new canonical projection or linkage record, with traceable links to all source records and preserved conflicting/alternate values.
