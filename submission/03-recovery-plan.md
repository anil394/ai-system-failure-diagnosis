# Prioritized Recovery Plan — Outdated Policy Answer

## Decision

- **Recommendation:** Contain, then Investigate. Do not roll back the whole assistant.
- **Decision owner:** Head of People Operations (content) with the AI platform lead (system).
- **Reason in one sentence:** The cause is not verified, the harm is moderate and contained by a banner and fallback, so the cheapest evidence should come before any model or architecture change.
- **Evidence that would change the recommendation:** Several policies found stale (pause the assistant for policy topics); any restricted document appearing for the wrong role (pause immediately); the retrieval trace showing v4 top-ranked (shift work to prompt and generation).

## Actions

| Priority | Horizon | Action | Linked cause or unknown | Impact | Effort | Risk reduction | Confidence | Reversibility | Owner |
|---|---|---|---|---|---|---|---|---|---|
| 1 | Contain | Show a "verify in the wiki" banner on travel answers and route travel-expense questions to the fallback | Any cause; symptom only | Medium | Low | Medium | High | High | Platform lead |
| 2 | Investigate | Pull the retrieval trace for the failing query, check whether v4 is in the index, and audit the ingestion history since v4 was published | H1, H2, H3 | High | Low | Medium | High | High | Platform engineer |
| 3 | Investigate | Sample 10 other policies (synthetic number) and compare index version with wiki version | Scope: is it systemic? | High | Medium | High | Medium | High | Platform engineer + People Ops |
| 4 | Correct | Smallest fix the evidence supports: re-index v4 and mark v3 superseded; if ranking is the cause, filter or boost by "status = current" | H1 or H2 | High | Medium | High | Medium (rises after action 2) | High | Platform team |
| 5 | Prevent | Turn the failing question into a regression case; add a nightly check that index versions equal wiki versions and alert on drift | All | High | Medium | High | High | High | Platform team |

## Options Considered

| Option | Why it could help | Why not first? | Reversible? |
|---|---|---|---|
| Workflow or non-ML change: policy owners mark superseded versions and must trigger re-indexing at publish time; banner and fallback | Removes the failure at the source and costs almost nothing | Depends on People Ops process discipline; does not fix a pipeline bug | Yes |
| Small architecture correction: retrieval filter on status and effective date; validator that shows version and date | Targets H1/H2 directly | Only worth doing once the trace shows which layer failed | Yes |
| Larger model, data, or platform change: fine-tune the model, or switch the vector store | Might help general quality | Does not address stale content, costly, and unsupported by any evidence | Hard |

## Proof of Recovery

| Level | Measure or test | Baseline | Pass or guardrail threshold | Owner |
|---|---|---|---|---|
| User or workflow | Travel-expense claims rejected due to wrong rule; "looks wrong" reports on policy answers | Current weekly counts (to be measured) | Trending to zero for four weeks | People Ops |
| Retrieval or context | Share of test questions where the current version is in the top 3 passages | Measured before fix on the fixed set | 100% on the original case, at least 95% on the 30-case set (synthetic target) | Platform engineer |
| Generation or action | Answers that cite the current version with effective date | Measured before fix | 100% on the original case; 0 answers citing a superseded version in the test set | Platform engineer |
| Operations | Index-vs-wiki drift check; time from publish to indexed | Unknown today | Drift alert fires within 24h; indexing within 24h of publishing | Platform lead |
| Risk | Permission tests: restricted policies never returned to unauthorised roles | Current results | 0 leaks in the adversarial set | Security |

## Release and Rollback

- **Release scope:** Re-enable travel-policy answers first for a small pilot group, then all employees after two clean weeks.
- **Monitoring required:** Weekly drift report, feedback button volume, regression suite on every prompt, model, index or ranker change.
- **Rollback or shutdown trigger:** Any answer that cites a superseded policy in the regression set, any permission leak, or drift alerts unresolved for more than 48 hours. Response: policy topics return to the fallback.
- **Fallback experience:** "I can't confirm the current rule; here is the policy page and the People Ops contact", with a ticket link.
- **Remaining risk and authorized risk owner:** The assistant may still be wrong on topics outside the tested set. The Head of People Operations accepts this with the disclaimer and citation dates in place.
