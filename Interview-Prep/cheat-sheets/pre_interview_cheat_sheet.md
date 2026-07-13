# Pre-Interview Cheat Sheet (Review 30 Min Before)

---

## ️ "Tell Me About Yourself" (60 sec)
> "I'm Maitraiyee. B.Tech CS (AI/ML), Jain University, 8.7 GPA. Published IEEE paper on fair ranking for skill-based job matching — Sentence-BERT + fairness post-processing to reduce hiring bias (40.7% disparity reduction). Built agentic RAG pipeline with 3-layer hallucination detection. 3 internships: Veranda (retrieval system), AICTE Microsoft Elevate (BI), Futurense (ML). Excited about [COMPANY] because [SPECIFIC REASON]."

---

## Self-Ratings
| Skill | Say | One-liner |
|:------|:----|:---------|
| Python | **4** | Built backends, ML pipelines, async systems, research code |
| SQL | **3** | JOINs, GROUP BY, window functions. Growing in optimization |
| APIs | **3** | Built REST APIs with FastAPI. Growing in enterprise integration |

---

## STAR Stories (Pick 1 per question)

| Story | Use For | Punch Line |
|:------|:--------|:-----------|
| **IEEE Paper** | Communication, collaboration, trade-offs | "40.7% disparity reduction, 109,780 data points, ablation study resolved team disagreement" |
| **RAG Hallucination Bug** | Debugging, problem-solving | "Cosine similarity scored 0.87 for factually wrong answer → added NLI + keyword → 97% catch rate" |
| **Veranda Internship** | Customer work, feedback, real impact | "Built retrieval system, iterated on user feedback, saved 5-10 hrs/week" |

---

## ️ Project Pitches (30 sec each)

**RAG:** "Q&A on PDFs. 3-way hybrid retrieval (vector + keyword + graph). Self-correction: generates → verifies each claim with 3-layer hallucination detection → fixes."

**Eval Dashboard:** "A/B testing for LLM prompts. 5-layer scoring + Welch's t-test for statistical significance."

**VS Code Extension:** "Autonomous coding agent. 6 agents: Analyze → Plan → Test → Code → Review → Git. Self-healing on errors."

**Dev Automation:** "CLI: natural language → Docker environments. Live sandbox validation — boots containers, reads crash logs, auto-fixes."

---

## API Status Codes
```
200=OK 201=Created 400=Bad Request 401=Auth Failed
403=Forbidden 404=Not Found 429=Rate Limited
500=Server Error 503=Service Down
```

## SQL Quick-Fire
```sql
SELECT dept, COUNT(*) FROM emp GROUP BY dept HAVING COUNT(*) > 5;
ROW_NUMBER() OVER (PARTITION BY dept ORDER BY salary DESC)
WITH cte AS (SELECT ...) SELECT * FROM cte WHERE ...;
-- WHERE = before GROUP BY. HAVING = after GROUP BY.
-- NULL: use IS NULL, never = NULL. COALESCE(val, default).
```

---

## Questions to Ask Them
1. "What does a typical first 90 days look like?"
2. "What's the team's biggest challenge right now?"
3. "What does career growth look like for this role?"

---

## Do / Don't
 Say "I" not "we" | Never rate yourself 5
 Use specific numbers | Never say "I don't know" — say "I'd figure it out by..."
 Pause and think | Never ramble — answer then stop
 Connect to the role | Never badmouth past experiences
