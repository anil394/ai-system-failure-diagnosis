# Presenting Script (about 3 minutes)

## Slide 1 — What is broken (~60s)
"Our case is an internal policy chatbot. It answers employee questions by searching company documents and letting an AI model write the reply. This is an invented case for the exercise.

An employee asked about the daily meal allowance. The bot said 30 euros and cited policy version 3. The current version 4 says 40 euros. Two people saw the same answer, so it isn't a one-off.

The impact is wrongly rejected expense claims and lost trust. For now we added a warning banner and send travel questions to a fallback. What we know for sure is one failing answer and that version 4 exists. Everything about how the system works inside is an assumption."

## Slide 2 — Why we think so (~75s)
"We have three main candidate causes.

One, an ingestion gap: version 4 never reached the search index, or version 3 was never retired. Two, ranking: both versions are there but the old one scores higher. Three, generation: the model sees both and picks the old one.

I rank the first two as medium confidence and the third as low. None is verified. The one thing that separates them is the retrieval trace for the failing question. If version 4 is missing, it's ingestion. If it's there but lower, it's ranking. If it's first but ignored, it's the model."

## Slide 3 — What we recommend (~60s)
"We recommend containing first and investigating second. No rollback, and no retraining of the model, because the evidence points to stale documents, not missing model knowledge.

The plan: keep the banner, get the trace and audit the index, check ten other policies to see if it's systemic, apply the smallest fix the evidence supports, then turn this failure into a regression test with a drift alert.

We call it recovered when the original question returns version 4 and no test answer cites an old version. The owners are People Ops for content and the AI platform lead for the system. If many policies are stale, or a permission leak appears, we pause policy answers."

## If asked
- **Why not fine-tune?** The symptom is stale content reaching the model. Fine-tuning freezes another snapshot that also goes stale.
- **Is the root cause proven?** No. It's the best-supported hypothesis until the trace confirms it.
- **Is this real data?** No, it's synthetic and labelled as such.
