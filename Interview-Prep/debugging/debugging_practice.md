# 🔧 Debugging Practice — 10 Mock Problems

> You froze at the Eightfold config debugging question because you'd never practiced this format. These 10 problems train you on the exact pattern: **read client complaint → read code → find and fix the issue → explain why.**

---

## How Debugging Questions Work in Interviews

```
┌──────────────────────┐     ┌──────────────────────────┐
│   CLIENT COMPLAINT   │     │   CODE / CONFIG          │
│   (Left side)        │     │   (Right side)           │
│                      │     │                          │
│  "Something is       │     │  def process():          │
│   broken because..." │     │      config = {...}      │
│                      │     │      ...                 │
└──────────────────────┘     └──────────────────────────┘

Your job:
1. READ the complaint carefully — what EXACTLY is wrong?
2. READ the code — understand what it does
3. FIND the mismatch between what the client expects and what the code does
4. FIX the code
5. EXPLAIN your reasoning
```

**The #1 mistake (what you did):** Jumping into the code without fully understanding the complaint. Always start with: "What does the client want?" THEN look at the code.

---

## Problem 1: Hiring Threshold (Similar to Your Eightfold Question)

**Client Complaint:**
> "Our hiring pipeline is rejecting too many candidates. We need at least 20% of interviewed candidates to be hired. Currently only ~8% are getting through."

**Code:**
```python
def evaluate_candidate(candidate):
    interview_score = candidate.get("interview_score", 0)   # 0-100
    skill_score = candidate.get("skill_score", 0)           # 0-100
    
    config = {
        "interview_weight": 1.5,
        "skill_weight": 2.5,
        "passing_threshold": 160
    }
    
    weighted_score = (interview_score * config["interview_weight"] + 
                      skill_score * config["skill_weight"])
    
    return weighted_score >= config["passing_threshold"]

# Sample data showing the problem:
candidates = [
    {"name": "Alice", "interview_score": 85, "skill_score": 30},   # 85*1.5 + 30*2.5 = 127.5+75 = 202.5 ✅
    {"name": "Bob",   "interview_score": 90, "skill_score": 10},   # 90*1.5 + 10*2.5 = 135+25  = 160   ✅ (barely)
    {"name": "Carol", "interview_score": 75, "skill_score": 20},   # 75*1.5 + 20*2.5 = 112.5+50 = 162.5 ✅
    {"name": "Dave",  "interview_score": 95, "skill_score": 0},    # 95*1.5 + 0*2.5  = 142.5+0  = 142.5 ❌
    {"name": "Eve",   "interview_score": 60, "skill_score": 15},   # 60*1.5 + 15*2.5 = 90+37.5  = 127.5 ❌
]
```

**Analysis:**
- The `skill_weight` (2.5) is much higher than `interview_weight` (1.5)
- Candidates with great interviews but low skill scores (like Dave: 95 interview, 0 skills) are failing
- The threshold of 160 requires either high skills OR both decent scores

**Fix Options:**
```python
# Option A: Lower the threshold
config["passing_threshold"] = 130  # More candidates pass

# Option B: Increase interview weight (what you did — this is valid!)
config["interview_weight"] = 2.0   # Interviews matter more

# Option C: Both — rebalance weights AND lower threshold
config = {
    "interview_weight": 2.0,
    "skill_weight": 2.0,
    "passing_threshold": 140
}
```

**Best explanation:** *"The issue is that skill_weight (2.5) is disproportionately higher than interview_weight (1.5), which means candidates who interview well but have fewer listed skills get penalized. Since the client wants to increase the hiring rate from 8% to 20%, I'd either lower the threshold or rebalance the weights to value interviews more equally. I'd recommend rebalancing weights to 2.0 each and lowering threshold to 140, so both factors contribute equally."*

---

## Problem 2: API Data Sync Missing Records

**Client Complaint:**
> "We sync candidate data from our ATS every night, but 30% of new candidates aren't appearing in our system."

**Code:**
```python
def sync_candidates(api_response):
    synced = []
    for candidate in api_response["data"]["candidates"]:
        if (candidate.get("email") and 
            candidate.get("name") and
            candidate.get("experience_years") is not None and
            candidate.get("skills") and len(candidate["skills"]) > 0):
            
            synced.append({
                "email": candidate["email"].lower().strip(),
                "name": candidate["name"].strip(),
                "experience": candidate["experience_years"],
                "skills": candidate["skills"]
            })
    return synced

# Sample data:
api_response = {
    "data": {
        "candidates": [
            {"name": "Alice", "email": "alice@ex.com", "experience_years": 3, "skills": ["Python"]},  # ✅ Syncs
            {"name": "Bob", "email": "bob@ex.com", "experience_years": 0, "skills": ["Java"]},        # ✅ Syncs
            {"name": "Carol", "email": "carol@ex.com", "experience_years": 2, "skills": []},           # ❌ Empty skills!
            {"name": "Dave", "email": None, "experience_years": 5, "skills": ["AWS"]},                 # ❌ No email!
            {"name": "", "email": "eve@ex.com", "experience_years": 1, "skills": ["SQL"]},             # ❌ Empty name!
            {"name": "Frank", "email": "frank@ex.com", "experience_years": None, "skills": ["React"]}, # ❌ Null experience!
        ]
    }
}
```

