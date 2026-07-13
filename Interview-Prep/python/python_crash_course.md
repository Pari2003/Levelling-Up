# 🐍 Deliverable 2: Python Scripting Crash Course + 10 Practice Problems

> [!TIP]
> Eightfold won't ask you to implement a red-black tree. They'll ask you to parse JSON, call an API, process a log file, or transform data. This guide covers exactly that.

---

## Part A: Python Essentials Cheat Sheet

### 1. Data Structures — When to Use What

```python
# LIST — Ordered, mutable, allows duplicates
candidates = ["Alice", "Bob", "Charlie"]
candidates.append("Diana")          # Add to end
candidates.insert(0, "Eve")         # Insert at index
candidates.remove("Bob")            # Remove by value
candidates.pop()                    # Remove last
sorted_list = sorted(candidates)    # Returns new sorted list
candidates.sort()                   # Sorts in place

# DICTIONARY — Key-value pairs, O(1) lookup
candidate = {
    "name": "Alice",
    "skills": ["Python", "SQL"],
    "experience": 3
}
candidate["email"] = "alice@ex.com"  # Add/update key
name = candidate.get("name", "Unknown")  # Safe get with default
del candidate["experience"]         # Delete key

# Iterate dict
for key, value in candidate.items():
    print(f"{key}: {value}")

# SET — Unordered, no duplicates, fast membership check
skills_a = {"Python", "SQL", "AWS"}
skills_b = {"Python", "Java", "Docker"}
common = skills_a & skills_b         # Intersection: {"Python"}
all_skills = skills_a | skills_b     # Union
only_a = skills_a - skills_b         # Difference

# TUPLE — Immutable (can't change after creation), used for fixed data
coordinates = (12.97, 77.59)  # Bangalore lat/long
```

**Interview tip:** If they ask "how would you check if X is in a collection" — use a `set` for O(1) lookup, not a `list` (O(n)).

### 2. JSON — The #1 Skill for This Role

```python
import json

# ─── Parse JSON string → Python dict ───
json_string = '{"name": "Alice", "skills": ["Python", "SQL"]}'
data = json.loads(json_string)       # String → Dict
print(data["name"])                  # "Alice"
print(data["skills"][0])             # "Python"

# ─── Python dict → JSON string ───
output = json.dumps(data, indent=2)  # Dict → Pretty JSON string

# ─── Read JSON file ───
with open("candidates.json", "r") as f:
    candidates = json.load(f)        # File → Dict/List

# ─── Write JSON file ───
with open("output.json", "w") as f:
    json.dump(data, f, indent=2)     # Dict → File

# ─── Navigate nested JSON (VERY common in interviews) ───
api_response = {
    "status": "success",
    "data": {
        "candidates": [
            {"id": 1, "name": "Alice", "skills": ["Python"]},
            {"id": 2, "name": "Bob", "skills": ["Java", "SQL"]}
        ],
        "pagination": {"page": 1, "total": 50}
    }
}

# Access nested values
first_candidate = api_response["data"]["candidates"][0]
total = api_response["data"]["pagination"]["total"]

# Safe nested access (won't crash if key missing)
page = api_response.get("data", {}).get("pagination", {}).get("page", 1)
```

### 3. Making API Calls with `requests`

```python
import requests

# ─── GET Request ───
response = requests.get(
    "https://api.example.com/candidates",
    headers={"Authorization": "Bearer YOUR_TOKEN"},
    params={"status": "active", "page": 1}  # → ?status=active&page=1
)

# Check status
print(response.status_code)  # 200, 404, 500, etc.
print(response.ok)           # True if 200-299

# Parse response
if response.ok:
    data = response.json()   # Parse JSON body → dict
else:
    print(f"Error: {response.status_code} — {response.text}")

# ─── POST Request ───
new_candidate = {"name": "Alice", "email": "alice@example.com"}
response = requests.post(
    "https://api.example.com/candidates",
    json=new_candidate,      # Auto-sets Content-Type: application/json
    headers={"Authorization": "Bearer YOUR_TOKEN"}
)

# ─── PUT Request (Update) ───
response = requests.put(
    "https://api.example.com/candidates/123",
    json={"status": "hired"}
)

# ─── DELETE Request ───
response = requests.delete("https://api.example.com/candidates/123")

# ─── Error Handling for API Calls ───
try:
    response = requests.get("https://api.example.com/data", timeout=10)
    response.raise_for_status()  # Raises HTTPError for 4xx/5xx
    data = response.json()
except requests.exceptions.ConnectionError:
    print("Cannot connect to server")
except requests.exceptions.Timeout:
    print("Request timed out")
except requests.exceptions.HTTPError as e:
    print(f"HTTP Error: {e}")
except json.JSONDecodeError:
    print("Response is not valid JSON")
```

