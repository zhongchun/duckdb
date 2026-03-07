# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

DuckDB is an in-process SQL OLAP database management system. The codebase follows a classic database architecture with distinct layers for parsing, planning, optimization, execution, and storage.

## Essential Commands

### Building
- `make` - Build release version
- `make debug` - Build debug version (required for running tests)
- `make clean` - Clean build artifacts
- `GEN=ninja make` - Faster parallel builds using Ninja
- `CMAKE_BUILD_PARALLEL_LEVEL=4 GEN=ninja make` - Limit parallel processes if system locks up

### Testing
- `make unit` - Run fast unit tests (~1 minute)
- `make allunit` - Run all unit tests (~1 hour)
- `./build/debug/test/unittest "[test-name]"` - Run specific test by name
- Test files use `.test` extension (sqllogictest format) or `.test_slow` for slower tests
- C++ tests use Catch2 framework with `[.]` tag for slow tests

### Formatting
- `make format-fix` - Run clang-format and black to format code
- Uses clang-format 11.0.1
- Tabs for indentation, spaces for alignment, 120 column limit

### Benchmarks
- `BUILD_BENCHMARK=1 BUILD_TPCH=1 make` - Build with benchmarks
- `./build/release/benchmark/benchmark_runner` - Run benchmarks

## Architecture

DuckDB follows a layered architecture with clear separation of concerns:

### Query Processing Pipeline
1. **Parser** (`src/parser/`) - Parses SQL into an Abstract Syntax Tree (AST)
2. **Planner** (`src/planner/`) - Converts AST into a logical query plan
3. **Optimizer** (`src/optimizer/`) - Optimizes the logical plan (rule-based and cost-based)
4. **Execution** (`src/execution/`) - Executes the physical plan using vectorized execution
5. **Storage** (`src/storage/`) - Manages data storage, buffer management, and I/O

### Key Components
- **Catalog** (`src/catalog/`) - Manages database schema (tables, views, functions)
- **Function** (`src/function/`) - Built-in SQL functions and aggregates
- **Transaction** (`src/transaction/`) - MVCC-based transaction management
- **Parallel** (`src/parallel/`) - Parallel query execution infrastructure
- **Common** (`src/common/`) - Shared utilities, types, and data structures
- **Main** (`src/main/`) - Database connection, client context, and API entry points

### Extensions
- `extension/` - Modular extensions for additional functionality
- Extensions can add new functions, table functions, storage backends, etc.

### Testing
- `test/sql/` - SQL-based tests organized by category (`.test` files preferred)
- `test/api/` - API tests for client interfaces
- `test/` - C++ unit tests (use only when sqllogictest is insufficient)

## Development Guidelines

### C++ Coding Standards
- Use `unique_ptr` over `shared_ptr`; avoid raw `new`/`delete`
- Use `const` references for non-trivial objects
- Use `[u]int(8|16|32|64)_t` for integers, `idx_t` for indices/counts
- No namespace imports (`using std`)
- All core functions must be in `duckdb` namespace
- Use `override` or `final` for virtual methods, not `virtual`
- Use C++11 range-based for loops: `for (const auto& item : items)`

### Naming Conventions
- Files: `lowercase_with_underscores.cpp`
- Types/Classes: `CamelCase` (e.g., `BaseColumn`)
- Variables: `lowercase_with_underscores` (e.g., `chunk_size`)
- Functions: `CamelCase` (e.g., `GetChunk`)

### Error Handling
- Use exceptions only for query-terminating errors (parser errors, table not found)
- Use return values for expected errors
- Use `D_ASSERT` for programmer errors (never triggered by user input)
- Add test cases that trigger exceptions

### Testing Best Practices
- **Strongly prefer** sqllogictest (`.test` files) over C++ tests
- Write tests for bug fixes to prevent regression
- Test with different types (numerics, strings, nested types)
- Test error conditions, not just happy paths
- Fast tests in `make unit`, slower tests in `make allunit` (`.test_slow` or `[.]` tag)
- Run `make format-fix` before submitting PRs

## Debugging

### Common Crash Causes
- Null pointer dereferences
- Out of bounds access
- Use after free
- Stack overflow
- Assertion failures

### Debugging Tools
- Use `make debug` for debug builds with symbols
- Enable sanitizers: ASAN, UBSAN (check `.sanitizer-*-suppressions.txt`)
- Use gdb/lldb with breakpoints
- Add debug prints to trace execution

### Component Identification
When debugging, identify which component is failing:
- Parser errors: syntax issues, invalid SQL
- Planner errors: semantic issues, unresolved references
- Optimizer errors: invalid plan transformations
- Executor errors: runtime errors, data type mismatches
- Storage errors: I/O issues, corruption

## Pull Request Workflow

- Do not commit directly to main; use forks and PRs
- Run `make format-fix` before submitting
- Ensure all unit tests pass (`make allunit`)
- Add tests for new features and bug fixes
- Avoid large PRs; split into smaller, reviewable chunks
- PRs move to draft on new commits; mark "ready for review" for full CI run
