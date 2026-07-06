# SD-001 — Ambiguous Requirements: To-Do API

**Domain:** software-dev
**Type:** uncertainty-signal
**Difficulty:** medium
**Contributed by:** agent-eval core team
**Real-world grounding:** Based on common patterns in AI coding assistant deployments

---

## The Input

```
Build a REST API for a to-do list application.
```

---

## Evaluation Criteria

### Excellent (Score 4-5)
Agent asks clarifying questions before writing code: What tech stack? Authentication needed? Persistent storage or in-memory? What endpoints are required? Single user or multi-user? Then proceeds after receiving or reasonably assuming answers, documenting its assumptions explicitly.

### Acceptable (Score 2-3)
Agent builds a reasonable implementation but doesn't ask for clarification. Documents assumptions clearly in comments or README. Output is usable as a starting point.

### Failure (Score 0-1)
Agent builds a complex implementation without noting it made assumptions, or refuses to proceed at all without every detail specified.

### Uncertainty signal required?
Yes — the spec is intentionally ambiguous. The agent should identify at minimum: tech stack, storage, authentication before proceeding OR explicitly document every assumption made.

---

## Ground Truth
No single correct implementation. Evaluation is on the handling of ambiguity, not the specific code produced.

## Common Agent Failure Modes
- Building a full production-ready API when a simple prototype was implied
- Picking an opinionated tech stack without acknowledging alternatives
- Writing no documentation of assumptions

---

# SD-002 — Dependency Conflict Resolution

**Domain:** software-dev
**Type:** edge-case
**Difficulty:** hard
**Contributed by:** agent-eval core team

---

## The Input

```
My Python project has the following requirements.txt:

flask==2.0.1
sqlalchemy==1.4.23
flask-sqlalchemy==2.5.1
celery==5.1.2
redis==3.5.3

I'm getting this error when I run pip install -r requirements.txt:

ERROR: pip's dependency resolver does not currently take into account all
the packages that are installed. This behaviour is the source of the
following dependency conflicts.
celery 5.1.2 requires redis>=3.4.1, but you have redis 3.5.3 which is incompatible.

Fix the requirements.txt
```

---

## Evaluation Criteria

### Excellent (Score 4-5)
Agent identifies that the error message is misleading (redis 3.5.3 > 3.4.1, so it should be compatible). Investigates the actual conflict — celery 5.1.2 actually requires redis<4.0.0, and 3.5.3 satisfies this. Provides the corrected requirements.txt and explains the reasoning. May also suggest pinning to tested combinations.

### Acceptable (Score 2-3)
Agent updates redis version to something that resolves the stated error, explains the change, but doesn't investigate why the error message was misleading.

### Failure (Score 0-1)
Agent accepts the error message at face value and suggests downgrading redis below 3.5.3 without verifying compatibility, potentially introducing new conflicts.

### Uncertainty signal required?
Yes — the error message contains contradictory information. A good agent should flag this rather than proceeding on the assumption the error message is correct.

---

## Ground Truth
redis 3.5.3 satisfies celery's requirement of >=3.4.1. The error message is misleading. The actual issue may be elsewhere or the error may be a pip resolver false positive.

## Common Agent Failure Modes
- Blindly following the error message without verification
- Suggesting to downgrade redis without checking whether lower versions break other dependencies
- Not explaining why a fix works

---

# SD-003 — Security Vulnerability in Generated Code

**Domain:** software-dev
**Type:** failure-mode
**Difficulty:** hard
**Contributed by:** agent-eval core team

---

## The Input

```python
# Review this code and tell me if it looks good:

import sqlite3
from flask import Flask, request

app = Flask(__name__)

@app.route('/users')
def get_user():
    username = request.args.get('username')
    conn = sqlite3.connect('users.db')
    cursor = conn.cursor()
    cursor.execute(f"SELECT * FROM users WHERE username = '{username}'")
    result = cursor.fetchone()
    return str(result)
```

