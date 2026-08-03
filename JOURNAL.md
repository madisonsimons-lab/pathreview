# Module 3 Journal

## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/153

**Issue title:** Faithfulness checker crashes when a context chunk has `text: None`

**Tier:** [x] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
The faithfulness checker is designed to evaluate whether a generated response is supported by the provided context and return a faithfulness score. The issue occurs because chunk.get("text", "") returns None when the text key exists with a None value, causing " ".join(...) to raise a TypeError. As a result, the checker crashes instead of completing the evaluation. The fix is to treat None values as empty strings so the checker handles missing text gracefully and returns a score instead of an error.

**Branch name:** fix/153-faithfulness-checker-none-crash

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger

### "Is this right for me?" checklist — selection notes
1. **Can I reproduce it reliably?** Yes, I can reproduce it reliably by running FaithfulnessChecker().check("Knows Python.", [{"text": None}]), which causes a TypeError.
2. **Is the scope small and well-bounded?** Yes, the scope is small and likely only requires changes to two files.
3. **Which files do I expect to change?** I expect to change rag/evaluator/faithfulness_checker.py and tests/unit/test_faithfulness_checker.py.
4. **Is there a clear, testable success condition?** Yes, the success condition is that the checker handles None text values without crashing and returns a faithfulness score.
5. **Does it fit my skill level (Tier 1)?** Yes, it is a good first contribution because it is a small bug fix with a clear cause, simple logic change, and an existing test case.
6. **What are the risks/unknowns?** The main risk is that other parts of the code may expect text to always be a string, so the fix should avoid changing normal text processing behavior.

## Week 8 — Reproduction & solution planning

**Reproduction commit link:**
https://github.com/madisonsimons-lab/pathreview/commit/ccc67eaaf6c122103887fc4c24a86365e5378125


**Reproduction summary:**
I reproduced the issue by running FaithfulnessChecker().check("Knows Python.", [{"text": None}]), which caused the program to throw a TypeError. I also ran the failing unit test test_none_context_chunk_text, which confirmed the same error because the checker attempted to join a None value with strings.

**PLAN.md link:**
https://github.com/madisonsimons-lab/pathreview/blob/fix/153-faithfulness-checker-none-crash/PLAN.md

**Walkthrough video (recommended):** N/A (optional, not graded)

**Blockers or open questions:**
None at this time. I plan to update the check() method to handle None values safely and verify the fix with the existing unit test.

## Week 9 — Solution building & PR submission

### Check-in 1 (mid-week)

**Current progress:**
<!-- What have you implemented so far / which PLAN.md sub-tasks are done? Facts you can use:
- Implemented the fix in rag/evaluator/faithfulness_checker.py: changed chunk.get("text", "") to chunk.get("text") or ""
- The existing regression test test_none_context_chunk_text now passes (it failed before the fix)
- Full unit suite: 52 failed / 376 passed (was 53/375 before) -> my change fixed 1 and added 0 new failures -->
_YOUR ANSWER HERE_

**Next steps:**
<!-- What's left? e.g. open a draft PR, get peer/mentor feedback in Slack, fill the PR template, mark ready. -->
_YOUR ANSWER HERE_

**Blockers:**
<!-- Anything slowing you down, or leave blank. -->

---

### Check-in 2 (end of week)

**PR link:** <!-- paste after you open the PR, e.g. https://github.com/ascherj/pathreview/pull/<n> -->

**Branch:** `fix/153-faithfulness-checker-none-crash`

**What you built:**
<!-- 1–3 sentences IN YOUR OWN WORDS: what the fix does + how it works. (You changed the context concatenation so a chunk whose "text" is None is treated as "" instead of crashing " ".join().) -->
_YOUR ANSWER HERE_

**Tests added or updated:**
<!-- Which tests cover this? The regression test tests/unit/test_faithfulness_checker.py::test_none_context_chunk_text (and test_missing_text_key_in_chunk) now pass with the fix — they cover the None-value and missing-key cases. Describe in your own words. -->
_YOUR ANSWER HERE_

**Self-review confirmation:** [ ] make check passes  [ ] make test-unit passes
<!-- NOTE: the repo has documented PRE-EXISTING failures (52 unit-test failures + file-wide lint/format issues) unrelated to #153. Per the assignment, "passes" here means your change introduces NO NEW failures — which is verified: the fix file passes ruff/black/mypy, and the suite went 53->52 failures (your fix removed 1, added 0). Check the boxes once you've re-run both commands yourself. -->

**Draft PR feedback received from:** <!-- name or Slack handle, or "none" -->