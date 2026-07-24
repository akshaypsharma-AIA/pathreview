## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/149

**Issue title:** Structural chunker silently drops documents that contain no headings

**Tier:** [x] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
The RAG ingestion pipeline chunks documents by splitting on markdown headings. StructuralChunker's section-extraction logic only starts collecting content once it has already seen a heading, so a document with zero headings never triggers collection and returns no sections at all. Since chunk() has nothing to loop over in that case, it returns an empty list instead of at least one chunk. The pipeline never flags a zero-chunk result as an error, so a real candidate's README that just doesn't use markdown headings would silently contribute nothing to their review. The fix reuses the file's existing SemanticChunker fallback (already used for oversized sections) so headingless documents get chunked too, instead of dropped.

**Branch name:** fix/149-structural-chunker-no-headings

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [ ] Issue added to cohort ledger
