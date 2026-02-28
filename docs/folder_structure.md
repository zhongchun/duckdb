# DuckDB Folder Structure

## Overview

DuckDB follows a well-organized folder structure that separates core functionality, extensions, tests, benchmarks, and tooling. This document provides a comprehensive guide to understanding the repository layout.

## Top-Level Directories

```
duckdb/
├── benchmark/          # Performance benchmarks
├── build/              # Build output (generated)
├── data/               # Test data and sample datasets
├── docs/               # Documentation files
├── examples/           # Example code for embedding DuckDB
├── extension/          # Modular extensions
├── logo/               # DuckDB logos and branding
├── scripts/            # Build and automation scripts
├── src/                # Core source code
├── test/               # Test suite
├── third_party/        # Third-party dependencies
└── tools/              # CLI and development tools
```

## Core Source Code (`src/`)

The `src/` directory contains the core database engine implementation.

### Main Components

```
src/
├── catalog/            # Database catalog management
│   ├── catalog_entry/  # Catalog entry types (tables, views, etc.)
│   └── default/        # Default catalog implementation
│
├── common/             # Shared utilities and data structures
│   ├── adbc/           # Arrow Database Connectivity
│   ├── arrow/          # Apache Arrow integration
│   ├── crypto/         # Cryptographic functions
│   ├── enums/          # Enumeration types
│   ├── exception/      # Exception handling
│   ├── operator/       # Common operators
│   ├── serializer/     # Serialization/deserialization
│   ├── types/          # Type system
│   ├── value_operations/   # Value-level operations
│   └── vector_operations/  # Vector-level operations
│
├── execution/          # Query execution engine
│   ├── expression_executor/  # Expression evaluation
│   ├── index/          # Index operations
│   ├── operator/       # Physical operators (scan, join, aggregate, etc.)
│   ├── physical_plan/  # Physical plan creation
│   └── sample/         # Sampling operations
│
├── function/           # Function implementations
│   ├── aggregate/      # Aggregate functions (SUM, AVG, etc.)
│   ├── cast/           # Type casting functions
│   ├── pragma/         # PRAGMA commands
│   ├── scalar/         # Scalar functions
│   ├── table/          # Table functions
│   └── window/         # Window functions
│
├── include/            # Public header files
│   └── duckdb/         # DuckDB API headers
│
├── logging/            # Logging infrastructure
│
├── main/               # Main entry points and APIs
│   ├── buffered_data/  # Buffered data handling
│   ├── capi/           # C API implementation
│   ├── extension/      # Extension loading
│   ├── http/           # HTTP client
│   ├── relation/       # Relation API
│   ├── secret/         # Secret management
│   └── settings/       # Configuration settings
│
├── optimizer/          # Query optimizer
│   ├── compressed_materialization/  # Compression optimization
│   ├── join_order/     # Join order optimization
│   ├── matcher/        # Pattern matching
│   ├── pushdown/       # Filter/projection pushdown
│   ├── rule/           # Optimization rules
│   └── statistics/     # Statistics-based optimization
│
├── parallel/           # Parallel execution
│
├── parser/             # SQL parser
│   ├── constraints/    # Constraint parsing
│   ├── expression/     # Expression parsing
│   ├── parsed_data/    # Parsed data structures
│   ├── query_node/     # Query tree nodes
│   ├── statement/      # SQL statement types
│   ├── tableref/       # Table reference parsing
│   └── transform/      # AST transformation
│
├── planner/            # Query planner
│   ├── binder/         # Symbol binding
│   ├── expression/     # Expression planning
│   ├── expression_binder/  # Expression binding
│   ├── filter/         # Filter planning
│   ├── operator/       # Logical operators
│   └── subquery/       # Subquery planning
│
├── storage/            # Storage engine
│   ├── buffer/         # Buffer management
│   ├── checkpoint/     # Checkpointing
│   ├── compression/    # Data compression
│   ├── metadata/       # Metadata management
│   ├── serialization/  # Storage serialization
│   ├── statistics/     # Storage statistics
│   └── table/          # Table storage
│
├── transaction/        # Transaction management
│
└── verification/       # Query verification
```