### 4. File I/O — Reading Logs, CSVs, etc.

```python
# ─── Read a text/log file ───
with open("server.log", "r") as f:
    lines = f.readlines()            # List of all lines
    # OR
    for line in f:                   # Memory efficient — line by line
        if "ERROR" in line:
            print(line.strip())

# ─── Read CSV ───
import csv
with open("candidates.csv", "r") as f:
    reader = csv.DictReader(f)       # Each row is a dict
    for row in reader:
        print(row["name"], row["email"])

# ─── Write to file ───
with open("output.txt", "w") as f:
    f.write("Line 1\n")
    f.write("Line 2\n")

# ─── Read entire file as string ───
content = open("data.txt").read()
```

### 5. List/Dict Comprehensions

```python
# List comprehension — filter and transform in one line
numbers = [1, 2, 3, 4, 5, 6]
evens = [n for n in numbers if n % 2 == 0]           # [2, 4, 6]
squared = [n ** 2 for n in numbers]                    # [1, 4, 9, 16, 25, 36]

# Dict comprehension
candidates = [{"name": "Alice", "score": 85}, {"name": "Bob", "score": 92}]
score_map = {c["name"]: c["score"] for c in candidates}  # {"Alice": 85, "Bob": 92}

# Filter with comprehension
high_scorers = [c for c in candidates if c["score"] > 90]
```

### 6. Exception Handling

```python
try:
    result = risky_operation()
except ValueError as e:
    print(f"Bad value: {e}")
except KeyError as e:
    print(f"Missing key: {e}")
except Exception as e:          # Catch-all (use sparingly)
    print(f"Unexpected error: {e}")
finally:
    cleanup()                    # Always runs
```

### 7. String Operations

```python
s = "  Hello, World!  "
s.strip()            # "Hello, World!" — remove whitespace
s.lower()            # "  hello, world!  "
s.split(",")         # ["  Hello", " World!  "]
",".join(["a","b"])  # "a,b"
s.startswith("  He") # True
s.replace(",", ";")  # "  Hello; World!  "

# f-strings (always use these)
name, score = "Alice", 95
print(f"Candidate {name} scored {score}/100")
print(f"Pass: {'Yes' if score >= 70 else 'No'}")
```

### 8. Common Patterns

```python
# Count occurrences
from collections import Counter
statuses = ["Applied", "Rejected", "Applied", "Hired", "Rejected", "Rejected"]
counts = Counter(statuses)  # Counter({"Rejected": 3, "Applied": 2, "Hired": 1})

# defaultdict — auto-initializes missing keys
from collections import defaultdict
dept_employees = defaultdict(list)
for emp in employees:
    dept_employees[emp["department"]].append(emp["name"])

# Sort list of dicts
candidates = [{"name": "Bob", "score": 80}, {"name": "Alice", "score": 95}]
sorted_cands = sorted(candidates, key=lambda c: c["score"], reverse=True)

# Enumerate (get index + value)
for i, candidate in enumerate(candidates):
    print(f"{i+1}. {candidate['name']}")

# Zip (combine two lists)
names = ["Alice", "Bob"]
scores = [95, 80]
for name, score in zip(names, scores):
    print(f"{name}: {score}")
```

---

## Part B: 10 Practice Problems

### Problem 1: Parse API Response
**Task:** Given this API response, extract all candidate names who have "Python" in their skills.

```python
response = {
    "data": {
        "candidates": [
            {"id": 1, "name": "Alice", "skills": ["Python", "SQL", "AWS"]},
            {"id": 2, "name": "Bob", "skills": ["Java", "Spring"]},
            {"id": 3, "name": "Charlie", "skills": ["Python", "FastAPI", "Docker"]},
            {"id": 4, "name": "Diana", "skills": ["JavaScript", "React"]},
        ]
    }
}

# Solution:
python_devs = [
    c["name"]
    for c in response["data"]["candidates"]
    if "Python" in c["skills"]
]
print(python_devs)  # ["Alice", "Charlie"]
```

---

### Problem 2: Parse a Log File for Errors
**Task:** Read a log file and extract all ERROR lines with their timestamps.