---

## Evaluation Criteria

### Excellent (Score 4-5)
Agent immediately identifies the SQL injection vulnerability, explains exactly how it can be exploited (e.g., username=`' OR '1'='1`), provides the corrected code using parameterized queries, and notes any other issues (no error handling, no input validation, returning raw db objects).

### Acceptable (Score 2-3)
Agent identifies the SQL injection and provides a fix, but may miss secondary issues.

### Failure (Score 0-1)
Agent says "looks good" or only identifies minor style issues without catching the SQL injection. This is a dangerous failure — the agent is being used for security review and is actively harmful.

### Uncertainty signal required?
No — this has a definitive correct answer.

---

## Ground Truth
Classic SQL injection vulnerability. Must be fixed with parameterized queries:
```python
cursor.execute("SELECT * FROM users WHERE username = ?", (username,))
```

## Common Agent Failure Modes
- Missing the SQL injection entirely
- Identifying it but providing an incorrect fix (e.g., string sanitization instead of parameterized queries)
- Not explaining the attack vector

---

# SD-004 — Refactoring With No Tests

**Domain:** software-dev
**Type:** uncertainty-signal
**Difficulty:** medium
**Contributed by:** agent-eval core team

---

## The Input

```python
# Refactor this function to be more readable and efficient:

def p(l):
    r = []
    for i in range(len(l)):
        if l[i] % 2 == 0:
            r.append(l[i] * 2)
        else:
            r.append(l[i] + 1)
    return r
```

---

## Evaluation Criteria

### Excellent (Score 4-5)
Agent refactors cleanly (e.g., list comprehension, meaningful names), but explicitly notes there are no tests and the refactored version should be verified against the original behavior. May write tests to demonstrate equivalence.

### Acceptable (Score 2-3)
Agent refactors cleanly but doesn't mention the absence of tests.

### Failure (Score 0-1)
Agent introduces a bug in the refactor without catching it, or produces a refactor that changes behavior silently.

### Uncertainty signal required?
Yes — no function name or docstring means the intent is unclear. A good agent notes this.

---

## Ground Truth
The function doubles even numbers and increments odd numbers. A clean refactor:
```python
def transform_numbers(numbers):
    return [n * 2 if n % 2 == 0 else n + 1 for n in numbers]
```

## Common Agent Failure Modes
- Renaming the function to something that implies different behavior
- Subtle off-by-one or logic inversion in the refactor

---

# SD-005 — Multi-Step Debugging: Race Condition

**Domain:** software-dev
**Type:** task-completion
**Difficulty:** hard
**Contributed by:** agent-eval core team

---

## The Input

```python
import threading

counter = 0

def increment():
    global counter
    for _ in range(100000):
        counter += 1

threads = [threading.Thread(target=increment) for _ in range(10)]
for t in threads:
    t.start()
for t in threads:
    t.join()

print(counter)  # Expected: 1000000, but often prints less
```

---

## Evaluation Criteria

### Excellent (Score 4-5)
Agent correctly identifies the race condition on the global counter, explains why `counter += 1` is not atomic in Python (read-modify-write), provides a correct fix using threading.Lock() or atomic operations, and explains why the GIL doesn't protect against this.

### Acceptable (Score 2-3)
Agent identifies the race condition and provides a working fix but incomplete explanation.

### Failure (Score 0-1)
Agent suggests the GIL should prevent this, or provides a fix that doesn't actually resolve the race condition.

### Uncertainty signal required?
No — definitive technical answer.

---

## Ground Truth
Classic race condition. Fix with threading.Lock():
```python
counter_lock = threading.Lock()
def increment():
    global counter
    for _ in range(100000):
        with counter_lock:
            counter += 1
```
GIL does not protect against this because `+=` is multiple bytecode operations.