### Key Source Files

- `src/main/connection.cpp` - Database connection implementation
- `src/main/database.cpp` - Database instance management
- `src/main/query_result.cpp` - Query result handling
- `src/parser/parser.cpp` - SQL parser entry point
- `src/planner/binder/binder.cpp` - Symbol binding
- `src/optimizer/optimizer.cpp` - Query optimization
- `src/execution/executor.cpp` - Query execution

## Test Suite (`test/`)

Comprehensive test coverage organized by functionality.

```
test/
├── api/                # API tests
│   ├── capi/           # C API tests
│   ├── adbc/           # ADBC tests
│   └── udf_function/   # User-defined function tests
│
├── sql/                # SQL functionality tests
│   ├── aggregate/      # Aggregate function tests
│   ├── join/           # Join operation tests
│   ├── select/         # SELECT query tests
│   ├── insert/         # INSERT tests
│   ├── update/         # UPDATE tests
│   ├── delete/         # DELETE tests
│   ├── function/       # Function tests
│   ├── types/          # Data type tests
│   ├── window/         # Window function tests
│   └── ... (60+ categories)
│
├── fuzzer/             # Fuzz testing
│   ├── sqlsmith/       # SQLsmith fuzzer
│   ├── duckfuzz/       # DuckDB fuzzer
│   └── pedro/          # Pedro fuzzer
│
├── optimizer/          # Optimizer tests
├── planner/            # Planner tests
├── storage/            # Storage tests
├── parquet/            # Parquet format tests
├── issues/             # Regression tests for reported issues
│   ├── general/        # General issues
│   ├── fuzz/           # Fuzzer-found issues
│   └── rigger/         # Rigger-found issues
│
└── unittest.cpp        # Main test runner
```

### Test File Format

- `.test` files - SQL test scripts with expected results
- `.cpp` files - C++ unit tests using Catch2

## Extensions (`extension/`)

Modular extensions that can be loaded dynamically.

```
extension/
├── autocomplete/       # Shell autocomplete
├── core_functions/     # Core SQL functions
│   ├── aggregate/      # Aggregate functions
│   └── scalar/         # Scalar functions
├── delta/              # Delta Lake support
├── demo_capi/          # C API demo extension
├── icu/                # Unicode support (ICU)
├── jemalloc/           # jemalloc memory allocator
├── json/               # JSON processing
├── parquet/            # Parquet file format
│   ├── decoder/        # Parquet decoding
│   ├── reader/         # Parquet reading
│   └── writer/         # Parquet writing
├── tpcds/              # TPC-DS benchmark
└── tpch/               # TPC-H benchmark
```

### Extension Structure

Each extension typically contains:
- `include/` - Header files
- `src/` or implementation files - Source code
- `CMakeLists.txt` - Build configuration

## Benchmarks (`benchmark/`)

Performance benchmarking suite.

```
benchmark/
├── micro/              # Micro-benchmarks
│   ├── aggregate/      # Aggregation benchmarks
│   ├── join/           # Join benchmarks
│   ├── filter/         # Filter benchmarks
│   └── ... (30+ categories)
│
├── tpch/               # TPC-H benchmark
│   ├── sf1/            # Scale factor 1
│   ├── aggregate/      # Aggregation queries
│   └── join/           # Join queries
│
├── tpcds/              # TPC-DS benchmark
├── clickbench/         # ClickBench benchmark
├── h2oai/              # H2O.ai benchmark
├── imdb/               # IMDB benchmark
├── ldbc/               # LDBC benchmark
├── csv/                # CSV benchmarks
├── parquet/            # Parquet benchmarks
└── benchmark_runner.cpp  # Main benchmark runner
```

## Tools (`tools/`)

Development tools and utilities.

```
tools/
├── shell/              # Command-line interface (CLI)
│   ├── shell.cpp       # Main shell implementation
│   ├── shell_renderer.cpp  # Output rendering
│   ├── shell_highlight.cpp # Syntax highlighting
│   └── linenoise/      # Line editing library
│
├── sqlite3_api_wrapper/  # SQLite compatibility layer
├── juliapkg/           # Julia package tools
├── swift/              # Swift bindings
└── utils/              # Utility tools
```

