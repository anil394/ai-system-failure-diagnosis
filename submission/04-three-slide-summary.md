# Three-Slide Decision Summary

Slides (also available as `04-three-slide-summary.pptx`).

---

## Slide 1 — What Is Broken

**System:** Internal policy assistant (RAG chatbot; synthetic case).
**Symptom:** Asked about domestic meal allowance, it answers EUR 30 citing Policy v3. Current v4 says EUR 40.
**Impact:** Wrongly rejected or under-claimed expenses, lost trust. Two employees affected.
**Containment now:** Banner "verify in wiki" + fallback for travel questions.

*Facts:* one failing answer, v4 exists, two users. *Assumptions:* pipeline, ranking and prompt behaviour.

---

## Slide 2 — Why We Think It Is Broken

| Rank | Hypothesis | Confidence |
|---|---|---|
| 1 | v4 not indexed / v3 not superseded (ingestion) | Medium |
| 2 | v3 outranks v4 (retrieval) | Medium |
| 3 | Model picks v3 from mixed context (generation) | Low |

**Missing evidence:** retrieved passages, index contents, config versions.
**Next test:** retrieval trace for the failing query. Absent v4 = ingestion; present but lower = ranking; top-ranked but ignored = generation.

---

## Slide 3 — What We Recommend

**Decision:** Contain, then investigate. No rollback, no fine-tuning.
1. Banner + fallback (today). 2. Trace + index audit. 3. Sample 10 other policies. 4. Smallest fix. 5. Regression case + drift alert.

**Proof of recovery:** original case passes; 95%+ on 30 cases; zero superseded citations; drift alert within 24h.
**Owner:** Head of People Ops + AI platform lead.
**Would change the plan:** many stale policies → pause policy topics; permission leak → pause now.
