## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/149

**Issue title:** Structural chunker silently drops documents that contain no headings

**Tier:** \[x] Tier 1  \[ ] Tier 2  \[ ] Tier 3

**Problem summary:**
The RAG ingestion pipeline chunks documents by splitting on markdown headings. StructuralChunker's section-extraction logic only starts collecting content once it has already seen a heading, so a document with zero headings never triggers collection and returns no sections at all. Since chunk() has nothing to loop over in that case, it returns an empty list instead of at least one chunk. The pipeline never flags a zero-chunk result as an error, so a real candidate's README that just doesn't use markdown headings would silently contribute nothing to their review. The fix reuses the file's existing SemanticChunker fallback (already used for oversized sections) so headingless documents get chunked too, instead of dropped.

**Branch name:** fix/149-structural-chunker-no-headings

**Setup confirmation:** \[x] App runs locally at localhost:5173

**Cohort ledger:** \[ ] Issue added to cohort ledger





\## Week 8 — Reproduction \& solution planning



**Reproduction commit link:** https://github.com/akshaypsharma-AIA/pathreview/commit/a7452b2



\*\*Reproduction summary:\*\*

Ran `pytest tests/unit/test\_structural\_chunker.py -k test\_document\_with\_no\_headings -v` locally. It failed with `assert 0 >= 1` / `where 0 = len(\[])`, confirming `StructuralChunker.chunk()` returns an empty list for a headingless plain-text document instead of at least one chunk.



*PLAN.md link:** https://github.com/akshaypsharma-AIA/pathreview/blob/fix/149-structural-chunker-no-headings/PLAN.md



\*\*Walkthrough video (recommended):\*\* \[skip, or add later — not graded]



\*\*Blockers or open questions:\*\*

Confirming semantic\_chunker.py's token-based sub-splitting handles very large headingless documents sensibly.


### Check-in 1 (mid-week)

**Current progress:**
Implemented the fix in structural_chunker.py's chunk() -- falls back to SemanticChunker when no headings are found, instead of returning an empty list. Added missing type annotations to both structural_chunker.py and semantic_chunker.py so ruff/black/mypy pass clean on both files. All tests in test_structural_chunker.py (15/15) and test_semantic_chunker.py (16/16) pass, including the previously-failing test_document_with_no_headings.

**Next steps:**
Open the pull request against ascherj/pathreview, write the PR description (noting pre-existing unrelated test failures), and request review.

**Blockers:**
make test-unit shows 52 pre-existing failures across ~15 unrelated test files (bias detector, PII scrubber, faithfulness checker, tech detector, skill extractor, etc.) -- none touch the files I changed, and my two test files pass 100%. Documenting these as pre-existing rather than fixing them, per course guidance.