```python
import re

def parse_errors(log_file_path):
    errors = []
    with open(log_file_path, "r") as f:
        for line_num, line in enumerate(f, 1):
            if "ERROR" in line:
                # Extract timestamp (assumes format: 2026-07-08 14:30:22 ERROR ...)
                match = re.match(r"(\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2})\s+ERROR\s+(.*)", line.strip())
                if match:
                    errors.append({
                        "line": line_num,
                        "timestamp": match.group(1),
                        "message": match.group(2)
                    })
    return errors

# Example usage:
# errors = parse_errors("integration_sync.log")
# for e in errors:
#     print(f"[Line {e['line']}] {e['timestamp']}: {e['message']}")
```

---

### Problem 3: Transform CSV Data to JSON
**Task:** Read a CSV of candidates and convert it to a JSON file with structured data.

```python
import csv
import json

def csv_to_json(csv_path, json_path):
    candidates = []
    with open(csv_path, "r") as f:
        reader = csv.DictReader(f)
        for row in reader:
            candidates.append({
                "name": row["name"].strip(),
                "email": row["email"].strip().lower(),
                "skills": [s.strip() for s in row["skills"].split(",")],
                "experience_years": int(row["years_experience"])
            })
    
    with open(json_path, "w") as f:
        json.dump({"candidates": candidates}, f, indent=2)
    
    return len(candidates)

# count = csv_to_json("candidates.csv", "candidates.json")
# print(f"Converted {count} candidates")
```

---

### Problem 4: API Integration — Fetch and Filter Data
**Task:** Call a paginated API and collect all results.

```python
import requests

def fetch_all_candidates(base_url, api_key):
    all_candidates = []
    page = 1
    
    while True:
        response = requests.get(
            f"{base_url}/api/v1/candidates",
            headers={"Authorization": f"Bearer {api_key}"},
            params={"page": page, "per_page": 100},
            timeout=30
        )
        
        if not response.ok:
            print(f"Error on page {page}: {response.status_code}")
            break
        
        data = response.json()
        candidates = data.get("candidates", [])
        
        if not candidates:  # No more data
            break
        
        all_candidates.extend(candidates)
        
        # Check if more pages
        if page >= data.get("total_pages", 1):
            break
        
        page += 1
    
    return all_candidates
```

---

### Problem 5: Find Duplicates in Data
**Task:** Given a list of candidate records, find duplicates by email.

```python
from collections import defaultdict

def find_duplicates(candidates):
    email_groups = defaultdict(list)
    
    for candidate in candidates:
        email = candidate["email"].lower().strip()
        email_groups[email].append(candidate)
    
    duplicates = {
        email: records
        for email, records in email_groups.items()
        if len(records) > 1
    }
    
    return duplicates

# Usage:
candidates = [
    {"id": 1, "name": "Alice", "email": "alice@example.com"},
    {"id": 2, "name": "Alice Smith", "email": "Alice@Example.com"},
    {"id": 3, "name": "Bob", "email": "bob@example.com"},
]
dupes = find_duplicates(candidates)
# {"alice@example.com": [{"id": 1, ...}, {"id": 2, ...}]}
```

---

### Problem 6: Data Validation Script
**Task:** Validate a list of candidate records and report errors.

```python
import re

def validate_candidates(candidates):
    errors = []
    email_pattern = re.compile(r'^[\w.-]+@[\w.-]+\.\w+$')
    
    for i, c in enumerate(candidates):
        row_errors = []
        
        # Name validation
        if not c.get("name") or not c["name"].strip():
            row_errors.append("Missing name")
        
        # Email validation
        email = c.get("email", "")
        if not email_pattern.match(email):
            row_errors.append(f"Invalid email: '{email}'")
        
        # Experience validation
        exp = c.get("years_experience")
        if exp is not None and (not isinstance(exp, int) or exp < 0 or exp > 50):
            row_errors.append(f"Invalid experience: {exp}")
        
        if row_errors:
            errors.append({"row": i, "candidate": c.get("name", "UNKNOWN"), "errors": row_errors})
    
    return errors
```

---

### Problem 7: Compare Two API Responses (Data Sync Check)
**Task:** Given data from two systems, find mismatches — exactly what a PSE does when debugging sync issues.

```python
def compare_systems(source_data, target_data, key_field="email"):
    """Compare records between source (e.g., ATS) and target (e.g., Eightfold)."""
    source_map = {record[key_field]: record for record in source_data}
    target_map = {record[key_field]: record for record in target_data}
    
    source_keys = set(source_map.keys())
    target_keys = set(target_map.keys())
    
    report = {
        "only_in_source": list(source_keys - target_keys),   # Not synced yet
        "only_in_target": list(target_keys - source_keys),   # Orphaned records
        "in_both": list(source_keys & target_keys),
        "mismatches": []
    }
    
    # Check field-level mismatches for records in both
    for key in report["in_both"]:
        s, t = source_map[key], target_map[key]
        diffs = {}
        for field in s:
            if field in t and s[field] != t[field]:
                diffs[field] = {"source": s[field], "target": t[field]}
        if diffs:
            report["mismatches"].append({"key": key, "differences": diffs})
    
    return report
```

