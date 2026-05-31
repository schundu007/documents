# DevOps / SRE / Platform Engineering — Python Interview Scripts

A practical study guide of the Python coding problems that actually show up in
DevOps, SRE, and Platform Engineering interviews (PayPal, Intuit, NVIDIA,
Google, Meta, Atlassian, and similar loops). Every script here is runnable and
self-contained, with the talking points interviewers listen for.

> **The one thing to internalize:** these rounds are *not* LeetCode. They test
> whether your code survives hostile production conditions — networks
> partition, APIs rate-limit, JSON gets truncated, files don't fit in memory.
> A feature-developer who writes a bare `while True: retry` loop or a silent
> `except: continue` fails. The "strong hire" signal is **defensive boundaries,
> observability, and bounded resource use**.

What interviewers grade, beyond "does it work":

1. **Defensive boundaries** — timeouts on every network call, specific exception
   handling, never hang forever.
2. **Observability** — when you drop a malformed line, increment a counter so
   the on-call operator knows data was lost. Don't swallow errors.
3. **Bounded resources** — stream large files, cap concurrency, don't load 5 GB
   into a list.
4. **Idempotency & safety** — confirmation prompts, dry-run modes, verify after
   you act.

---

## Table of contents

1. [Log & text parsing](#1-log--text-parsing)
2. [Mini Unix tools (CLI)](#2-mini-unix-tools-cli)
3. [Rate limiting & throttling](#3-rate-limiting--throttling)
4. [Resilient API interaction](#4-resilient-api-interaction)
5. [Cloud automation with boto3](#5-cloud-automation-with-boto3)
6. [Detection & metrics](#6-detection--metrics)
7. [File operations & "too big for memory"](#7-file-operations--too-big-for-memory)
8. [Algorithmic / scheduling](#8-algorithmic--scheduling)
9. [Your three uploaded problems](#9-your-three-uploaded-problems)
10. [60-minute live-coding playbook](#10-60-minute-live-coding-playbook)

---

## 1. Log & text parsing

> *"Parse an nginx access log and print the top 10 IPs by request count."* This
> is the single most common Level-1 DevOps screen. File I/O, a `Counter`, sort.

### 1.1 Top N IPs from an access log (streaming)

```python
import sys
from collections import Counter

def top_ips(path: str, n: int = 10) -> list[tuple[str, int]]:
    """Stream the file line-by-line (never load it all) and rank IPs.

    Talking points: O(1) memory in the number of *lines*, only the distinct-IP
    set is held. Counter.most_common is a heap-based partial sort -> O(L log n).
    """
    counts: Counter[str] = Counter()
    malformed = 0
    with open(path, "r", encoding="utf-8", errors="replace") as fh:
        for line in fh:
            # Common Log Format: IP first whitespace-delimited token.
            ip = line.split(" ", 1)[0]
            if not ip or ip.count(".") != 3:
                malformed += 1          # observability, not silent drop
                continue
            counts[ip] += 1
    if malformed:
        print(f"warning: skipped {malformed} malformed lines", file=sys.stderr)
    return counts.most_common(n)


if __name__ == "__main__":
    for ip, c in top_ips(sys.argv[1] if len(sys.argv) > 1 else "access.log"):
        print(f"{c:>8}  {ip}")
```

### 1.2 Count log levels / extract a field

```python
import re
from collections import Counter

LEVEL = re.compile(r"\b(DEBUG|INFO|WARN|WARNING|ERROR|CRITICAL)\b")

def level_histogram(path: str) -> Counter:
    counts = Counter()
    with open(path, encoding="utf-8", errors="replace") as fh:
        for line in fh:
            m = LEVEL.search(line)
            if m:
                counts[m.group(1)] += 1
    return counts
```

**Follow-up they'll ask:** "Now it's a 5 GB file." Answer: you already stream it,
so memory is fine; mention `mmap` for random access, and that for *sorting* the
output you'd use the external-sort pattern in §7.

---

## 2. Mini Unix tools (CLI)

> *"Build `tail` for a huge file"* and *"build `tail -f`"* are classic Level-2
> tool-integration problems. The trap is reading the whole file; the signal is
> seeking from the end.

### 2.1 Last N lines without loading the file

```python
import os

def tail_n(path: str, n: int, block: int = 4096) -> list[bytes]:
    """Read the last n lines by seeking backwards in blocks from EOF."""
    with open(path, "rb") as f:
        f.seek(0, os.SEEK_END)
        size = f.tell()
        data = b""
        while size > 0 and data.count(b"\n") <= n:
            step = min(block, size)
            size -= step
            f.seek(size)
            data = f.read(step) + data
        return data.splitlines()[-n:]
```

### 2.2 `tail -f` (follow a growing file)

```python
import time

def follow(path: str, poll: float = 0.5):
    """Yield new lines as they are appended. Handles truncation/rotation."""
    with open(path, "r", encoding="utf-8", errors="replace") as f:
        f.seek(0, 2)  # jump to EOF
        while True:
            line = f.readline()
            if line:
                yield line
                continue
            # No new data: detect rotation (file shrank/replaced) then sleep.
            if os.stat(path).st_size < f.tell():
                f.seek(0)
            time.sleep(poll)
```

**Signal:** handling **log rotation** is the senior touch most candidates miss.

---

## 3. Rate limiting & throttling

> Rate limiters bridge coding and system design and appear in nearly every SRE
> loop. Know **token bucket** (allows bursts) and **sliding window** (smooth).

### 3.1 Token bucket

```python
import time

class TokenBucket:
    """Allow bursts up to `capacity`, refilling at `refill_rate` tokens/sec.

    monotonic() not time() so a clock adjustment can't break it.
    """
    def __init__(self, capacity: float, refill_rate: float):
        self.capacity = capacity
        self.tokens = capacity
        self.refill_rate = refill_rate
        self.ts = time.monotonic()

    def allow(self, cost: float = 1.0) -> bool:
        now = time.monotonic()
        self.tokens = min(self.capacity, self.tokens + (now - self.ts) * self.refill_rate)
        self.ts = now
        if self.tokens >= cost:
            self.tokens -= cost
            return True
        return False
```

### 3.2 Sliding-window logger rate limiter (dedupe within a window)

```python
class LoggerRateLimiter:
    """Print a given message at most once per `window` seconds (LeetCode 359)."""
    def __init__(self, window: int = 10):
        self.window = window
        self.last_seen: dict[str, int] = {}

    def should_print(self, timestamp: int, message: str) -> bool:
        last = self.last_seen.get(message)
        if last is None or timestamp - last >= self.window:
            self.last_seen[message] = timestamp
            return True
        return False
```

**Distributed follow-up:** move state to Redis with `INCR` + `EXPIRE` (fixed
window) or a sorted set of timestamps (sliding window); return
`X-RateLimit-Remaining` / `Retry-After` headers and HTTP 429.

---

## 4. Resilient API interaction

> *"Hit this API; it sometimes returns 503."* The naive `while True: retry` is a
> rejection. The signal is **bounded retries + exponential backoff + jitter +
> respect `Retry-After`**, plus timeouts on every call.

### 4.1 Retry with exponential backoff and jitter

```python
import random
import time
import requests

RETRYABLE = {429, 500, 502, 503, 504}

def get_with_retry(url: str, *, max_attempts: int = 5, base: float = 0.5,
                   timeout: float = 5.0) -> requests.Response:
    last_exc = None
    for attempt in range(max_attempts):
        try:
            resp = requests.get(url, timeout=timeout)  # never hang forever
            if resp.status_code not in RETRYABLE:
                resp.raise_for_status()
                return resp
            # Honor server's Retry-After if present.
            wait = float(resp.headers.get("Retry-After", base * (2 ** attempt)))
        except requests.RequestException as exc:
            last_exc = exc
            wait = base * (2 ** attempt)
        # full jitter avoids thundering-herd synchronization
        time.sleep(wait + random.uniform(0, base))
    raise RuntimeError(f"failed after {max_attempts} attempts: {url}") from last_exc
```

### 4.2 Defensive line-delimited JSON parsing (observability)

```python
import json

def parse_ndjson(path: str) -> tuple[list[dict], int]:
    """Return (records, malformed_count). Never silently drop bad lines."""
    records, malformed = [], 0
    with open(path, encoding="utf-8", errors="replace") as fh:
        for line in fh:
            line = line.strip()
            if not line:
                continue
            try:
                records.append(json.loads(line))
            except json.JSONDecodeError:
                malformed += 1   # operator must know data was dropped
    return records, malformed
```

> Paginated REST fetch (GitHub PRs) is in your uploaded set — see
> `github_pr_reporter.py` for `Link`/page-loop pagination + token auth.

---

## 5. Cloud automation with boto3

> *"Automate this manual ops task."* Cost-saving start/stop, tag governance,
> security audits. Always include **dry-run** and **pagination** (`get_paginator`).

### 5.1 Stop/start EC2 by tag (scheduled cost saving)

```python
import boto3

def set_ec2_state(action: str, tag_key: str, tag_value: str,
                  region: str = "us-east-1", dry_run: bool = True) -> list[str]:
    """action in {'start','stop'}. dry_run defaults True — opt IN to real action."""
    ec2 = boto3.client("ec2", region_name=region)
    affected = []
    paginator = ec2.get_paginator("describe_instances")
    pages = paginator.paginate(Filters=[{"Name": f"tag:{tag_key}", "Values": [tag_value]}])
    for page in pages:
        for res in page["Reservations"]:
            for inst in res["Instances"]:
                affected.append(inst["InstanceId"])
    if not affected:
        return []
    fn = ec2.start_instances if action == "start" else ec2.stop_instances
    fn(InstanceIds=affected, DryRun=dry_run)
    return affected
```

### 5.2 Security audit — find S3 buckets with public access

```python
import boto3
from botocore.exceptions import ClientError

def public_buckets(region: str = "us-east-1") -> list[str]:
    s3 = boto3.client("s3", region_name=region)
    flagged = []
    for b in s3.list_buckets()["Buckets"]:
        name = b["Name"]
        try:
            cfg = s3.get_public_access_block(Bucket=name)["PublicAccessBlockConfiguration"]
            if not all(cfg.values()):           # any block disabled == risk
                flagged.append(name)
        except ClientError as e:
            if e.response["Error"]["Code"] == "NoSuchPublicAccessBlockConfiguration":
                flagged.append(name)            # no block at all == risk
            else:
                raise
    return flagged
```

### 5.3 Tag governance — find resources missing a required tag

```python
import boto3

def untagged_instances(required: str = "Owner", region: str = "us-east-1") -> list[str]:
    ec2 = boto3.client("ec2", region_name=region)
    missing = []
    for page in ec2.get_paginator("describe_instances").paginate():
        for res in page["Reservations"]:
            for inst in res["Instances"]:
                tags = {t["Key"] for t in inst.get("Tags", [])}
                if required not in tags:
                    missing.append(inst["InstanceId"])
    return missing
```

> The full RDS scale-up/down automation (with Jenkins orchestration) is in your
> uploaded set — see `rds_scale.py` + `Jenkinsfile`.

---

## 6. Detection & metrics

### 6.1 Detect IPs exceeding N requests in a rolling window

```python
from collections import defaultdict, deque

class AbuseDetector:
    """Flag an IP once it exceeds `threshold` requests within `window` seconds.

    Per-IP deque of timestamps; evict expired on each record. O(1) amortized.
    """
    def __init__(self, threshold: int, window: int):
        self.threshold = threshold
        self.window = window
        self.hits: dict[str, deque] = defaultdict(deque)

    def record(self, ip: str, ts: int) -> bool:
        q = self.hits[ip]
        q.append(ts)
        while q and q[0] <= ts - self.window:
            q.popleft()
        return len(q) > self.threshold
```

### 6.2 Percentiles (p50 / p95 / p99) from a sample

```python
def percentile(data: list[float], p: float) -> float:
    """Linear-interpolation percentile. p in [0,100]."""
    if not data:
        raise ValueError("no data")
    s = sorted(data)
    k = (len(s) - 1) * (p / 100)
    f = int(k)
    c = min(f + 1, len(s) - 1)
    return s[f] + (s[c] - s[f]) * (k - f)

# p50/p95/p99 are the latency SLO numbers interviewers expect you to name.
```

**Follow-up:** "millions of points, streaming?" — name **t-digest** or **HDR
histogram** for approximate percentiles in bounded memory.

---

## 7. File operations & "too big for memory"

### 7.1 Copy files matching a pattern across folders

```python
import shutil
from pathlib import Path

def gather(src_dirs: list[str], pattern: str, dest: str) -> int:
    out = Path(dest)
    out.mkdir(parents=True, exist_ok=True)
    n = 0
    for d in src_dirs:
        for f in Path(d).glob(pattern):
            if f.is_file():
                shutil.copy2(f, out / f.name)   # copy2 preserves metadata
                n += 1
    return n
```

### 7.2 External sort — a file too large to fit in RAM

```python
import heapq
import os
import tempfile

def external_sort(in_path: str, out_path: str, chunk_lines: int = 1_000_000) -> None:
    """Split into sorted chunks on disk, then k-way merge. Bounded memory."""
    chunks = []
    with open(in_path, encoding="utf-8", errors="replace") as fh:
        while True:
            block = [fh.readline() for _ in range(chunk_lines)]
            block = [ln for ln in block if ln]
            if not block:
                break
            block.sort()
            tmp = tempfile.NamedTemporaryFile("w", delete=False, encoding="utf-8")
            tmp.writelines(block)
            tmp.close()
            chunks.append(tmp.name)
    try:
        files = [open(c, encoding="utf-8") for c in chunks]
        with open(out_path, "w", encoding="utf-8") as out:
            out.writelines(heapq.merge(*files))   # streaming k-way merge
    finally:
        for f in files:
            f.close()
        for c in chunks:
            os.unlink(c)
```

### 7.3 Find duplicate files by content hash

```python
import hashlib
from collections import defaultdict
from pathlib import Path

def find_dupes(root: str) -> dict[str, list[str]]:
    by_hash: dict[str, list[str]] = defaultdict(list)
    for f in Path(root).rglob("*"):
        if f.is_file():
            h = hashlib.sha256()
            with open(f, "rb") as fh:
                for block in iter(lambda: fh.read(65536), b""):  # stream, don't read all
                    h.update(block)
            by_hash[h.hexdigest()].append(str(f))
    return {h: paths for h, paths in by_hash.items() if len(paths) > 1}
```

---

## 8. Algorithmic / scheduling

Occasionally a loop includes a genuinely algorithmic problem dressed in infra
clothing — dependency resolution, build scheduling, DAG ordering. Your NVIDIA
**DevImageBuilder** (layered-image build scheduling via a prefix tree, scheduled
backwards from deadlines) is the prime example — see `dev_image_builder.py`.

Adjacent ones worth knowing:

- **Topological sort** of a dependency DAG (Terraform graph, build order) —
  Kahn's algorithm.
- **Detect a cycle** in a dependency graph (would-be infinite Terraform apply).

```python
from collections import defaultdict, deque

def topo_order(deps: dict[str, list[str]]) -> list[str]:
    """deps[node] = nodes it depends on. Returns a valid build order or raises on a cycle."""
    indeg = defaultdict(int)
    graph = defaultdict(list)
    nodes = set(deps)
    for node, prereqs in deps.items():
        for p in prereqs:
            graph[p].append(node)
            indeg[node] += 1
            nodes.add(p)
    q = deque(n for n in nodes if indeg[n] == 0)
    order = []
    while q:
        n = q.popleft()
        order.append(n)
        for m in graph[n]:
            indeg[m] -= 1
            if indeg[m] == 0:
                q.append(m)
    if len(order) != len(nodes):
        raise ValueError("dependency cycle detected")
    return order
```

---

## 9. Your three uploaded problems

Condensed solutions below. Full hardened versions (auth, pagination, error
handling, tests) are in the standalone files `github_pr_reporter.py`,
`rds_scale.py` + `Jenkinsfile`, and `dev_image_builder.py`.

### 9.1 GitHub PR Reporter — *PayPal / Venmo*

**Problem.** Fetch every PR from a public repo via the GitHub REST API. Print
`✓ author: #num "title"` if all checks passed, else `✗`. (Repo:
`venmo/foundations-interview`.) Shows: REST pagination, the checks API, defensive
API calls.

```python
import requests
from dataclasses import dataclass

OWNER, REPO = "venmo", "foundations-interview"
API, OK = "https://api.github.com", {"success", "neutral", "skipped"}

@dataclass
class PR:
    number: int; title: str; author: str; passed: bool
    def render(self):
        return f'{"✓" if self.passed else "✗"} {self.author}: #{self.number} "{self.title}"'

def checks_passed(s, sha):  # both Checks API and legacy status must be OK
    runs = s.get(f"{API}/repos/{OWNER}/{REPO}/commits/{sha}/check-runs", timeout=30).json()
    state = s.get(f"{API}/repos/{OWNER}/{REPO}/commits/{sha}/status", timeout=30).json().get("state")
    return all(r.get("conclusion") in OK for r in runs.get("check_runs", [])) and state != "failure"

def report():
    s = requests.Session(); s.headers["Accept"] = "application/vnd.github+json"
    prs, page = [], 1
    while batch := s.get(f"{API}/repos/{OWNER}/{REPO}/pulls",
                         params={"state": "all", "per_page": 100, "page": page}, timeout=30).json():
        prs += batch; page += 1
    for p in sorted(prs, key=lambda x: x["number"]):
        pr = PR(p["number"], p["title"], (p.get("user") or {}).get("login", "?"),
                checks_passed(s, p["head"]["sha"]))
        print(pr.render())
```

> Set `GITHUB_TOKEN` (Bearer header) or you hit the 60-req/hr anonymous limit.

### 9.2 RDS instance-class scaler — *Intuit*

**Problem.** Automate the manual "scale RDS up/down for business cycles" task with
Jenkins + AWS. Params: DB name and target class (dropdowns). Stages: read current
class → if unchanged, message + succeed → confirmation prompt → modify → verify.
Shows: boto3 admin actions, idempotency, confirmation gate, verify-after-act.

```python
import boto3, time
rds = boto3.client("rds", region_name="us-east-1")

def current_class(db):
    return rds.describe_db_instances(DBInstanceIdentifier=db)["DBInstances"][0]["DBInstanceClass"]

def scale(db, new_class, timeout=1800):
    if current_class(db) == new_class:
        print("No change needed"); return                      # idempotent
    rds.modify_db_instance(DBInstanceIdentifier=db, DBInstanceClass=new_class, ApplyImmediately=True)
    end = time.time() + timeout
    while time.time() < end:                                   # verify after acting
        i = rds.describe_db_instances(DBInstanceIdentifier=db)["DBInstances"][0]
        if i["DBInstanceStatus"] == "available" and i["DBInstanceClass"] == new_class:
            print("Scaled OK"); return
        time.sleep(20)
    raise TimeoutError("scale did not complete")
```

```groovy
// Jenkinsfile (declarative) — confirmation gate is the key safety control
pipeline {
  agent any
  parameters {
    choice(name: 'DB', choices: ['orders-db', 'payments-db'])
    choice(name: 'CLASS', choices: ['db.t3.medium', 'db.r6g.large', 'db.r6g.xlarge'])
  }
  stages {
    stage('Confirm') { steps { input "Scale ${params.DB} to ${params.CLASS}?" } }
    stage('Scale')   { steps { withAWS(credentials: 'aws-rds-admin', region: 'us-east-1') {
      sh "python3 rds_scale.py modify --db-name ${params.DB} --new-class ${params.CLASS}"
      sh "python3 rds_scale.py verify --db-name ${params.DB} --new-class ${params.CLASS}" } } }
  }
  post { failure { echo 'Scaling FAILED' } }
}
```

### 9.3 DevImageBuilder — *NVIDIA*

**Problem.** Each developer wants a layered image (e.g. `aarch64.zed_camera`)
ready before their wake time. Images build one layer at a time on a base image.
Build each image *as late as possible*, never build the same image twice, reuse
shared bases. Shows: prefix-tree dedupe, backwards-from-deadline scheduling.

**Key insight.** Required images form a prefix tree; each distinct prefix = one
job. Schedule backwards: a node must finish by the earliest of (its developers'
wake time, every child's start). `start = deadline − build_time`; solve deepest
first so children are known before parents.

```python
from collections import defaultdict

def find_build_schedule(developers, layer_map):
    INF = float("inf")
    last, base_of, kids = {}, {}, defaultdict(set)
    deadline = defaultdict(lambda: INF); images = set()
    for d in developers:                                   # build the prefix tree
        names = d.image_str.split("."); deadline[d.image_str] = min(deadline[d.image_str], d.wake_time)
        base = ""
        for i, n in enumerate(names):
            cur = ".".join(names[:i + 1]); images.add(cur); last[cur] = layer_map[n]; base_of[cur] = base
            if base: kids[base].add(cur)
            base = cur
    start = {}
    for s in sorted(images, key=lambda x: x.count("."), reverse=True):  # deepest first
        dl = min([deadline[s]] + [start[c] for c in kids[s]])
        start[s] = dl - last[s].build_time
    mk = lambda s: Image([layer_map[n] for n in s.split(".")] if s else [])
    return sorted(((start[s], Job(mk(base_of[s]), last[s])) for s in images), key=lambda x: x[0])
```

> Shared `aarch64` builds once and finishes at 09:30, so Charlie's 30-min
> `zed_camera` is ready exactly at 10:00 — matching the problem's hint.

---

## 10. 60-minute live-coding playbook

A repeatable structure that reads as senior in any of these rounds:

1. **Clarify (2–3 min).** Input size? One file or streaming? Expected output
   format? Edge cases (empty input, malformed lines, file rotation)? State your
   assumptions out loud.
2. **State the approach (1 min).** Name the data structure and complexity
   *before* typing. "Counter for O(1) increment, most_common for the heap."
3. **Code the happy path**, then immediately **harden the boundaries** — add the
   timeout, the specific `except`, the malformed-line counter. Narrate *why*.
4. **Test with a tiny case** you type inline; show it runs.
5. **Volunteer the scale follow-up** before they ask: "for 5 GB I stream; for
   distributed I'd move state to Redis; for millions of points I'd use a
   t-digest." This is what separates a hire from a strong hire.

**Memorize these reflexes:**
- Every network call gets a `timeout=`.
- Never `except:` bare — catch the specific exception.
- Dropped/skipped data gets *counted and reported*, never silently ignored.
- Large files stream; never `f.read()` the whole thing.
- Anything destructive gets a `dry_run`/confirmation gate and a verify step.
