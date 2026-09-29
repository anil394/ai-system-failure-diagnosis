# Diagnostic Register — Outdated Policy Answer

## Incident Frame

- **Observed symptom:** The assistant states the old travel-expense rule and cites a document titled "Travel Policy v3".
- **Reproducible example (synthetic):** Question: "What is the daily meal allowance for domestic business travel?" Answer: "EUR 30 per day, per Travel Policy v3." The current policy, v4, published *2 months earlier* (synthetic), sets EUR 40. Expected: EUR 40, citing v4 with its effective date.
- **Affected users or workflow:** Employees planning trips and managers approving expense claims; claims are wrongly rejected or under-claimed.
- **Impact and severity:** Medium. Financial impact is low per case, but trust damage and manual rework are significant, and it suggests other policies may be stale too.
- **First observed:** Reported by one employee (synthetic), then confirmed by a second person asking the same question.
- **Recent relevant changes:** Policy v4 was published; assistant prompt and model versions are unknown; a wiki migration *may* have occurred (unverified, listed as an unknown, not a fact).
- **Immediate containment:** Add a visible banner on travel-policy answers: "Verify against the policy wiki", and route the topic to the fallback until investigated.
- **Facts frozen for this case:** One failing request and answer (above); expected answer; two affected users; the failure exists today; v4 exists in the source wiki. Nothing else is fact.

## Evidence Available

| Evidence | Source | What it shows | Limitation | Observed, inferred, or verified? |
|---|---|---|---|---|
| Failing question and answer | Employee report + screenshot | The answer cites v3 with EUR 30 | One example, no trace | Observed (synthetic) |
| Policy v4 in wiki | Wiki page | Correct rule is EUR 40 | Says nothing about the index | Observed (synthetic) |
| Second employee reproduces | Chat | Not a one-off session glitch | No passages logged | Observed (synthetic) |
| Retrieved passages and ranks | **Not available yet** | Would show whether v4 was retrieved | Must be requested | Requested |
| Index contents and ingest history | **Not available yet** | Would show whether v4 was ever indexed | Must be requested | Requested |
| Prompt / model / config versions | **Not available yet** | Would show a recent change | Must be requested | Requested |

Nothing here is verified. Only a trace or index inspection can separate the causes below.

## Competing Hypotheses

| Rank | Hypothesis and component | Why it fits | Evidence against it | Next discriminating test | Confidence |
|---|---|---|---|---|---|
| 1 | **Ingestion gap:** v4 never reached the index, or was indexed without replacing v3 (ingestion pipeline) | Symptom is exactly "old answer, old citation"; the assistant cannot cite what it does not have | v4 might be indexed; no ingest logs seen yet | Search the index for v4 chunks and check the ingest job history for the publish date | Medium |
| 2 | **Ranking ignores recency:** both v3 and v4 are indexed and v3 outranks v4 (retriever) | Old documents often have more chunks, more link text and a longer history, so they score higher | If v4 is absent from the index, this is moot | Run the failing query directly against the retriever and inspect ranked passages with version metadata | Medium |
| 3 | **Model picks the wrong passage:** both versions are in context and the LLM chooses v3 (prompt/generation) | Prompt may lack a rule such as "prefer the newest effective version" | Only applies if v4 was in context; needs the final context to judge | Inspect final prompt context for the failing request; replay it with an explicit newest-wins instruction | Low |
| 4 | **Cache or stale session:** an earlier answer is served from a cache or a conversation state (orchestrator) | Two people got the same answer | Cache existence unknown; second user had a fresh session (synthetic) | Ask platform team if a cache exists; repeat the query with cache bypassed | Low |
| 5 | **Source metadata wrong:** v3 is still marked "current" in the wiki (content ownership) | Would let a healthy pipeline serve v3 correctly | v4 is visible in wiki, but its status field is unchecked | Compare the status/effective-date properties of v3 and v4 in the wiki | Low |

## Next-Best Evidence

- **The next observation or test we would request:** The retrieval trace for the failing query: the ranked passages, each with document version, effective date and index timestamp.
- **Why it best separates the leading hypotheses:** If v4 is absent, it is an ingestion problem (H1). If both appear and v3 ranks higher, it is ranking (H2). If v4 ranks higher but the answer is still v3, it is generation (H3). One trace splits three causes.
- **Who can provide or run it:** Platform engineer with retriever and log access.
- **Decision that depends on it:** Whether to fix the ingestion pipeline, adjust ranking, or change the prompt, and whether other policies need re-indexing now.

## AI Challenge Note

The first draft of this register was challenged by an AI assistant (Claude) using only synthetic, non-sensitive information. The author reviewed each suggestion; the decisions are recorded below. None of them is verified evidence.

| LLM suggestion | How it was checked | Accepted, edited, or rejected | Reason |
|---|---|---|---|
| Add "metadata wrong in wiki" as a cause (H5) | Reviewed against the case facts | Accepted | Cheap to check and not covered by the first draft |
| Fine-tune the model on the new policy | Compared with the symptom and evidence | Rejected | Stale retrieval is not a knowledge gap in the model; fine-tuning would bake in another snapshot |
| Consider cache as a cause (H4) | Two users saw the same answer | Edited | Kept at low confidence since a shared index also explains it |
| Claim "the index is certainly missing v4" | No index evidence exists | Rejected | An unverified root cause remains a hypothesis |

## Current Diagnosis

- **Verified cause, if any:** None.
- **Best-supported hypothesis if not verified:** H1, an ingestion gap (v4 missing or v3 not superseded), with H2 as close second.
- **Important uncertainty:** We do not know whether v4 is in the index at all.
- **Evidence that would change the diagnosis:** v4 present and top-ranked in retrieval (moves the cause to generation or cache); or several other policies also stale (points to a systemic ingestion failure rather than one document).
