# Learning DuckDB Internals

A practical guide to understanding how DuckDB works under the hood.

## Overview

DuckDB follows a classic database architecture with distinct layers for parsing, planning, optimization, execution, and storage. This guide provides a hands-on learning path to understand the internals.

## Query Processing Pipeline

Every SQL query flows through these stages:

```
SQL String → Parser → Planner → Optimizer → Executor → Results
```

### 1. Parser: SQL → Abstract Syntax Tree (AST)
- **Location:** `src/parser/`
- **Input:** SQL string (e.g., "SELECT 42")
- **Output:** AST (tree of statement and expression objects)
- **Key files:**
  - `src/parser/parser.cpp` - Main parser entry point
  - `src/parser/parsed_data/` - Parsed statement structures
  - `src/parser/expression/` - Expression parsing

### 2. Planner/Binder: AST → Logical Plan
- **Location:** `src/planner/`
- **Input:** AST
- **Output:** Logical operator tree
- **Key files:**
  - `src/planner/binder/binder.cpp` - Binds parsed statements
  - `src/planner/binder/query_node/bind_select_node.cpp` - SELECT binding
  - `src/planner/logical_operator.cpp` - Base logical operator

### 3. Optimizer: Logical Plan → Optimized Plan
- **Location:** `src/optimizer/`
- **Input:** Logical plan
- **Output:** Optimized logical plan
- **Key files:**
  - `src/optimizer/optimizer.cpp` - Main optimizer
  - `src/optimizer/rule/` - Optimization rules
  - `src/optimizer/expression_rewriter.cpp` - Expression optimization

### 4. Physical Planner: Logical Plan → Physical Plan
- **Location:** `src/execution/`
- **Input:** Optimized logical plan
- **Output:** Physical operator tree
- **Key file:** `src/execution/physical_plan_generator.cpp`

### 5. Executor: Physical Plan → Results
- **Location:** `src/execution/`
- **Input:** Physical plan
- **Output:** Query results (DataChunks)
- **Key files:**
  - `src/execution/executor.cpp` - Main executor
  - `src/execution/physical_operator.cpp` - Base physical operator
  - `src/execution/operator/` - Specific operators

## Entry Point

All queries enter through `ClientContext`:

**File:** `src/main/client_context.cpp`

```cpp
// Line 969: Main query entry point
unique_ptr<QueryResult> ClientContext::Query(const string &query) {
    // 1. Parse SQL → AST
    statements = ParseStatements(*lock, query);

    // 2. Plan AST → Logical Plan
    // 3. Optimize Logical Plan
    // 4. Execute Physical Plan
    // 5. Return Results
}
```

**Key function:** `ClientContext::Query()` at line 969

## Core Components

### Storage Layer
**Location:** `src/storage/`
- `buffer_manager.cpp` - Memory management and buffer pool
- `data_table.cpp` - Table storage implementation
- `table/` - Table structures and row groups
- Implements columnar storage with compression

### Type System
**Location:** `src/common/types/`
- `value.cpp` - Single scalar values
- `vector.cpp` - Columnar vectors (arrays of values)
- `data_chunk.cpp` - Chunks of vectors (batch of rows)
- **Key concept:** Vectorized execution operates on DataChunks

### Catalog
**Location:** `src/catalog/`
- Manages database schema (tables, views, functions, indexes)
- `catalog.cpp` - Main catalog implementation
- `catalog_entry/` - Different types of catalog entries

### Function System
**Location:** `src/function/`
- `scalar_function.cpp` - Scalar functions (e.g., UPPER, ABS)
- `aggregate_function.cpp` - Aggregate functions (e.g., SUM, AVG)
- `table_function.cpp` - Table-producing functions (e.g., read_csv)
- Functions are registered in the catalog

### Transaction Management
**Location:** `src/transaction/`
- Implements MVCC (Multi-Version Concurrency Control)
- `transaction_manager.cpp` - Transaction lifecycle
- `transaction_context.cpp` - Per-transaction state

### Parallel Execution
**Location:** `src/parallel/`
- `pipeline.cpp` - Query execution pipelines
- `task_scheduler.cpp` - Task scheduling
- Implements intra-query parallelism