## Third-Party Dependencies (`third_party/`)

External libraries integrated into DuckDB.

```
third_party/
├── libpg_query/        # PostgreSQL parser
├── re2/                # Regular expressions
├── fmt/                # String formatting
├── mbedtls/            # Cryptography
├── catch/              # Testing framework
├── parquet/            # Parquet library
├── yyjson/             # JSON parser
├── zstd/               # Compression
├── lz4/                # Compression
├── snappy/             # Compression
├── brotli/             # Compression
├── fsst/               # String compression
├── hyperloglog/        # Approximate counting
├── fast_float/         # Float parsing
├── concurrentqueue/    # Lock-free queue
└── ... (20+ libraries)
```

## Scripts (`scripts/`)

Build automation and development scripts.

```
scripts/
├── amalgamation.py     # Create single-file distribution
├── apply_extension_patches.py  # Extension patching
├── asset-upload.py     # Release asset upload
├── check_coverage.py   # Code coverage checking
├── clang-tidy-diff.py  # Static analysis
├── create_patch.py     # Patch creation
└── ... (80+ scripts)
```

## Examples (`examples/`)

Example code demonstrating DuckDB usage.

```
examples/
├── embedded-c/         # C embedding example
├── embedded-c++/       # C++ embedding example
├── embedded-c++-windows/  # Windows C++ example
├── python/             # Python examples
└── standalone-window/  # Standalone window example
```

## Data (`data/`)

Test data and sample datasets.

```
data/
├── csv/                # CSV test files
├── parquet/            # Parquet test files
├── json/               # JSON test files
└── ... (various test datasets)
```

## Build Output (`build/`)

Generated during compilation (not in version control).

```
build/
├── release/            # Release build
│   ├── duckdb          # CLI executable
│   ├── libduckdb.so    # Shared library
│   └── test/           # Test executables
│
└── debug/              # Debug build
    ├── duckdb          # CLI executable (debug)
    ├── libduckdb.so    # Shared library (debug)
    └── test/           # Test executables (debug)
```

## Configuration Files

Root directory configuration files:

- `CMakeLists.txt` - Main CMake configuration
- `Makefile` - Build wrapper
- `.clang-format` - Code formatting rules
- `.clang-tidy` - Static analysis configuration
- `.gitignore` - Git ignore rules
- `Doxyfile` - Doxygen documentation config
- `CONTRIBUTING.md` - Contribution guidelines
- `LICENSE` - MIT License
- `README.md` - Project overview

## Navigation Tips

### Finding Functionality

1. **SQL Features**: Look in `test/sql/[feature]/` for examples
2. **Functions**: Check `src/function/` or `extension/core_functions/`
3. **Operators**: See `src/execution/operator/`
4. **Parser**: Start at `src/parser/`
5. **Optimizer**: Explore `src/optimizer/rule/`
6. **Storage**: Check `src/storage/`

### Adding New Features

1. **New Function**: Add to `src/function/` or `extension/core_functions/`
2. **New Operator**: Add to `src/execution/operator/`
3. **New Extension**: Create in `extension/[name]/`
4. **New Test**: Add to `test/sql/[category]/` or `test/api/`
5. **New Benchmark**: Add to `benchmark/micro/` or `benchmark/[suite]/`

## Summary

The DuckDB folder structure is organized around:

- **Core Engine** (`src/`) - Database implementation
- **Extensions** (`extension/`) - Modular functionality
- **Tests** (`test/`) - Comprehensive test coverage
- **Benchmarks** (`benchmark/`) - Performance testing
- **Tools** (`tools/`) - CLI and utilities
- **Dependencies** (`third_party/`) - External libraries
- **Scripts** (`scripts/`) - Automation
- **Examples** (`examples/`) - Usage demonstrations

This structure promotes modularity, testability, and maintainability while keeping related functionality together.
