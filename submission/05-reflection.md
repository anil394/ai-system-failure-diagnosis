# Reflection

- **Which evidence changed your ranking of root causes most?** The fact that a second person got the same answer. It made a one-off session glitch unlikely and moved the focus to shared components: the index and the retriever.
- **Which attractive fix did you reject because it was unsupported or too broad?** Fine-tuning the model on the new policy. The symptom points at stale content reaching the model, and a fine-tune would freeze another snapshot that goes stale again.
- **Where does accountability sit when the model, data, and workflow have different owners?** With one named decision owner (Head of People Operations) who accepts remaining risk. The platform team owns the pipeline and index, and policy owners own version status. A shared "who publishes, who re-indexes" agreement closes the gap between them.
- **Which part of the architecture would be hardest to observe during a real incident?** The ingestion pipeline. It runs separately from requests, so a silent failure leaves no trace on user requests, and only a drift check between wiki and index would reveal it.
