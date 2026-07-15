# Module 3 Journal

## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/153

**Issue title:** Faithfulness checker crashes when a context chunk has `text: None`

**Tier:** [x] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
<!--
TODO (YOU write this — it is graded and must be in YOUR OWN WORDS, 3–5 sentences.
Do NOT paste the technical notes below verbatim; use them to understand the bug,
then explain it yourself: what the issue is, what's currently broken, and what a
successful fix accomplishes. Mentioning the affected part of the codebase helps.)

Technical notes to understand it (reference only — not for submission):
- Affected code: rag/evaluator/faithfulness_checker.py, the FaithfulnessChecker.check() method (lines ~34–36).
- It builds context via `chunk.get("text", "") for chunk in context_chunks`, then `" ".join(...)`.
- dict.get's default only applies when the key is ABSENT. If the key "text" exists
  but its value is None, .get returns None — so a None reaches " ".join(...).
- " ".join([...]) requires all items to be str, so a None item raises:
  TypeError: sequence item 0: expected str instance, NoneType found.
- A correct fix handles a None text value gracefully (e.g. treat it as "") so the
  checker returns a score instead of crashing. The related regression test is
  test_none_context_chunk_text in tests/unit/test_faithfulness_checker.py.
-->

**Branch name:** fix/153-faithfulness-checker-none-crash

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [ ] Issue added to cohort ledger
<!-- Check this box yourself AFTER you've added the row to your section's tab. -->

### "Is this right for me?" checklist — selection notes
<!--
TODO (YOU write this — graded). Work through the official "Is this right for me?"
checklist (linked in the assignment resources) for issue #153 and note your scope
reasoning here in your own words: why the scope fits a Tier-1 first contribution,
which files you expect to touch, whether it has a clear reproduction and a testable
outcome, and any risks/unknowns. I did not have the checklist link, so paste the
actual checklist items and answer each one.
-->
