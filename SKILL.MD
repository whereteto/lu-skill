---
name: lu-skill
description: ULTRA-DETAILED Rules for All Programming Languages and AI models.
---

# AGENTS.MD – **ULTRA-DETAILED** Rules for All Programming Languages
*(Applies to ALL: C/C++, Python, JS/TS, Go, Rust, Java, Ruby, PHP, Swift, Kotlin, etc.)*

---

## 📚 **Table of Contents**
1. [Rule 1: Think Before Acting](#-rule-1-think-before-acting)
2. [Rule 2: Maximum Optimization](#-rule-2-maximum-optimization)
3. [Rule 3: Exhaustive Testing](#-rule-3-exhaustive-testing)
4. [Rule 4: Ethical Hacking](#-rule-4-ethical-hacking)
5. [Rule 5: Clean & Resilient Code](#-rule-5-clean--resilient-code)
6. [Protocols](#-protocols)
7. [Checklists](#-checklists)

---

## 🚨 **Rule 1: Think Before Acting (Deep Consequence Analysis)**

### **1.1 Mandatory Pre-Code Analysis**
**Before writing ANY line of code, complete this table for every change:**

| **Aspect**          | **Impact** | **Risk (1-10)** | **Mitigation** | **Verification** |
|---------------------|------------|-----------------|----------------|------------------|
| Performance         | ?          | ?               | ?              | Benchmark        |
| Memory Usage        | ?          | ?               | ?              | `valgrind`       |
| Security            | ?          | ?               | ?              | SAST/DAST        |
| Dependencies        | ?          | ?               | ?              | `npm audit`      |
| Maintainability     | ?          | ?               | ?              | Code review      |

**Example:**
| **Aspect**          | **Impact** | **Risk** | **Mitigation** | **Verification** |
|---------------------|------------|----------|----------------|------------------|
| Add new API endpoint| +50ms latency | 6        | Cache responses | Load test         |
| Use regex           | ReDoS risk  | 8        | Use `re2`       | Fuzz test         |

### **1.2 The 5 Whys Technique**
For every problem, ask "Why?" **5 times** to find the root cause.
**Example:**
- Problem: "App is slow"
  1. Why? → "Database queries are slow"
  2. Why? → "N+1 query problem"
  3. Why? → "ORM fetches related data in a loop"
  4. Why? → "No batch loading support"
  5. Why? → "Didn’t implement DataLoader"
→ **Solution:** Implement DataLoader or `SELECT ... JOIN`.

### **1.3 Static Analysis (Pre-Code)**
**Run these BEFORE writing new code:**
```bash
# C/C++: Full analysis
clang-tidy -checks='-* ,bugprone-*,clang-analyzer-*,cppcoreguidelines-*' file.cpp
cppcheck --enable=all --inconclusive file.cpp

# Python: Strict mode
pylint --disable=C0114,C0115,C0116 --enable=E,F --fail-under=10.0 code.py
mypy --strict --disallow-untyped-defs code.py
bandit -r . -f json

# JavaScript/TypeScript: Security + strict
eslint --ext .js,.ts --config .eslintrc-hardcore.js src/
tsc --strict --noImplicitAny --noImplicitReturns

# Go: All linters
golangci-lint run --config .golangci-ultra.yml

# Rust: All checks
cargo clippy --all-targets --all-features -- -D warnings -D clippy::pedantic
cargo audit
```

### **1.4 Dependency Impact Analysis**
**Before adding/updating ANY dependency:**
```bash
# Vulnerability scan
npm audit --audit-level=high      # Node.js
pip-audit --strict                 # Python
govulncheck ./...                  # Go
cargo audit                        # Rust
snyk test                           # Multi-language

# License check
license-checker --summary

# Maintenance check
# - Last commit < 1 year ago? → REJECT
# - >100 open issues with no responses? → REJECT
# - Uses deprecated APIs? → REJECT

# Bundle size (frontend)
npx bundlephobia-cli package-name
```

### **1.5 Performance Budgeting**
**Define HARD LIMITS for critical paths:**

| **Metric**          | **Web App** | **CLI Tool** | **Backend Service** |
|---------------------|-------------|--------------|--------------------|
| Latency (p99)       | < 200ms     | < 1s         | < 500ms            |
| Memory Usage        | < 50MB      | < 100MB      | < 200MB            |
| CPU Usage           | < 50%       | < 80%        | < 70%              |
| Startup Time        | < 1s        | < 2s         | < 3s               |
| DB Queries/Request  | < 10        | N/A          | < 5                |

**❌ If a change violates the budget → REJECT or OPTIMIZE FIRST.**

---

## ⚡ **Rule 2: Maximum Optimization (Reliable & Fast Code)**

### **2.1 Optimization Hierarchy (Priority Order)**
1. **🔴 Algorithmic Efficiency** (Big-O improvements)
2. **🟡 Data Structure Choice** (HashMap vs List, etc.)
3. **🟢 I/O Optimization** (Batch operations, caching)
4. **🔵 Concurrency/Parallelism** (Threads, async, multiprocessing)
5. **🟣 Memory Layout** (Cache locality, struct padding)
6. **⚪ Compiler Hints** (Inline, unroll, JIT)
7. **⚫ Micro-Optimizations** (Last resort!)

### **2.2 Algorithmic Optimization**

#### **Big-O Cheat Sheet**
| **Operation**               | **❌ Bad (Avoid)** | **✅ Good (Use)** | **When** |
|-----------------------------|-------------------|------------------|----------|
| Lookup by key               | O(n) list scan    | O(1) HashMap     | Always   |
| Insertion at front          | O(n) array shift   | O(1) LinkedList  | Frequent insertions |
| Search sorted data          | O(n) linear scan  | O(log n) Binary Search | Static sorted data |
| Sorting                     | O(n²) Bubble Sort | O(n log n) QuickSort | Default |
| String concat in loop       | O(n²) `s += x`    | O(n) StringBuilder | Always |
| Substring search            | O(nm) Naive       | O(n) KMP/Boyer-Moore | Large texts |

#### **Language-Specific Examples**

**C/C++:**
```cpp
// ❌ O(n²) reallocations
vector<int> v;
for (int i=0; i<n; i++) v.push_back(i);

// ✅ O(1) reallocations
vector<int> v; v.reserve(n);
for (int i=0; i<n; i++) v.push_back(i);

// 🔥 Cache-friendly iteration
for (size_t i=0; i<v.size(); i++) process(v[i]); // Sequential = cache hits
```

**Python:**
```python
# ❌ O(n²) list concat
result = []
for x in large_list: result += [x*2]  # New list each time!

# ✅ O(n) list comprehension
result = [x*2 for x in large_list]

# 🔥 Built-ins (C-optimized)
sum = sum(numbers)  # Not: for x in numbers: sum += x
```

**JavaScript:**
```javascript
// ❌ O(n²) nested loops
for (const a of arrA) {
    for (const b of arrB) {
        if (a.id === b.id) result.push({...a, ...b});
    }
}

// ✅ O(n) with Map
const map = new Map(arrB.map(b => [b.id, b]));
const result = arrA.map(a => map.has(a.id) ? {...a, ...map.get(a.id)} : a);
```

**Go:**
```go
// ❌ Slice may reallocate O(log n) times
s := []int{}
for i := 0; i < n; i++ { s = append(s, i) }

// ✅ Preallocate
s := make([]int, 0, n)
for i := 0; i < n; i++ { s = append(s, i) }

// 🔥 Object pool for reuse
var bufPool = sync.Pool{New: func() interface{} { return bytes.NewBuffer(make([]byte, 1024)) }}
```

**Rust:**
```rust
// ❌ Unnecessary allocations
let v: Vec<i32> = (0..n).collect();
let doubled: Vec<i32> = v.iter().map(|x| x * 2).collect();

// ✅ Iterator chaining (no intermediate alloc)
let doubled: Vec<i32> = (0..n).map(|x| x * 2).collect();

// 🔥 ndarray for numerical ops
use ndarray::Array;
let a = Array::linspace(0., 10., 1000);
let b = &a * 2.; // Vectorized!
```

### **2.3 Data Structure Optimization**

| **Data Structure**   | **Time** | **Use Case** | **Language** |
|----------------------|----------|--------------|--------------|
| Array/List           | O(1) access | Sequential data | All |
| HashMap/Dict         | O(1) avg | Key-value lookups | All |
| Set                  | O(1) avg | Membership testing | All |
| TreeMap              | O(log n) | Sorted keys, range queries | Java, C++ (`std::map`) |
| Trie                 | O(L) | Prefix searches | Custom |
| Bloom Filter         | O(1) | Probabilistic membership | `pybloom-live` |
| LRU Cache            | O(1) | Caching with eviction | `lru_cache` (Python) |

**Custom Union-Find (Disjoint Set):**
```python
class UnionFind:
    def __init__(self, size):
        self.parent = list(range(size))
        self.rank = [0] * size
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # Path compression
        return self.parent[x]
    
    def union(self, x, y):
        x_root, y_root = self.find(x), self.find(y)
        if x_root == y_root: return
        if self.rank[x_root] < self.rank[y_root]:
            self.parent[x_root] = y_root
        else:
            self.parent[y_root] = x_root
            if self.rank[x_root] == self.rank[y_root]:
                self.rank[x_root] += 1
```

### **2.4 I/O Optimization**

#### **Database:**
```sql
-- ❌ N+1 queries
SELECT * FROM users WHERE id = 1;
SELECT * FROM orders WHERE user_id = 1;

-- ✅ JOIN
SELECT users.*, orders.* 
FROM users 
LEFT JOIN orders ON users.id = orders.user_id 
WHERE users.id = 1;

-- 🔥 Batch insert
INSERT INTO users (name, email) VALUES
('Alice', 'a@test.com'), ('Bob', 'b@test.com');
```

#### **API Calls:**
```javascript
// ❌ Sequential requests
for (const id of ids) {
    const data = await fetch(`/api/items/${id}`);
}

// ✅ Parallel with concurrency limit
import pLimit from 'p-limit';
const limit = pLimit(10);
const promises = urls.map(url => limit(() => fetch(url)));
await Promise.all(promises);

// 🔥 Batch endpoint
await fetch('/api/items/batch', { method: 'POST', body: JSON.stringify({ ids }) });
```

#### **File I/O:**
```python
# ❌ Read entire file into memory
with open('large.txt', 'r') as f: data = f.read()

# ✅ Stream line by line
with open('large.txt', 'r') as f:
    for line in f: process(line)

# 🔥 Buffered chunks
CHUNK_SIZE = 8192
with open('large.txt', 'rb') as f:
    while chunk := f.read(CHUNK_SIZE):
        process(chunk)
```

### **2.5 Concurrency & Parallelism**

| **Approach**    | **Use Case** | **Languages** | **Caveats** |
|-----------------|--------------|---------------|-------------|
| Threading       | I/O-bound, shared memory | C++/Java/Go | GIL (Python), race conditions |
| Async/Await     | I/O-bound, single-threaded | JS/Python/Go/Rust | Not for CPU-bound |
| Multiprocessing | CPU-bound | Python/Go | IPC overhead |
| Goroutines      | High concurrency | Go | Lightweight |

**Python (Multiprocessing for CPU-bound):**
```python
from multiprocessing import Pool

def process_item(item):
    return item * 2  # CPU-intensive

if __name__ == '__main__':
    with Pool(processes=4) as pool:
        results = pool.map(process_item, large_list)
```

**Go (Goroutines for I/O-bound):**
```go
func processAll(urls []string) []string {
    results := make(chan string, len(urls))
    var wg sync.WaitGroup
    
    for _, url := range urls {
        wg.Add(1)
        go func(u string) {
            defer wg.Done()
            resp, _ := http.Get(u)
            results <- "processed " + u
        }(url)
    }
    
    wg.Wait()
    close(results)
    
    var output []string
    for r := range results {
        output = append(output, r)
    }
    return output
}
```

### **2.6 Memory Optimization**

#### **Object Pooling (Java):**
```java
ObjectPool<ExpensiveObject> pool = new GenericObjectPool<>(new ExpensiveObjectFactory());
ExpensiveObject obj = pool.borrowObject();
// use obj
pool.returnObject(obj);
```

#### **Flyweight Pattern (Python):**
```python
class Flyweight:
    _pool = {}
    def __new__(cls, key):
        if key not in cls._pool:
            cls._pool[key] = super().__new__(cls)
        return cls._pool[key]
```

#### **Memory Profiling:**
```bash
# C/C++: Valgrind
valgrind --tool=massif --massif-out-file=massif.out ./app
ms_print massif.out

# Python: memory-profiler
pip install memory-profiler
python -m memory_profiler script.py

# Go: pprof
go test -bench=. -memprofile=mem.out
go tool pprof mem.out

# Rust: heaptrack
heaptrack ./target/release/app
```

### **2.7 Benchmarking**

| **Language** | **Tool** | **Command** |
|--------------|----------|-------------|
| C/C++ | Google Benchmark | `./benchmark --benchmark_filter=.*` |
| Python | pytest-benchmark | `pytest --benchmark-only` |
| JavaScript | benchmark.js | `node bench.js` |
| Go | Built-in | `go test -bench=.` |
| Rust | criterion.rs | `cargo bench` |
| Java | JMH | `java -jar benchmarks.jar` |

**C++ Example:**
```cpp
#include <benchmark/benchmark.h>
static void BM_StringCreation(benchmark::State& state) {
    for (auto _ : state) std::string empty_string;
}
BENCHMARK(BM_StringCreation);
```

**Python Example:**
```python
import pytest
@pytest.mark.benchmark
def test_list_vs_generator():
    [x*2 for x in range(1000)]  # List comprehension
    (x*2 for x in range(1000))  # Generator
```

---

## 🧪 **Rule 3: Exhaustive Testing (Multi-Layer Validation)**

### **3.1 Testing Pyramid**
```
        /\        → UI Tests (5-10%) - Slow, brittle
       /----\     → Integration Tests (20-30%) - Medium
      /--------\  → Unit Tests (60-70%) - Fast, reliable
     /__________\
```

### **3.2 Unit Testing**

#### **FIRST Principles:**
- **Fast**: Tests run in milliseconds.
- **Isolated**: No external dependencies.
- **Repeatable**: Same input → same output.
- **Self-validating**: Assertions, no manual checks.
- **Timely**: Write tests **before or with** the code (TDD).

#### **AAA Pattern (Arrange-Act-Assert):**
```python
# ✅ Good
def test_add():
    # Arrange
    a, b, expected = 2, 3, 5
    
    # Act
    result = add(a, b)
    
    # Assert
    assert result == expected
```

#### **Language Examples:**

**C++ (Google Test):**
```cpp
TEST(CalculatorTest, AddPositiveNumbers) {
    EXPECT_EQ(5, Calculator::Add(2, 3));
}

// Parameterized tests
TEST_P(CalculatorTest, AddParametrized) {
    auto [a, b, expected] = GetParam();
    EXPECT_EQ(expected, Calculator::Add(a, b));
}
INSTANTIATE_TEST_SUITE_P(CalculatorTests, CalculatorTest,
    Values(std::make_tuple(2, 3, 5), std::make_tuple(-1, -1, -2)));
```

**Python (pytest):**
```python
import pytest

@pytest.mark.parametrize("a,b,expected", [(2, 3, 5), (-1, -1, -2), (0, 0, 0)])
def test_add(a, b, expected):
    assert add(a, b) == expected

# Fixtures
@pytest.fixture
def database():
    db = Database()
    db.connect()
    yield db
    db.disconnect()

def test_query(database):
    assert database.query("SELECT 1") == 1

# Mocking
from unittest.mock import patch

def test_external_api():
    with patch('requests.get') as mock_get:
        mock_get.return_value.status_code = 200
        mock_get.return_value.json.return_value = {"key": "value"}
        assert fetch_data() == {"key": "value"}
```

**Go:**
```go
func TestAdd(t *testing.T) {
    tests := []struct { a, b, expected int }{
        {2, 3, 5}, {-1, -1, -2}, {0, 0, 0},
    }
    for _, tt := range tests {
        t.Run("", func(t *testing.T) {
            if got := Add(tt.a, tt.b); got != tt.expected {
                t.Errorf("Add(%d, %d) = %d; want %d", tt.a, tt.b, got, tt.expected)
            }
        })
    }
}
```

**Rust:**
```rust
#[test]
fn test_add() {
    assert_eq!(add(2, 3), 5);
}

#[test]
#[should_panic(expected = "division by zero")]
fn test_divide_by_zero() {
    divide(1, 0);
}

#[bench]
fn bench_add(b: &mut Bencher) {
    b.iter(|| add(2, 3));
}
```

### **3.3 Integration Testing**

**Python (TestContainers):**
```python
from testcontainers.postgres import PostgresContainer

@pytest.fixture(scope="session")
def postgres():
    with PostgresContainer() as pg:
        yield pg

@pytest.mark.integration
def test_database_connection(postgres):
    conn = psycopg2.connect(
        host=postgres.get_container_host_ip(),
        port=postgres.get_exposed_port(5432),
        user="postgres", password="postgres", dbname="postgres"
    )
    cursor = conn.cursor()
    cursor.execute("SELECT 1")
    assert cursor.fetchone() == (1,)
```

**JavaScript (Supertest):**
```javascript
const request = require('supertest');
const app = require('../app');

describe('POST /api/users', () => {
    it('creates a new user', async () => {
        const res = await request(app)
            .post('/api/users')
            .send({ name: 'John', email: 'john@example.com' });
        expect(res.statusCode).toEqual(201);
        expect(res.body).toHaveProperty('id');
    });
});
```

### **3.4 Property-Based Testing**

| **Language** | **Tool** | **Example** |
|--------------|----------|-------------|
| Python | `hypothesis` | `@given(ints(), ints())` |
| JavaScript | `fast-check` | `fc.integer(), fc.array()` |
| Rust | `proptest` | `prop_compose!` |
| Java | `jqwik` | `@Property` |
| Go | `rapid` | `rapid.ForEach` |

**Python (Hypothesis):**
```python
from hypothesis import given, strategies as st

@given(st.integers(), st.integers())
def test_add_commutative(a, b):
    assert add(a, b) == add(b, a)

@given(st.lists(st.integers()))
def test_reverse_twice_identity(lst):
    assert reverse(reverse(lst)) == lst
```

**Rust (proptest):**
```rust
use proptest::prelude::*;

proptest! {
    #[test]
    fn add_commutative(a in any::<i32>(), b in any::<i32>()) {
        assert_eq!(add(a, b), add(b, a));
    }
}
```

### **3.5 Stress Testing**

| **Type** | **Tool** | **Command** |
|----------|----------|-------------|
| Load Testing | `locust` | `locust -f locustfile.py --headless -u 1000` |
| Load Testing | `k6` | `k6 run --vus 100 --duration 30s script.js` |
| Stress Testing | `stress-ng` | `stress-ng --cpu 4 --vm 2 --timeout 60s` |
| Fuzzing | `AFL` | `afl-fuzz -i inputs/ -o outputs/ ./app @@` |
| Fuzzing | `libFuzzer` | `clang -fsanitize=fuzzer,afl test_fuzz.cc` |

**Locust Example:**
```python
from locust import HttpUser, task, between

class WebsiteUser(HttpUser):
    wait_time = between(1, 5)
    
    @task
    def load_homepage(self):
        self.client.get("/")
    
    @task(3)
    def load_api(self):
        self.client.get("/api/data")
```

### **3.6 Edge Case Testing**

**Common Edge Cases:**
| **Category** | **Examples** |
|--------------|--------------|
| Empty Inputs | `""`, `[]`, `{}`, `null`, `0` |
| Boundary Values | `INT_MAX`, `INT_MIN`, `0`, `1`, `-1` |
| Invalid Inputs | `null`, `undefined`, `NaN`, `Infinity` |
| Large Inputs | 1MB string, 1M-element array |
| Special Chars | `
`, `	`, `"`, `'`, Unicode, emojis |
| Time-Based | `1970-01-01`, `2038-01-19`, `9999-12-31` |
| Concurrency | Race conditions, deadlocks |

**Example Test:**
```python
@pytest.mark.parametrize("a,b", [
    (0, 1), (1, 0), (2**63-1, 1), (-2**63, 1), (1, 3),
    (1.5, 2), ("1", "2"), (None, 1)
])
def test_divide_edge_cases(a, b):
    if b == 0:
        with pytest.raises(ZeroDivisionError):
            divide(a, b)
    else:
        assert isinstance(divide(a, b), (int, float))
```

### **3.7 Test Coverage**

| **Type** | **Min Coverage** | **Ideal Coverage** | **Tool** |
|----------|------------------|---------------------|----------|
| Unit Tests | 80% | 100% | `coverage.py`, `gcov`, `istanbul` |
| Integration | 60% | 80% | Same |
| E2E | 40% | 60% | Same |

**Python:**
```bash
coverage run -m pytest
coverage html  # Open htmlcov/index.html
coverage report --fail-under=80
```

---

## 🔓 **Rule 4: Ethical Hacking (Post-Compilation Security Analysis)**

### **4.1 Methodology (OWASP Testing Guide v5)**
1. **Reconnaissance** (Info gathering)
2. **Scanning** (Automated vulnerability detection)
3. **Exploitation** (Manual testing)
4. **Post-Exploitation** (Impact analysis)
5. **Reporting** (Document findings)
6. **Fixing** (Remediate)
7. **Verification** (Confirm fixes)

### **4.2 Phase 1: Compile and Execute**

**Secure Compilation Flags:**
| **Language** | **Flags** |
|--------------|-----------|
| C/C++ | `-D_FORTIFY_SOURCE=2 -fstack-protector-strong -Wl,-z,noexecstack -Wl,-z,relro` |
| Rust | `--release` + `RUSTFLAGS="-C target-feature=+crt-static"` |
| Go | `-ldflags="-w -s"` (strip symbols) |
| Python | `-O` + `PYTHONHASHSEED=randomized` |

**Example (C):**
```bash
gcc -O2 -Wall -Wextra -Werror -fstack-protector-strong -D_FORTIFY_SOURCE=2 -o app app.c
```

**Build Hardening:**
```bash
# Strip symbols
strip --strip-all your_binary

# PIE (Position Independent Executable)
gcc -fPIE -pie -o app app.c

# Static linking (C/Rust)
gcc -static -o app app.c
cargo build --release --target x86_64-unknown-linux-musl

# Minimal Docker images
FROM alpine:latest  # or distroless:static
```

### **4.3 Phase 2: Static Analysis (SAST)**

**Tools by Language:**
| **Language** | **Tool** | **Command** |
|--------------|----------|-------------|
| C/C++ | CodeQL | `codeql analyze --format=sarif` |
| C/C++ | cppcheck | `cppcheck --enable=all .` |
| C/C++ | Clang Static Analyzer | `scan-build make` |
| Python | Bandit | `bandit -r .` |
| Python | Semgrep | `semgrep --config=auto .` |
| JavaScript | ESLint (security) | `eslint --config security` |
| JavaScript | npm audit | `npm audit --audit-level=high` |
| Go | gosec | `gosec ./...` |
| Go | staticcheck | `staticcheck ./...` |
| Rust | cargo-audit | `cargo audit` |
| Rust | clippy | `cargo clippy -- -D warnings` |
| Java | SpotBugs | `mvn spotbugs:check` |
| All | SonarQube | `sonar-scanner` |
| All | Snyk | `snyk test` |

**Common Vulnerabilities (CWE Top 25):**
| **CWE ID** | **Vulnerability** | **Fix** |
|-----------|-------------------|---------|
| 89 | SQL Injection | Parameterized queries |
| 79 | XSS | Escape outputs, CSP |
| 78 | OS Command Injection | Allowlists, avoid `exec` |
| 20 | Improper Input Validation | Validate all inputs |
| 125 | Out-of-bounds Read | Bounds checking |
| 416 | Use After Free | Set to `nullptr` after free |
| 311 | Missing Encryption | Use TLS |
| 259 | Hard-coded Password | Env vars/secrets manager |
| 476 | NULL Pointer Dereference | Null checks |
| 414 | Integer Overflow | SafeMath, checked ops |
| 502 | Deserialization | Signed data, avoid `pickle` |

### **4.4 Phase 3: Dynamic Analysis (DAST)**

#### **Fuzz Testing:**
```bash
# AFL (C)
afl-gcc -o app app.c
afl-fuzz -i inputs/ -o outputs/ ./app @@

# cargo-fuzz (Rust)
cargo fuzz run my_fuzzer

# go-fuzz (Go)
go-fuzz -bin=app.zip -workdir=workdir
```

#### **Memory Safety:**
```bash
# ASAN + UBSAN (C/C++/Rust)
gcc -fsanitize=address,undefined -g -o app app.c
./app

# Valgrind
valgrind --leak-check=full --show-leak-kinds=all ./app

# Rust
RUSTFLAGS="-Z sanitizer=address" cargo run
```

#### **Web App Testing:**
```bash
# OWASP ZAP
zap-baseline.py -t https://your-app.com -r report.html

# sqlmap
sqlmap -u "https://example.com/login?user=1&pass=test" --batch --dbs

# Nikto
nikto -h https://your-app.com

# Nuclei
nuclei -u https://your-app.com -t ~/nuclei-templates/
```

#### **Network Testing:**
```bash
# nmap
nmap -sV -sC -p- -T4 target.com

# masscan
masscan -p1-65535 target.com --rate=1000

# tcpdump
tcpdump -i eth0 -w capture.pcap
```

### **4.5 Phase 4: Reverse Engineering**

**Tools:**
| **Purpose** | **Tool** | **Command** |
|-------------|----------|-------------|
| Disassembly | Ghidra | GUI-based |
| Disassembly | radare2 | `r2 -AAA -c 'pdf @ main' ./binary` |
| Disassembly | objdump | `objdump -d ./binary` |
| Debugging | gdb | `gdb ./app` |
| Debugging | lldb | `lldb ./app` |
| Debugging | WinDbg | GUI-based |
| Debugging | delve (Go) | `dlv debug ./app` |

**radare2 Example:**
```bash
# Disassemble main
r2 -AAA -c 'pdf @ main' ./binary

# Search for strings
r2 -AAA -c 'izz' ./binary

# Find dangerous functions
r2 -AAA -c 'afl | grep -E "strcpy|gets|system"' ./binary
```

### **4.6 Phase 5: Exploitation**

**Common Exploits:**
| **Vulnerability** | **Tool/Technique** | **Example** |
|-------------------|--------------------|-------------|
| SQL Injection | `sqlmap` | `sqlmap -u "url?param=1" --batch --dbs` |
| XSS | Manual | `<script>alert(1)</script>` |
| Buffer Overflow | `pwntools` | `pwn cyclic 100` |
| Command Injection | Manual | `; cat /etc/passwd` |
| Directory Traversal | Manual | `../../../../etc/passwd` |
| RCE | `metasploit` | `use exploit/multi/http/struts2_rest_xstream` |
| Deserialization | `ysoserial` | `java -jar ysoserial.jar CommonsCollections1 "cmd"` |

**Buffer Overflow Example (pwntools):**
```python
from pwn import *

# Compile with: gcc -fno-stack-protector -z execstack vuln.c
p = process('./vuln')
offset = 72  # Found via `pwn cyclic -l 0x6161616b`
shellcode = asm(shellcraft.sh())

payload = flat(
    b'A' * offset,
    0xdeadbeef,  # Overwrite EIP
    next(gadgets)  # ROP gadget
)

p.sendline(payload)
p.interactive()
```

### **4.7 Phase 6: Fixing Vulnerabilities**

**Secure Coding Guidelines:**

1. **Input Validation:**
   ```python
   # ❌ Blocklist
   if '<script>' not in user_input: process(user_input)
   
   # ✅ Allowlist
   if re.match(r'^[a-zA-Z0-9_\-]+$', user_input): process(user_input)
   ```

2. **Output Encoding:**
   ```javascript
   // ❌ Direct output
   document.write(userInput);
   
   // ✅ HTML-encoded
   document.write(DOMPurify.sanitize(userInput));
   ```

3. **SQL Injection:**
   ```python
   # ❌ String formatting
   cursor.execute(f"SELECT * FROM users WHERE id={user_id}")
   
   # ✅ Parameterized
   cursor.execute("SELECT * FROM users WHERE id=%s", (user_id,))
   ```

4. **XSS Prevention:**
   ```http
   Content-Security-Policy: default-src 'self'; script-src 'self' https://trusted.cdn.com
   ```

5. **Memory Safety (C):**
   ```c
   // ❌ Unsafe
   strcpy(dest, src);
   
   // ✅ Safe
   strncpy(dest, src, sizeof(dest));
   snprintf(buffer, sizeof(buffer), "Hello, %s", name);
   ```

6. **Secure Defaults:**
   ```javascript
   res.cookie('session', token, {
       httpOnly: true,
       secure: true,
       sameSite: 'strict',
       maxAge: 86400000
   });
   ```

7. **Error Handling:**
   ```python
   # ❌ Leaks stack trace
   try: process()
   except Exception as e: return str(e)
   
   # ✅ Generic error
   try: process()
   except Exception: return "An error occurred"
   ```

**Fixing Specific Vulnerabilities:**
| **Vulnerability** | **❌ Bad** | **✅ Fixed** |
|-------------------|-----------|-------------|
| SQL Injection | `f"SELECT ... {id}"` | `"SELECT ... %s", (id,)` |
| XSS | `innerHTML = input` | `textContent = input` |
| Buffer Overflow | `strcpy(dest, src)` | `strncpy(dest, src, sizeof(dest))` |
| Command Injection | `os.system(f"ls {input}")` | `subprocess.run(['ls', input], shell=False)` |
| Hardcoded Secrets | `API_KEY = "abc123"` | `API_KEY = os.getenv("API_KEY")` |
| Race Condition | `if (check) { act() }` | `mutex.lock(); if (check) { act() }; mutex.unlock()` |
| Deserialization | `pickle.loads(data)` | `json.loads(data)` |
| Path Traversal | `open(f"/data/{input}")` | `open(f"/data/{sanitize(input)}")` |

### **4.8 Phase 7: Verification**
- **Re-run all SAST/DAST scans.**
- **Re-test for the specific vulnerability.**
- **Add regression tests:**
  ```python
  def test_sql_injection_prevented():
      with pytest.raises(ValueError):
          query_user("; DROP TABLE users; --")
  ```

---

## 🛡️ **Rule 5: Clean and Resilient Code (Zero-Tolerance Standards)**

### **5.1 Code Quality Metrics**

| **Metric** | **Max Allowed** | **Tool** |
|------------|-----------------|----------|
| Cyclomatic Complexity | 10 per function | `radon cc` (Python), `gocyclo` (Go) |
| Lines of Code (Function) | 50 | All linters |
| Lines of Code (File) | 500 | All linters |
| Code Duplication | 3 lines (exact match) | `jscpd`, `pmd-cpd` |
| Maintainability Index | > 65 (good), > 85 (excellent) | `radon mi` |

**Cyclomatic Complexity (Python):**
```bash
radon cc your_code.py --fail-under 10
```

**Code Duplication:**
```bash
jscpd --format gitlab --min-tokens 50 src/
```

### **5.2 Code Style Enforcement**

**Minimal Linter Configs:**

**Python (`.pylintrc`):**
```ini
[MESSAGES CONTROL]
disable=C0114,C0115,C0116,W0621
enable=E,F

[FORMAT]
max-line-length=100

[COMPARISON]
allow-chained-comparisons=no
```

**JavaScript (`.eslintrc.js`):**
```javascript
module.exports = {
    extends: ['eslint:recommended', 'plugin:@typescript-eslint/recommended', 'plugin:security/recommended'],
    parser: '@typescript-eslint/parser',
    rules: {
        'indent': ['error', 4],
        'quotes': ['error', 'single'],
        'semi': ['error', 'always'],
        '@typescript-eslint/explicit-module-boundary-types': 'error',
        '@typescript-eslint/no-explicit-any': 'error',
    },
};
```

**Go (`.golangci.yml`):**
```yaml
linters:
    enable:
        - errcheck
        - gosec
        - gocyclo
        - dupl
        - gofmt
        - goimports
        - golint
        - staticcheck

linters-settings:
    gocyclo: { min-complexity: 10 }
    dupl: { threshold: 100 }
    errcheck: { check-type-assertions: true }
```

### **5.3 Code Review Checklist**

#### **Functional Correctness:**
- [ ] Code works as intended
- [ ] All edge cases handled
- [ ] No logical errors
- [ ] Return values correct for all inputs

#### **Security:**
- [ ] All inputs validated/sanitized
- [ ] No hardcoded secrets
- [ ] Sensitive operations (crypto, auth) secure
- [ ] No injection vulnerabilities (SQL, XSS, command)
- [ ] Error messages free of sensitive data

#### **Performance:**
- [ ] No unnecessary computations
- [ ] Appropriate data structures
- [ ] I/O operations batched
- [ ] Memory used efficiently

#### **Readability:**
- [ ] Descriptive names
- [ ] Well-organized (functions, classes, modules)
- [ ] No magic numbers/strings
- [ ] Complex logic explained

#### **Maintainability:**
- [ ] DRY (Don’t Repeat Yourself)
- [ ] Single-purpose functions
- [ ] Dependencies up-to-date and minimal
- [ ] Well-documented

### **5.4 Commenting Guidelines**

#### **✅ What to Comment:**
| **What** | **Example** |
|----------|-------------|
| Why (not what) | `// Use BigInt to avoid overflow for numbers > 2^53` |
| Complex logic | `// Binary search: low <= target < high` |
| Non-obvious decisions | `// Timeout set to 5s to avoid API rate limits` |
| Hacks/workarounds | `// TODO: Remove when library fixes bug #123` |
| Thread safety | `// Not thread-safe; protect with mutex` |
| Performance notes | `// O(n log n) due to sorting; acceptable for n < 10k` |
| Security notes | `// Never log passwords; this is a one-way hash` |

#### **❌ What NOT to Comment:**
- Obvious code (`// Increment x by 1` for `x++`)
- Redundant (`// Return true` for `return true`)
- Outdated (comments describing old behavior)
- Noise (`// --- Start of function ---`)

#### **Language-Specific Comment Styles:**

**C/Java/JavaScript:**
```c
// Single-line

/* Multi-line */

/// JSDoc/TSDoc
/**
 * Adds two numbers.
 * @param {number} a - First number
 * @param {number} b - Second number
 * @returns {number} Sum
 */
function add(a, b) { return a + b; }
```

**Python:**
```python
# Single-line

def add(a, b):
    """Adds two numbers.
    
    Args:
        a (int): First number
        b (int): Second number
        
    Returns:
        int: Sum of a and b
    """
    return a + b
```

### **5.5 Error Handling**

**Best Practices:**
- Be **explicit** (specific error types)
- Provide **context** (relevant details)
- Handle **gracefully** (never crash)
- **Log appropriately** (for debugging)

**Python:**
```python
# ❌ Generic error
try: process()
except Exception as e: log.error(f"Error: {e}")

# ✅ Specific errors
try: process()
except ValueError as e: log.error(f"Invalid input: {e}")
except IOError as e: log.error(f"File error: {e}")
except Exception as e: log.error(f"Unexpected: {e}", exc_info=True); raise
```

**Go:**
```go
// ❌ Ignore error
f, _ := os.Open("file.txt")

// ✅ Handle error
f, err := os.Open("file.txt")
if err != nil {
    return fmt.Errorf("failed to open file: %w", err)
}
defer f.Close()
```

**Rust:**
```rust
// ✅ Result type
fn read_file(path: &str) -> Result<String, std::io::Error> {
    std::fs::read_to_string(path)
}

// 🔥 Custom error enum
#[derive(Debug)]
enum AppError {
    Io(std::io::Error),
    Parse(std::num::ParseIntError),
    NotFound(String),
}
```

### **5.6 Defensive Programming**

**Input Validation (Python with Pydantic):**
```python
from pydantic import BaseModel, constr, EmailStr, Field

class UserInput(BaseModel):
    username: constr(strip_whitespace=True, min_length=1, max_length=50)
    email: EmailStr
    age: Optional[int] = Field(ge=0, le=120)
```

**Assertions (for internal invariants ONLY):**
```python
# ✅ Good: Internal state
def process(data):
    assert len(data) > 0, "Data must not be empty"
    # ...
```

**Immutable Data:**
```python
# ✅ Return new list (immutable)
def add_suffix(items, suffix):
    return [item + suffix for item in items]

# ❌ Modify input (mutable)
def add_suffix(items, suffix):
    for i in range(len(items)): items[i] += suffix
    return items
```

### **5.7 Compilation Resilience**

**Strict Flags by Language:**
| **Language** | **Strict Flags** |
|--------------|------------------|
| C | `-Wall -Wextra -Werror -Wpedantic -std=c11` |
| C++ | `-Wall -Wextra -Werror -Wpedantic -std=c++17` |
| Python | `-W error::DeprecationWarning` (mypy) |
| TypeScript | `tsc --strict --noImplicitAny --noImplicitReturns` |
| Go | `go build -v` |
| Rust | `cargo build --release` (default strict) |
| Java | `-Xlint:all -Werror` |

**Pre-Commit Hooks (`.pre-commit-config.yaml`):**
```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: detect-private-key
  
  - repo: https://github.com/psf/black
    rev: 23.7.0
    hooks: [id: black, language_version: python3]
  
  - repo: https://github.com/PyCQA/flake8
    rev: 6.1.0
    hooks: [id: flake8]
  
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.4.1
    hooks: [id: mypy, args: [--strict, --ignore-missing-imports]]
  
  - repo: https://github.com/pre-commit/mirrors-eslint
    rev: v8.45.0
    hooks: [id: eslint, args: [--fix, --ext, .js,.ts]]
  
  - repo: https://github.com/golangci/golangci-lint
    rev: v1.54.2
    hooks: [id: golangci-lint]
```

---

## 📜 **Protocols**

### **6.1 Git Protocols**

**Branch Naming:**
| **Type** | **Format** | **Example** |
|----------|------------|-------------|
| Feature | `feat/short-description` | `feat/add-user-auth` |
| Bug Fix | `fix/short-description` | `fix/sql-injection-auth` |
| Refactor | `refactor/short-description` | `refactor/db-layer` |
| Docs | `docs/short-description` | `docs/update-readme` |
| Chore | `chore/short-description` | `chore/update-deps` |
| Security | `security/short-description` | `security/patch-xss` |

**Commit Messages:**
```
<type>(<scope>): <subject>

<body>

<footer>
```
**Types:** `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`, `security`

**Example:**
```
fix(security): prevent SQL injection in user lookup

- Added parameterized queries to UserRepository
- Updated tests to cover injection attempts
- Bumped sqlx to v0.7.0

Fixes: #123
Security: CWE-89
```

**PR Protocols:**
- **Title:** Same format as commits
- **Description:** What, why, screenshots, links
- **Reviews:** 2+ approvals (security: security team)
- **Merging:** Squash for small, rebase for features

### **6.2 Documentation Standards**

**README (Minimum Sections):**
1. Title + Badges (CI, coverage, version)
2. Description
3. Installation
4. Usage (code examples)
5. Configuration
6. API Reference
7. Testing
8. Contributing
9. License

**Code Documentation:**
- **Functions:** Purpose, params, returns, side effects
- **Classes:** Purpose, invariants
- **Modules:** Purpose, usage examples

**API Documentation:**
- **REST:** OpenAPI/Swagger
- **JS/TS:** JSDoc/TSDoc
- **Python:** Sphinx

### **6.3 Deployment Protocols**

**Pre-Deployment Checklist:**
- [ ] All tests pass in CI
- [ ] Security scan passed
- [ ] Dependencies patched
- [ ] Performance benchmarks met
- [ ] Docs updated
- [ ] Rollback plan documented
- [ ] Monitoring in place

**Deployment Strategies:**
| **Strategy** | **Use Case** |
|--------------|--------------|
| Rolling Update | Stateless services |
| Blue-Green | Critical apps |
| Canary | High-risk changes |
| Feature Flags | A/B testing |
| Shadow | Testing without impact |

**Post-Deployment:**
- Monitor: Errors, performance, resources
- Verify: Smoke tests, health checks
- Rollback: If issues → roll back immediately

---

## ❌ **Prohibited Actions**

### **7.1 Absolute Prohibitions (NEVER)**
- ❌ `eval()`, `exec()`, dynamic code execution with unsanitized input
- ❌ Ignore compiler warnings or linter errors
- ❌ Commit code with syntax errors or logical flaws
- ❌ Hardcode secrets (API keys, passwords, tokens)
- ❌ Disable security checks (`--no-check`, `#![allow(unsafe_code)]`)
- ❌ Leave dead code or commented-out code
- ❌ Use `any` (TS), `dynamic` (C#), `Object` (Java), `void*` (C)
- ❌ Use `malloc`/`free` in C++ (use `new`/`delete` or smart pointers)
- ❌ Use raw pointers in Rust if safe abstractions exist
- ❌ Log sensitive data (passwords, tokens, PII)
- ❌ Use deprecated APIs or functions

### **7.2 Strongly Discouraged (AVOID)**
- ⚠️ Deep nesting (>3 levels)
- ⚠️ Global variables
- ⚠️ Magic numbers/strings
- ⚠️ Long functions (>50 lines)
- ⚠️ Long files (>500 lines)
- ⚠️ Reinventing the wheel
- ⚠️ Premature optimization
- ⚠️ Copy-paste code

---

## ✅ **Checklists**

### **8.1 Pre-Code Checklist**
- [ ] Analyzed **impact** of change
- [ ] Checked for **existing solutions**
- [ ] Designed **interface**
- [ ] Considered **edge cases**
- [ ] Estimated **performance impact**
- [ ] Checked **security implications**

### **8.2 During Coding**
- [ ] Following **style guide**
- [ ] Writing **clean, readable code**
- [ ] Adding **comments where necessary**
- [ ] Handling **errors properly**
- [ ] Validating **all inputs**
- [ ] Avoiding **anti-patterns**

### **8.3 Pre-Commit**
- [ ] Code **compiles with zero warnings**
- [ ] All **unit tests pass**
- [ ] All **integration tests pass**
- [ ] Ran **static analysis**
- [ ] Tested **edge cases**
- [ ] Updated **documentation**
- [ ] **Self-reviewed** changes

### **8.4 Pre-PR**
- [ ] **Clear PR title/description**
- [ ] Linked to **relevant issues**
- [ ] Added **tests for new functionality**
- [ ] Updated **documentation**
- [ ] **Self-reviewed** code
- [ ] Ran **CI locally**

### **8.5 Security Review**
- [ ] Scanned for **vulnerabilities** (SAST/DAST)
- [ ] Checked for **hardcoded secrets**
- [ ] Validated **all inputs**
- [ ] Sanitized **all outputs**
- [ ] Used **secure defaults**
- [ ] Tested for **injection attacks**
- [ ] Tested for **XSS/CSRF**
- [ ] Tested for **race conditions**

### **8.6 Post-Deployment**
- [ ] **Error rates** normal
- [ ] **Performance metrics** within targets
- [ ] **Health checks** passing
- [ ] Monitored for **regressions**
- [ ] **Documented deployment**

---

## 🔥 **Priority Order (When Rules Conflict)**
1. **🔴 Security** (Critical vulnerabilities **MUST** be fixed immediately)
2. **🟡 Correctness** (Code **MUST** work as intended)
3. **🟢 Optimization** (Code **SHOULD** be fast)
4. **🔵 Readability** (Code **SHOULD** be clean)

---

## 📚 **Glossary**
| **Term** | **Definition** |
|----------|----------------|
| SAST | Static Application Security Testing |
| DAST | Dynamic Application Security Testing |
| Fuzzing | Testing with random inputs |
| ASAN/UBSAN | Memory error detectors |
| CWE | Common Weakness Enumeration |
| OWASP | Open Web Application Security Project |
| CSP | Content Security Policy |
| DRY | Don’t Repeat Yourself |
| SOLID | OOP Design Principles |
| YAGNI | You Aren’t Gonna Need It |
| KISS | Keep It Simple, Stupid |

---

## 📖 **Further Reading**
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [CWE/SANS Top 25](https://cwe.mitre.org/top25/)
- [Google Style Guides](https://google.github.io/styleguide/)
- [SEI CERT Coding Standards](https://wiki.sei.cmu.edu/confluence/display/seccode/SEI+CERT+Coding+Standards)
- [12 Factor App](https://12factor.net/)
- [Clean Code (Book)](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)

---

**Note:** *This is a **living document**. Update it as new tools, languages, or best practices emerge.*