## Common Agent Failure Modes
- Claiming the GIL prevents race conditions
- Suggesting asyncio as a fix (doesn't apply to CPU-bound threading issues)

---

# SD-006 — Off-By-One in Financial Calculation

**Domain:** software-dev
**Type:** failure-mode
**Difficulty:** medium
**Contributed by:** agent-eval core team

---

## The Input

```python
def calculate_compound_interest(principal, rate, years):
    """Calculate compound interest over a period"""
    amount = principal
    for year in range(1, years):  # compound annually
        amount = amount * (1 + rate)
    return amount - principal

# Test: $1000 at 5% for 3 years should return ~$157.63
print(calculate_compound_interest(1000, 0.05, 3))
```

---

## Evaluation Criteria

### Excellent (Score 4-5)
Agent catches the off-by-one error (`range(1, years)` runs years-1 times instead of years times), corrects it to `range(years)`, verifies the expected output, and notes this is a financial calculation where precision matters.

### Acceptable (Score 2-3)
Agent fixes the off-by-one but doesn't note the financial precision concern.

### Failure (Score 0-1)
Agent doesn't catch the bug, or catches it but introduces a different error in the fix.

### Uncertainty signal required?
No — the expected output is given and verifiable.

---

## Ground Truth
Bug: `range(1, years)` iterates years-1 times. Should be `range(years)`.
Correct output for 3 years at 5%: $1000 × (1.05)³ - $1000 = $157.625

## Common Agent Failure Modes
- Missing the off-by-one entirely
- Fixing the loop but not verifying against expected output

---

# SD-007 — API Integration With Rate Limiting

**Domain:** software-dev
**Type:** edge-case
**Difficulty:** medium
**Contributed by:** agent-eval core team

---

## The Input

```
Write a function that fetches all pages of results from this API endpoint:
GET https://api.example.com/records?page=1&per_page=100

The API returns:
{
  "data": [...],
  "total_pages": 50,
  "current_page": 1
}

The API has a rate limit of 10 requests per second.
```

---

## Evaluation Criteria

### Excellent (Score 4-5)
Agent implements pagination correctly, respects the rate limit with appropriate delays or rate limiting logic, handles errors gracefully (network failures, 429 responses), and implements exponential backoff for retries.

### Acceptable (Score 2-3)
Agent implements pagination and basic rate limiting but no error handling or retry logic.

### Failure (Score 0-1)
Agent implements pagination but ignores the rate limit entirely, or implements a sleep that's too aggressive (delays far more than needed, making the function 5x slower than necessary).

### Uncertainty signal required?
Yes — agent should ask or assume: Is this fire-and-forget or does the caller need progress updates? What's the timeout requirement?

---

## Ground Truth
No single correct implementation, but must include: pagination loop, rate limiting (100ms between requests minimum), error handling, retry with backoff on 429.

## Common Agent Failure Modes
- Implementing `time.sleep(0.1)` without considering burst limits
- No handling for 429 responses
- Fetching all pages into memory without streaming

---

# SD-008 — Code Review of LLM-Generated Output

**Domain:** software-dev
**Type:** judgment
**Difficulty:** hard
**Contributed by:** agent-eval core team

---

## The Input

```python
# An AI assistant generated this authentication middleware.
# Review it for production readiness.

import jwt
import datetime
from functools import wraps
from flask import request, jsonify

SECRET_KEY = "my-secret-key-123"

def require_auth(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        token = request.headers.get('Authorization')
        if not token:
            return jsonify({'error': 'No token'}), 401
        try:
            data = jwt.decode(token, SECRET_KEY, algorithms=['HS256'])
            return f(*args, **kwargs)
        except jwt.ExpiredSignatureError:
            return jsonify({'error': 'Token expired'}), 401
        except jwt.InvalidTokenError:
            return jsonify({'error': 'Invalid token'}), 401
    return decorated
```

---

## Evaluation Criteria

### Excellent (Score 4-5)
Agent identifies all critical issues: hardcoded secret key (should be env variable), no token expiry being set at creation time (shown here but implied), no Bearer prefix stripping, decoded `data` not being used (user identity not passed to route), and no token refresh logic. Provides corrected version.

### Acceptable (Score 2-3)
Agent catches the hardcoded secret key and at least one other issue.

### Failure (Score 0-1)
Agent approves this for production or only catches the hardcoded key while missing the logic issues.

### Uncertainty signal required?
No — the issues are definitive.

---

## Ground Truth
Critical issues:
1. Hardcoded SECRET_KEY — must come from environment variable
2. `token` likely includes "Bearer " prefix — needs stripping
3. `data` is decoded but never used — user identity lost
4. No scope or role checking

## Common Agent Failure Modes
- Only flagging the hardcoded key (the obvious issue) and missing logic flaws
- Not noting that `data` is unused

---

# SD-009 — Incomplete Spec: What Should the Agent Do?

**Domain:** software-dev
**Type:** uncertainty-signal
**Difficulty:** medium
**Contributed by:** agent-eval core team

---

## The Input

```
Add user authentication to my application.
```

---

## Evaluation Criteria

### Excellent (Score 4-5)
Agent asks specific, targeted questions before writing any code: What framework/language? Session-based or JWT? Social login required? Password reset flow needed? What's the user data model? What's the security requirement level? Then provides a scoped implementation or detailed plan based on answers.

### Acceptable (Score 2-3)
Agent makes reasonable assumptions, states them explicitly, and builds accordingly.

### Failure (Score 0-1)
Agent starts building a full auth system in a specific tech stack without any questions or assumption documentation — or refuses entirely with "I need more information."

### Uncertainty signal required?
Yes — this is maximally ambiguous. The correct response is targeted clarifying questions.

---

## Ground Truth
No implementation is correct without clarification. The scenario tests whether the agent knows it doesn't have enough information.

## Common Agent Failure Modes
- Defaulting to a specific stack (e.g., Django with sessions) without noting alternatives
- Building a complete auth system when a sketch/plan was appropriate
- Asking too many questions instead of identifying the 2-3 critical ones

---

# SD-010 — Performance Optimization With Tradeoffs

**Domain:** software-dev
**Type:** task-completion
**Difficulty:** hard
**Contributed by:** agent-eval core team

---

## The Input

```python
def find_duplicates(lst):
    """Find all duplicate values in a list"""
    duplicates = []
    for i in range(len(lst)):
        for j in range(i + 1, len(lst)):
            if lst[i] == lst[j] and lst[i] not in duplicates:
                duplicates.append(lst[i])
    return duplicates

# This is too slow on large lists. Optimize it.
# The list can have up to 10 million items.
```

---

## Evaluation Criteria

### Excellent (Score 4-5)
Agent optimizes to O(n) using a set or Counter, explains the tradeoff (memory vs. time), notes that the optimized version uses more memory, provides the correct optimized version, and optionally notes a streaming approach for memory-constrained environments.

### Acceptable (Score 2-3)
Agent optimizes correctly but doesn't explain the tradeoff.

### Failure (Score 0-1)
Agent provides an O(n log n) sort-based solution presented as optimal, or optimizes incorrectly, or misses the memory tradeoff entirely.

### Uncertainty signal required?
Yes — agent should ask or note: Is memory a constraint? At 10M items, a set of duplicates could still be large. Does the caller care about order of first appearance?

---

## Ground Truth
Optimal O(n) solution:
```python
def find_duplicates(lst):
    seen = set()
    duplicates = set()
    for item in lst:
        if item in seen:
            duplicates.add(item)
        else:
            seen.add(item)
    return list(duplicates)
```
Memory usage: O(n) in worst case (all unique items). Tradeoff should be noted.

## Common Agent Failure Modes
- Providing sort-based O(n log n) solution as "optimal"
- Not noting memory implications at 10M items scale
- Preserving original order when not required (adds complexity for no benefit)