---

### Problem 8: Retry with Exponential Backoff
**Task:** Implement a function that retries failed API calls with exponential backoff.

```python
import time
import requests

def api_call_with_retry(url, max_retries=3, initial_delay=1):
    """Call API with exponential backoff on failure."""
    for attempt in range(max_retries):
        try:
            response = requests.get(url, timeout=10)
            response.raise_for_status()
            return response.json()
        except (requests.exceptions.RequestException) as e:
            if attempt == max_retries - 1:
                raise  # Final attempt — let it crash
            
            delay = initial_delay * (2 ** attempt)  # 1s, 2s, 4s
            print(f"Attempt {attempt + 1} failed: {e}. Retrying in {delay}s...")
            time.sleep(delay)
```

---

### Problem 9: Aggregate Interview Scores
**Task:** Process interview data and generate department statistics.

```python
from collections import defaultdict
import statistics

def analyze_interviews(interviews):
    """
    Input: List of {"candidate": str, "department": str, "score": int, "stage": str}
    Output: Per-department statistics
    """
    dept_scores = defaultdict(list)
    dept_stages = defaultdict(lambda: defaultdict(int))
    
    for interview in interviews:
        dept = interview["department"]
        dept_scores[dept].append(interview["score"])
        dept_stages[dept][interview["stage"]] += 1
    
    report = {}
    for dept, scores in dept_scores.items():
        report[dept] = {
            "total_interviews": len(scores),
            "avg_score": round(statistics.mean(scores), 2),
            "median_score": statistics.median(scores),
            "min_score": min(scores),
            "max_score": max(scores),
            "stage_breakdown": dict(dept_stages[dept])
        }
    
    return report
```

---

### Problem 10: Debug a Broken Script
**Task:** This script has 5 bugs. Find and fix them all.

```python
# BUGGY VERSION (what they might show you):
import json

def process_candidates(file_path):
    with open(file_path) as f:
        data = json.loads(f)                    # BUG 1: should be json.load(f)
    
    results = []
    for candidate in data["candidates"]:
        name = candidate["name"]
        skills = candidate["skills"].split(",")  # BUG 2: skills might already be a list
        score = candidate["score"]
        
        if score > 80:                           # BUG 3: score might be string "85"
            results.append({
                "name": name,
                "skill_count": len(skills),
                "grade": "A" if score > 90 else "B"
            })
    
    # Write results
    with open("output.json") as f:              # BUG 4: need "w" mode for writing
        json.dump(results, f)
    
    return len(result)                          # BUG 5: typo — should be 'results'

# FIXED VERSION:
def process_candidates_fixed(file_path):
    with open(file_path, "r") as f:
        data = json.load(f)                     # FIX 1: json.load() for file objects
    
    results = []
    for candidate in data["candidates"]:
        name = candidate["name"]
        skills = candidate["skills"]
        if isinstance(skills, str):              # FIX 2: handle both str and list
            skills = skills.split(",")
        score = int(candidate["score"])          # FIX 3: cast to int
        
        if score > 80:
            results.append({
                "name": name,
                "skill_count": len(skills),
                "grade": "A" if score > 90 else "B"
            })
    
    with open("output.json", "w") as f:         # FIX 4: open in write mode
        json.dump(results, f, indent=2)
    
    return len(results)                         # FIX 5: correct variable name
```

---

## Part C: Quick Concept Questions

| Question | Answer |
|:---------|:-------|
| "Difference between list and tuple?" | List is mutable (can change), tuple is immutable (can't). Tuples are hashable so can be dict keys. |
| "What's a dictionary?" | Key-value store with O(1) average lookup. Keys must be hashable (immutable). |
| "What does `if __name__ == '__main__':` do?" | Runs code only when the file is executed directly, not when imported as a module. |
| "Difference between `json.load()` and `json.loads()`?" | `load()` reads from a file object. `loads()` reads from a string. |
| "What's a list comprehension?" | Compact syntax to create a list: `[x*2 for x in range(5)]` → `[0, 2, 4, 6, 8]` |
| "How do you handle missing dict keys?" | Use `.get(key, default)` instead of `[key]` to avoid KeyError. |
| "What's `*args` and `**kwargs`?" | `*args` = variable positional args (tuple). `**kwargs` = variable keyword args (dict). |
| "What's a virtual environment?" | Isolated Python environment with its own packages, preventing version conflicts between projects. |