## Learning Path

### Phase 1: Trace a Simple Query (Week 1)

**Goal:** Understand the complete flow from SQL to results

**Exercise: Trace "SELECT 42"**

1. **Using the debugger (recommended):**
```bash
lldb ./build/debug/duckdb

# Set breakpoints
(lldb) b ClientContext::Query
(lldb) b Parser::ParseQuery
(lldb) b Binder::Bind
(lldb) b Optimizer::Optimize
(lldb) b Executor::Execute

# Run and execute query
(lldb) r
> SELECT 42;

# Step through each stage
(lldb) step
(lldb) print query
(lldb) continue
```

2. **Reading order:**
   - `src/main/client_context.cpp:969` - Entry point
   - `src/parser/parser.cpp` - Parsing
   - `src/planner/binder/binder.cpp` - Planning
   - `src/optimizer/optimizer.cpp` - Optimization
   - `src/execution/executor.cpp` - Execution

3. **What to observe:**
   - How "SELECT 42" becomes a `SelectStatement`
   - How it becomes a `LogicalProjection` with a `ConstantExpression`
   - How optimization simplifies it
   - How execution produces a `DataChunk` with value 42

### Phase 2: Understand Vectorization (Week 2)

**Goal:** Understand how DuckDB processes data in batches

**Key concepts:**
- **Vector:** Columnar array of values (one column)
- **DataChunk:** Collection of vectors (multiple columns, batch of rows)
- **Default chunk size:** 2048 rows

**Files to read:**
- `src/common/types/vector.cpp`
- `src/common/types/data_chunk.cpp`
- `src/execution/operator/projection/physical_projection.cpp`

**Exercise:**
1. Find how `PhysicalProjection::Execute()` processes chunks
2. See how it operates on entire vectors at once
3. Compare to row-by-row processing

### Phase 3: Explore a Feature (Week 3-4)

**Goal:** Deep dive into a specific SQL feature

**Pick a feature:**
- Joins (`src/execution/operator/join/`)
- Aggregates (`src/execution/operator/aggregate/`)
- Window functions (`src/execution/operator/window/`)
- Sorting (`src/execution/operator/order/`)

**Process:**
1. Find tests in `test/sql/[feature]/`
2. Read test files to understand behavior
3. Trace implementation through the pipeline
4. Understand the physical operator implementation

**Example: Understanding JOINs**
1. Read `test/sql/join/inner/test_join.test`
2. Find `PhysicalHashJoin` in `src/execution/operator/join/`
3. Understand hash table building and probing
4. See how it handles different join types

### Phase 4: Storage and Transactions (Week 5-6)

**Goal:** Understand data persistence and MVCC

**Topics:**
- How tables are stored on disk
- Buffer manager and caching
- Transaction isolation
- MVCC implementation

**Files to read:**
- `src/storage/data_table.cpp`
- `src/storage/buffer_manager.cpp`
- `src/transaction/transaction_manager.cpp`

## Practical Exercises

### Exercise 1: Add a Simple Scalar Function

Create a function `double(x)` that returns `x * 2`:

1. Look at existing functions in `src/function/scalar/math/`
2. Create your function following the pattern
3. Register it in the catalog
4. Test with: `SELECT double(21);` → should return 42

### Exercise 2: Understand a Test Failure

1. Pick a test from `test/sql/`
2. Modify it to make it fail
3. Run it and analyze the error
4. Trace through the code to understand why it failed

### Exercise 3: Profile a Query

```sql
PRAGMA enable_profiling;
PRAGMA profiling_output='profile.json';

SELECT * FROM large_table WHERE condition;
```

Analyze the profile output to understand:
- Which operators took the most time
- How many rows flowed through each operator
- Where optimization opportunities exist

### Exercise 4: Read Operator Implementation

Pick an operator and understand it completely:
1. Find its logical operator (e.g., `LogicalFilter`)
2. Find its physical operator (e.g., `PhysicalFilter`)
3. Understand how it processes DataChunks
4. Find tests that exercise it
5. Trace a query through it with the debugger

## Test File Format

DuckDB uses `.test` files (sqllogictest format):