**Bug:** The validation is too strict. Many valid candidates are being rejected because:
1. `skills` list is empty (new candidates haven't listed skills yet)
2. `experience_years` is None (optional field in ATS)
3. `name` is empty string (passes truthiness check? No — empty string is falsy)

**Fix:**
```python
def sync_candidates_fixed(api_response):
    synced = []
    skipped = []
    
    for candidate in api_response["data"]["candidates"]:
        email = candidate.get("email", "")
        name = candidate.get("name", "")
        
        # Only email is truly required — it's the unique identifier
        if not email or not email.strip():
            skipped.append({"candidate": candidate, "reason": "missing email"})
            continue
        
        synced.append({
            "email": email.lower().strip(),
            "name": name.strip() if name else "Unknown",
            "experience": candidate.get("experience_years", 0) or 0,  # Default to 0
            "skills": candidate.get("skills", [])  # Empty list is OK
        })
    
    print(f"Synced: {len(synced)}, Skipped: {len(skipped)}")
    for s in skipped:
        print(f"  Skipped: {s['reason']}")
    
    return synced
```

**Explanation:** *"The sync function was silently dropping candidates who had empty skills lists, null experience, or empty names. But these are legitimate candidates — a fresher might not have listed skills yet. The fix: make only email required (it's the unique key), default everything else to sensible values, and log what's skipped so we can debug."*

---

## Problem 3: Pagination Bug

**Client Complaint:**
> "When I fetch all job listings, I only get the first 100 even though we have 500+ jobs."

**Code:**
```python
import requests

def get_all_jobs(api_url, api_key):
    response = requests.get(
        f"{api_url}/api/v1/jobs",
        headers={"Authorization": f"Bearer {api_key}"},
        params={"page": 1, "per_page": 100}
    )
    
    if response.ok:
        return response.json()["data"]["jobs"]
    return []
```

**Bug:** The function only fetches page 1. There's no pagination loop.

**Fix:**
```python
def get_all_jobs_fixed(api_url, api_key):
    all_jobs = []
    page = 1
    
    while True:
        response = requests.get(
            f"{api_url}/api/v1/jobs",
            headers={"Authorization": f"Bearer {api_key}"},
            params={"page": page, "per_page": 100}
        )
        
        if not response.ok:
            print(f"Error on page {page}: {response.status_code}")
            break
        
        data = response.json()["data"]
        jobs = data.get("jobs", [])
        
        if not jobs:
            break
        
        all_jobs.extend(jobs)
        
        if page >= data.get("total_pages", 1):
            break
        
        page += 1
    
    return all_jobs
```

---

## Problem 4: Duplicate Records

**Client Complaint:**
> "Same candidate appearing multiple times in search results."

**Code:**
```python
def add_candidate(candidates_list, new_candidate):
    candidates_list.append(new_candidate)
    return candidates_list
```

**Bug:** No deduplication check before adding.

**Fix:**
```python
def add_candidate_fixed(candidates_list, new_candidate):
    # Check for existing candidate by email (unique identifier)
    for existing in candidates_list:
        if existing["email"].lower() == new_candidate["email"].lower():
            # Update existing record instead of adding duplicate
            existing.update(new_candidate)
            return candidates_list
    
    candidates_list.append(new_candidate)
    return candidates_list
```

---

## Problem 5: Date Format Mismatch

**Client Complaint:**
> "Candidate hire dates are showing wrong dates. Some are showing as month/day swapped."

**Code:**
```python
from datetime import datetime

def parse_hire_date(date_string):
    return datetime.strptime(date_string, "%m/%d/%Y")

# Problem data:
dates = [
    "07/13/2026",   # July 13 — US format ✅
    "13/07/2026",   # July 13 — European format ❌ CRASHES (month 13 doesn't exist)
    "2026-07-13",   # July 13 — ISO format ❌ CRASHES
]
```

**Bug:** Hardcoded US date format. Different source systems use different formats.

**Fix:**
```python
from datetime import datetime
from dateutil import parser as dateutil_parser

def parse_hire_date_fixed(date_string):
    """Try multiple date formats, fall back to smart parsing."""
    formats = [
        "%Y-%m-%d",      # ISO: 2026-07-13
        "%m/%d/%Y",      # US: 07/13/2026
        "%d/%m/%Y",      # EU: 13/07/2026
        "%Y/%m/%d",      # Alt: 2026/07/13
        "%d-%m-%Y",      # EU dash: 13-07-2026
    ]
    
    for fmt in formats:
        try:
            return datetime.strptime(date_string, fmt)
        except ValueError:
            continue
    
    # Fallback to intelligent parser
    try:
        return dateutil_parser.parse(date_string)
    except:
        raise ValueError(f"Cannot parse date: {date_string}")
```

---

## Problem 6: Rate Limiting Not Handled

**Client Complaint:**
> "Integration keeps failing after running for a few minutes. Error: 429."

**Code:**
```python
def bulk_update_candidates(candidates, api_url):
    results = []
    for c in candidates:
        response = requests.post(f"{api_url}/candidates", json=c)
        results.append(response.status_code)
    return results
```

**Bug:** No rate limiting handling. API returns 429 (Too Many Requests) and the code just records it as failure.

**Fix:**
```python
import time

def bulk_update_candidates_fixed(candidates, api_url):
    results = []
    for i, c in enumerate(candidates):
        response = requests.post(f"{api_url}/candidates", json=c)
        
        if response.status_code == 429:
            # Rate limited — wait and retry
            retry_after = int(response.headers.get("Retry-After", 5))
            print(f"Rate limited at candidate {i}. Waiting {retry_after}s...")
            time.sleep(retry_after)
            response = requests.post(f"{api_url}/candidates", json=c)  # Retry
        
        results.append({"candidate": c["email"], "status": response.status_code})
    
    return results
```

---

## Problem 7: Environment Variable Missing

**Client Complaint:**
> "Application crashes on startup with KeyError."

**Code:**
```python
import os

DB_HOST = os.environ["DATABASE_HOST"]
DB_PORT = os.environ["DATABASE_PORT"]
API_KEY = os.environ["API_KEY"]
```

**Bug:** `os.environ["KEY"]` crashes with `KeyError` if the variable isn't set.

**Fix:**
```python
import os
import sys

def get_required_env(key, default=None):
    value = os.environ.get(key, default)
    if value is None:
        print(f"ERROR: Required environment variable '{key}' is not set.")
        sys.exit(1)
    return value

DB_HOST = get_required_env("DATABASE_HOST", "localhost")
DB_PORT = get_required_env("DATABASE_PORT", "5432")
API_KEY = get_required_env("API_KEY")  # No default — truly required
```

---

## Problem 8: SQL Query Returning Wrong Count

**Client Complaint:**
> "Dashboard shows 500 total applications but the jobs page shows 600. Numbers don't match."

**Code:**
```sql
-- Dashboard query
SELECT COUNT(*) FROM applications;

-- Jobs page query
SELECT j.title, COUNT(a.id) as app_count
FROM jobs j
LEFT JOIN applications a ON j.id = a.job_id
GROUP BY j.title;
-- Then sums the app_count column in frontend
```

**Bug:** The LEFT JOIN includes jobs with zero applications (NULL a.id), and `COUNT(a.id)` correctly returns 0 for those. But the total is still 500. The real issue: some applications reference `job_id` values that don't exist in the `jobs` table (orphaned records).

**Fix:**
```sql
-- Find orphaned applications
SELECT a.id, a.job_id 
FROM applications a
LEFT JOIN jobs j ON a.job_id = j.id
WHERE j.id IS NULL;

-- The 100 missing applications have job_ids that don't exist in jobs table
-- Fix: use INNER JOIN on dashboard or clean up orphaned records
```

---

## Problem 9: JSON Parsing Error

**Client Complaint:**
> "Integration fails with 'JSONDecodeError' for some candidates."

**Code:**
```python
def process_webhook(request_body):
    data = json.loads(request_body)
    name = data["candidate"]["name"]
    return name
```

**Bug:** Multiple issues: (1) request_body might not be valid JSON, (2) keys might be missing.

**Fix:**
```python
def process_webhook_fixed(request_body):
    try:
        data = json.loads(request_body)
    except json.JSONDecodeError as e:
        return {"error": f"Invalid JSON: {e}", "raw_body": request_body[:200]}
    
    candidate = data.get("candidate")
    if not candidate:
        return {"error": "Missing 'candidate' field", "received_keys": list(data.keys())}
    
    name = candidate.get("name", "Unknown")
    return {"name": name, "status": "ok"}
```

---

## Problem 10: Off-by-One in Batch Processing

**Client Complaint:**
> "When we import 1000 candidates, only 999 show up."

**Code:**
```python
def batch_import(candidates, batch_size=100):
    total = len(candidates)
    for i in range(0, total // batch_size):
        batch = candidates[i * batch_size : (i + 1) * batch_size]
        process_batch(batch)
```

**Bug:** `total // batch_size` uses integer division. 1000 // 100 = 10, which is fine. But 1050 // 100 = 10, missing the last 50.

**Fix:**
```python
import math

def batch_import_fixed(candidates, batch_size=100):
    total = len(candidates)
    num_batches = math.ceil(total / batch_size)  # Round UP
    for i in range(num_batches):
        batch = candidates[i * batch_size : (i + 1) * batch_size]
        process_batch(batch)
    print(f"Processed {total} candidates in {num_batches} batches")
```

---

## Debugging Checklist (Use This During Any Debug Question)

1. **Read the complaint first.** What EXACTLY is the expected behavior vs actual behavior?
2. **Identify the type of bug:**
   - Data validation too strict/loose?
   - Pagination missing?
   - Error handling missing?
   - Off-by-one?
   - Format mismatch?
   - Missing retry/backoff?
3. **Trace the data flow.** Follow a specific input through the code.
4. **Find the mismatch.** Where does the code's behavior diverge from the expected behavior?
5. **Fix + explain.** Don't just fix it — say WHY it was wrong and WHY your fix works.
