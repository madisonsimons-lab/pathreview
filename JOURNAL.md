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


**Reproduction summary:**
I reproduced the issue by running FaithfulnessChecker().check("Knows Python.", [{"text": None}]), which caused the program to throw a TypeError. I also ran the failing unit test test_none_context_chunk_text, which confirmed the same error because the checker attempted to join a None value with strings.

**PLAN.md link:**


**Walkthrough video (recommended):** N/A (optional, not graded)

**Blockers or open questions:**
None at this time. I plan to update the check() method to handle None values safely and verify the fix with the existing unit test.