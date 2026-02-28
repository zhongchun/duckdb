# DuckDB Codebase Structure

## What is DuckDB?

DuckDB is a high-performance analytical database system designed to be fast, reliable, portable, and easy to use. It's an in-process SQL OLAP database management system (similar to SQLite but for analytics). It supports a rich SQL dialect with advanced features like nested correlated subqueries, window functions, complex types (arrays, structs, maps), and more.

## Core Architecture

The codebase follows a classic database architecture with these main components:

### 1. Parser (`src/parser/`)
- Entry point for all queries
- Uses PostgreSQL's libpg_query parser
- Transforms SQL into a custom parse tree with SQLStatements, Expressions, and TableRefs

### 2. Planner (`src/planner/`)
- Converts parse tree into a Logical Query Plan
- Represented as a tree of LogicalOperator nodes
- Binds symbols to actual database objects using the Catalog

### 3. Optimizer (`src/optimizer/`)
- Transforms logical plans into faster equivalent plans
- Performs both rule-based and cost-based optimizations
- Includes predicate pushdown, expression rewriting, join ordering

### 4. Execution (`src/execution/`)
- Converts Logical Query Plan to Physical Query Plan
- Uses push-based execution model with PhysicalOperators
- Contains operators for joins, aggregates, scans, filters, etc.

### 5. Catalog (`src/catalog/`)
- Manages database metadata (tables, schemas, functions)
- Used during planning to resolve symbols

### 6. Storage (`src/storage/`)
- Manages physical data in memory and on disk
- Handles base table scans and data modifications

### 7. Transaction (`src/transaction/`)
- Manages concurrent transactions
- Handles COMMIT/ROLLBACK operations

## Additional Components

- **Common** (`src/common/`): Shared utilities, types, operators, serialization
- **Function** (`src/function/`): Built-in functions (aggregate, scalar, table, window)
- **Main** (`src/main/`): Database connection, client API, HTTP support
- **Parallel** (`src/parallel/`): Parallel execution support
- **Verification** (`src/verification/`): Query verification and testing

## Extensions

DuckDB has a modular extension system (`extension/`):
- **core_functions**: Core SQL functions
- **parquet**: Parquet file format support
- **json**: JSON processing
- **icu**: International Components for Unicode
- **delta**: Delta Lake support
- **tpch/tpcds**: Benchmark data generators

## Testing

Comprehensive test suite (`test/`):
- SQL tests for various features
- API tests (C, C++, Python, etc.)
- Fuzzer tests
- Performance benchmarks

## Build System

- Uses CMake with a Makefile wrapper
- `make` - build release version
- `make debug` - build debug version
- `make unit` - run unit tests
- `make format-fix` - format code

## Query Execution Flow

The typical query execution flow follows this path:

```
SQL Query → Parser → Planner → Optimizer → Execution → Results
                        ↓           ↓
                    Catalog     Storage
```

1. **Parse**: SQL string is parsed into a parse tree
2. **Plan**: Parse tree is converted to a logical query plan
3. **Optimize**: Logical plan is optimized for performance
4. **Execute**: Optimized plan is converted to physical operators and executed
5. **Results**: Data is retrieved from storage and returned to the user

The codebase is well-organized following database system principles, making it relatively easy to navigate once you understand the query execution flow.
