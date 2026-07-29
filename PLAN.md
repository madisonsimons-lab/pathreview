## Solution plan

**Issue:** Faithfulness checker crashes when a context chunk has `text: None` — https://github.com/ascherj/pathreview/issues/153

### Understand
The issue occurs because chunk.get("text", "") returns None when the text key exists but its value is None; the default value is only used when the key is missing. The expected behavior is for check() to treat None or missing text as an empty string and continue evaluating the context. Instead, the method passes None to " ".join(...), causing a TypeError and preventing the faithfulness score from being returned.

### Map
Files / functions involved (verified in the codebase):
- `rag/evaluator/faithfulness_checker.py` — `FaithfulnessChecker.check()`, context concatenation at lines ~34–36: `" ".join([chunk.get("text", "") for chunk in context_chunks])`. **This is where the crash occurs.**
- `tests/unit/test_faithfulness_checker.py` — already contains the failing regression test `test_none_context_chunk_text` (and a related `test_missing_text_key_in_chunk` that currently passes).
- Caller: `rag/evaluator/eval_suite.py:43` — `EvalSuite.evaluate()` calls `check(feedback, chunks)`, so the crash propagates up into review evaluation.

Files I expect to touch:
- `rag/evaluator/faithfulness_checker.py` — the fix.
- `tests/unit/test_faithfulness_checker.py` — only if I add/extend tests (the core repro test already exists).

Related code with the SAME `chunk.get("text", "")` pattern (decide if in scope — see Risks):
- `rag/evaluator/relevance_scorer.py:32`
- `rag/generator/review_generator.py:157`

### Plan
1. Update the check() method to replace None text values with an empty string before joining the context.
2. Verify the fix by running the failing unit test and then the full test_faithfulness_checker.py file to ensure nothing else broke.
3. Keep the scope focused on the faithfulness checker and confirm it correctly handles None, missing, and valid text values without crashing.

### Inputs & outputs
- **Input:** `feedback: str` and `context_chunks: list[dict]`, where each chunk's `"text"` value may be a normal string, missing entirely, or `None`.
- **Output:** a `float` faithfulness score in the range `0.0–1.0`.
- **Change:** after the fix, a chunk with `text: None` (or a missing `text` key) contributes an empty string to the concatenated context instead of raising a `TypeError`, so `check()` always returns a score.

### Risks & unknowns
The main risk is that other files use the same get("text", "") pattern and may have the same issue. Another unknown is whether converting None to an empty string could hide an upstream data problem, but for this issue the goal is to prevent the checker from crashing.

### Edge cases
The fix should correctly handle a context chunk with {"text": None}, a missing text key, an empty string, multiple chunks containing both None and valid text, and a context where all chunks contain None without causing an error.