```sql
# Comment

statement ok
CREATE TABLE t (a INTEGER, b VARCHAR);

statement ok
INSERT INTO t VALUES (1, 'hello'), (2, 'world');

query IT
SELECT * FROM t ORDER BY a;
----
1	hello
2	world

statement error
SELECT * FROM nonexistent_table;
```

**Format:**
- `statement ok` - Statement should succeed
- `statement error` - Statement should fail (optionally with error message)
- `query [types]` - Query with expected results
  - `I` = Integer, `R` = Real, `T` = Text
- `----` - Separator before expected results
- Results are tab-separated

**Location:** `test/sql/[category]/`

## Debugging Tips

### Add Debug Prints

```cpp
#include <iostream>

std::cout << "DEBUG: " << logical_plan->ToString() << std::endl;
std::cout << "DEBUG: Vector size: " << vector.GetSize() << std::endl;
```

### Use Assertions

```cpp
D_ASSERT(chunk.size() > 0);  // Debug builds only
D_ASSERT(vector.GetType() == LogicalType::INTEGER);
```

### Enable Profiling

```sql
PRAGMA enable_profiling;
PRAGMA profiling_mode='detailed';
SELECT ...;
```

### Use EXPLAIN

```sql
EXPLAIN SELECT * FROM t WHERE a > 10;
EXPLAIN ANALYZE SELECT * FROM t WHERE a > 10;
```

### Common Debugging Scenarios

**Crash/Segfault:**
1. Run with ASAN: `BUILD_SANITIZER=address make debug`
2. Use debugger to get stack trace
3. Check for null pointer dereferences
4. Verify data structure invariants

**Wrong Results:**
1. Enable verification: `PRAGMA enable_verification;`
2. Add debug prints to trace data flow
3. Check operator implementations
4. Verify type conversions

**Performance Issues:**
1. Use profiling to identify bottlenecks
2. Check if vectorization is working
3. Look for unnecessary copies
4. Verify indexes are being used

## Key Concepts

### 1. Vectorized Execution
- Operates on batches (vectors) of ~2048 rows
- Reduces function call overhead
- Enables SIMD optimizations
- Better cache locality

### 2. Columnar Storage
- Data stored by column, not row
- Better compression
- Efficient for analytical queries
- Skip irrelevant columns

### 3. MVCC (Multi-Version Concurrency Control)
- Multiple versions of rows exist
- Readers don't block writers
- Writers don't block readers
- Snapshot isolation

### 4. Push-Based Execution
- Operators push data to next operator
- Enables pipelining
- Reduces materialization
- Better memory efficiency

### 5. Operator Trees
- Queries represented as trees of operators
- Logical operators (what to do)
- Physical operators (how to do it)
- Tree transformations for optimization

## Architecture Patterns

### Visitor Pattern
Used extensively for traversing operator and expression trees:
- `LogicalOperatorVisitor`
- `ExpressionVisitor`

### Factory Pattern
Used for creating operators and functions:
- `PhysicalPlanGenerator` creates physical operators
- Function registration in catalog

### Template Method Pattern
Base classes define execution flow:
- `PhysicalOperator::Execute()`
- Subclasses implement specific logic

## Resources

### Documentation
- [Official DuckDB docs](https://duckdb.org/docs/)
- [Build guide](https://duckdb.org/docs/dev/building/overview)
- [Testing guide](https://duckdb.org/dev/testing)

### Code Exploration
- Test files in `test/sql/` - Excellent examples
- Extension code in `extension/` - Self-contained features
- Benchmark code in `benchmark/` - Performance testing

### Community
- [Discord](https://discord.gg/tcvwpjfnZx) - Ask questions
- [GitHub Issues](https://github.com/duckdb/duckdb/issues) - Bug reports and discussions
- [Blog](https://duckdb.org/news/) - Deep dives into features

## Next Steps

1. **Start simple:** Trace "SELECT 42" with the debugger
2. **Build understanding:** Read the key files in order
3. **Go deep:** Pick a feature and understand it completely
4. **Contribute:** Fix a bug or add a small feature
5. **Share:** Help others learn what you've discovered

Remember: The best way to learn is by doing. Set breakpoints, add debug prints, modify code, and see what happens!
