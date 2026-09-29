# Three-Slide Decision Summary

Also as [.pptx](04-three-slide-summary.pptx) and [.html](04-three-slide-summary.html).

## 1. What is broken
- **System:** Policy chatbot (RAG), synthetic case
- **Symptom:** Says EUR 30 (policy v3); current v4 says EUR 40
- **Impact:** Wrong expense claims, lost trust
- **Now:** Warning banner + fallback for travel questions

## 2. Why we think so
1. Ingestion gap: v4 not indexed (Medium)
2. Ranking: v3 outranks v4 (Medium)
3. Model picks v3 from mixed context (Low)

**Next test:** Retrieval trace for the failing question

## 3. What we recommend
- **Decision:** Contain, then investigate, no rollback
- **Plan:** Banner, trace, audit, smallest fix, regression test
- **Proof:** Original case passes; zero old-version citations
- **Owner:** People Ops + AI platform lead
