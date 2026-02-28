# DuckDB Main Entry Points

## Overview

DuckDB has multiple entry points depending on how it's being used. The main entry points are:

1. **CLI Application** - Command-line interface for interactive SQL
2. **Unit Test Runner** - Test execution framework
3. **Benchmark Runner** - Performance benchmarking tool
4. **Library API** - Embedded usage in applications

## 1. CLI Application (Shell)

**Location**: `tools/shell/shell.cpp:4838`

```cpp
int SQLITE_CDECL main(int argc, char **argv) {
```

This is the main entry point for the DuckDB command-line interface (CLI). The shell is based on SQLite's shell implementation but adapted for DuckDB.

### Key Features:
- Interactive SQL REPL (Read-Eval-Print Loop)
- Batch mode for script execution
- Command-line argument processing
- History support (.duckdb_history)
- Configuration file support (.duckdbrc)
- Multiple output formats (CSV, JSON, box, table, etc.)

### Main Flow:
1. **Initialization** (line 4870): `main_init(&data)` - Initialize shell state
2. **Argument Parsing** (lines 4900-5160): Process command-line options
   - `-c COMMAND` - Execute command and exit
   - `-f FILENAME` - Execute SQL file
   - `-cmd COMMAND` - Run command before stdin
   - `-init FILENAME` - Load initialization file
   - Various output format options
3. **Interactive Mode** (lines 5193-5232):
   - Display welcome message with version info
   - Load command history
   - Process user input with readline/linenoise
   - Save command history on exit
4. **Batch Mode** (lines 5233-5236): Process stdin non-interactively
5. **Cleanup** (lines 5238-5250): Close database and free resources

### Command-Line Options:
- `-ascii`, `-box`, `-column`, `-csv`, `-json`, `-markdown`, `-table` - Output formats
- `-bail` - Stop on error
- `-batch` - Force batch I/O
- `-echo` - Print commands before execution
- `-header` / `-noheader` - Toggle column headers
- `-init FILENAME` - Initialization file
- `-readonly` - Open database in read-only mode
- `-safe` - Enable safe mode (restricted operations)

## 2. Unit Test Runner

**Location**: `test/unittest.cpp:12`

```cpp
int main(int argc_in, char *argv[]) {
```

This is the entry point for DuckDB's unit test suite using the Catch2 testing framework.

### Key Features:
- Runs C++ unit tests
- Test directory management
- Test filtering and selection
- Temporary directory creation/cleanup

### Main Flow:
1. **Initialize** (lines 13-17): Create file system and get test configuration
2. **Parse Arguments** (lines 19-41):
   - `--test-dir` - Set test directory
   - `--test-temp-dir` - Set temporary test directory
   - `--require` - Add test requirements
   - Pass remaining args to Catch2
3. **Setup Test Environment** (lines 44-49): Create/clean test directories
4. **Run Tests** (line 56): Execute Catch2 test runner
5. **Cleanup** (line 58): Remove test directories

### Usage:
```bash
./build/debug/test/unittest [test-name] [options]
./build/debug/test/unittest --test-dir=/path/to/tests
./build/debug/test/unittest "SELECT tests" --require=parquet
```

## 3. Benchmark Runner

**Location**: `benchmark/benchmark_runner.cpp`

The benchmark runner uses Catch2's `CATCH_CONFIG_RUNNER` and provides a main function for running performance benchmarks.

### Key Features:
- TPC-H benchmark support
- TPC-DS benchmark support
- Custom benchmark registration
- Performance profiling
- Result comparison

### Benchmark Types:
- **TPC-H**: Standard OLAP benchmark (data/benchmark/tpch/)
- **TPC-DS**: Decision support benchmark
- **Custom**: User-defined benchmarks

### Usage:
```bash
./build/release/benchmark/benchmark_runner [benchmark-name]
```

## 4. Library API (Embedded Usage)

**Location**: `examples/embedded-c++/main.cpp:5`

```cpp
int main() {
    DuckDB db(nullptr);
    Connection con(db);
    con.Query("CREATE TABLE integers(i INTEGER)");
    con.Query("INSERT INTO integers VALUES (3)");
    auto result = con.Query("SELECT * FROM integers");
    result->Print();
}
```

This demonstrates how to embed DuckDB in a C++ application. The library provides a clean API without requiring a main function.

### Core API Classes:
- **DuckDB**: Database instance
- **Connection**: Database connection
- **QueryResult**: Query execution results
- **Appender**: Bulk data insertion
- **PreparedStatement**: Parameterized queries

### API Entry Points:
- `src/main/connection.cpp` - Connection management
- `src/main/database.cpp` - Database initialization
- `src/main/query_result.cpp` - Result handling
- `src/main/appender.cpp` - Data appending

## Build Targets

The main executables are built as:

1. **duckdb** (CLI): `build/release/duckdb` or `build/debug/duckdb`
2. **unittest**: `build/debug/test/unittest`
3. **benchmark_runner**: `build/release/benchmark/benchmark_runner`

### Build Commands:
```bash
# Build CLI
make

# Build debug version with tests
make debug

# Build and run unit tests
make unit

# Build benchmarks
BUILD_BENCHMARK=1 make
```

## Entry Point Summary

| Entry Point | File | Line | Purpose |
|------------|------|------|---------|
| CLI Shell | `tools/shell/shell.cpp` | 4838 | Interactive SQL interface |
| Unit Tests | `test/unittest.cpp` | 12 | Test execution |
| Benchmarks | `benchmark/benchmark_runner.cpp` | ~20 | Performance testing |
| Library API | `src/main/*.cpp` | N/A | Embedded usage |

## How Query Execution Starts

Regardless of the entry point, SQL query execution follows this path:

1. **Entry Point** → Parse command/query
2. **Connection::Query()** → `src/main/connection.cpp`
3. **Parser** → `src/parser/` - Parse SQL into AST
4. **Planner** → `src/planner/` - Create logical plan
5. **Optimizer** → `src/optimizer/` - Optimize plan
6. **Executor** → `src/execution/` - Execute physical plan
7. **Results** → Return to caller

The CLI shell (tools/shell/shell.cpp) is the most common entry point for end users, while the library API is used for embedding DuckDB in applications.
