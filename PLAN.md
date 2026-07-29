## Solution plan

**Issue:** Faithfulness checker crashes when a context chunk has `text: None` — https://github.com/ascherj/pathreview/issues/153

### Understand
<!-- Root cause + expected vs. actual behavior, IN YOUR OWN WORDS (you can adapt your Week 7 summary). Prompts:
- Root cause: why does dict.get("text", "") return None here instead of ""? (default only applies when the key is ABSENT)
- Expected: what should check() do with a None/missing text?
- Actual: what happens today? (TypeError at the " ".join(...) call) -->
_YOUR ANSWER HERE_

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
<!-- 3–5 concrete sub-tasks IN YOUR OWN WORDS. Prompts:
1. What is the minimal code change? (coerce a None/missing text to "" before the join, e.g. `chunk.get("text") or ""`)
2. How will you verify the fix? (run the failing test -> green; run the whole test file to check no regressions)
3. Will you add any tests beyond the existing one? (e.g. multiple chunks where some are None)
4. Will you address the sibling pattern in relevance_scorer.py / review_generator.py, or keep scope to faithfulness only? -->
1. _YOUR ANSWER HERE_
2. _YOUR ANSWER HERE_
3. _YOUR ANSWER HERE_

### Inputs & outputs
- **Input:** `feedback: str` and `context_chunks: list[dict]`, where each chunk's `"text"` value may be a normal string, missing entirely, or `None`.
- **Output:** a `float` faithfulness score in the range `0.0–1.0`.
- **Change:** after the fix, a chunk with `text: None` (or a missing `text` key) contributes an empty string to the concatenated context instead of raising a `TypeError`, so `check()` always returns a score.

### Risks & unknowns
<!-- Specific risks tied to real files / investigation paths, IN YOUR OWN WORDS. Prompts:
- Sibling bug: relevance_scorer.py:32 and review_generator.py:157 use the same get("text","") pattern and would also break on None. Is fixing only faithfulness enough, or inconsistent?
- Upstream source of None: producers vector_store.py:106, hybrid.py:85, readme_parser.py:61 build {"text": ...}. Could coercing None -> "" mask a real upstream data bug worth reporting?
- Does an empty context change scoring in a surprising way (e.g. _is_supported returns False for every claim -> score 0.0)? Is that acceptable? -->
_YOUR ANSWER HERE_

### Edge cases
<!-- Concrete inputs/states the fix should handle gracefully. Candidates to consider (keep the ones you'll actually cover):
- chunk = {"text": None}            (the reported bug)
- chunk = {}                        (missing "text" key — already handled, keep it working)
- chunk = {"text": ""}              (empty string)
- multiple chunks, some None + some valid
- all chunks None -> empty context blob
- non-string text (e.g. an int) -> decide whether that's in scope -->
_YOUR ANSWER HERE_
