# DuckDB Technology Stack

## Core Technologies

### Programming Languages

**C++11** - Primary implementation language
- Modern C++ features with C++11 standard
- Strict standard compliance (no compiler extensions)
- Position-independent code for library usage

**Python 3** - Scripting and tooling
- Build scripts and automation
- Testing utilities
- Release management tools
- Extension management

### Build System

**CMake** (3.5-3.29)
- Cross-platform build configuration
- Modular extension system
- Unity builds support
- Compiler caching (ccache/sccache)

**GNU Make**
- Convenient build wrapper
- Simplified commands for common tasks

## Third-Party Libraries

### Core Dependencies

**libpg_query** - PostgreSQL SQL parser
- Provides SQL parsing capabilities
- Ensures PostgreSQL compatibility

**re2** - Regular expression engine
- Fast, safe regular expression matching
- Used for pattern matching operations

**fmt** - String formatting library
- Modern C++ string formatting
- Type-safe printf alternative

### Compression Libraries

- **zstd** - Zstandard compression
- **lz4** - Fast compression algorithm
- **snappy** - Fast compression/decompression
- **brotli** - Compression algorithm
- **miniz** - Minimal zip library
- **fsst** - Fast static symbol table compression

### Data Format Support

**Parquet** - Apache Parquet format
- Columnar storage format
- Efficient analytics queries

**Thrift** - Apache Thrift
- Used for Parquet metadata

**yyjson** - JSON parser
- High-performance JSON parsing
- Used for JSON extension

### Cryptography & Security

**mbedtls** - Cryptographic library
- TLS/SSL support
- Encryption capabilities

### Algorithms & Data Structures

**hyperloglog** - Approximate counting
- Cardinality estimation
- Memory-efficient distinct counting

**fast_float** - Fast floating-point parsing
- High-performance number parsing
- Standards-compliant

**concurrentqueue** - Lock-free queue
- Thread-safe concurrent operations
- High-performance parallel processing

**pdqsort** - Pattern-defeating quicksort
- Fast sorting algorithm
- Hybrid sorting approach

**ska_sort** - Ska sort algorithm
- Radix-based sorting
- Optimized for specific data types

**skiplist** - Skip list data structure
- Probabilistic data structure
- Efficient search operations

**vergesort** - Adaptive sorting
- Optimized for partially sorted data

### Text Processing

**utf8proc** - Unicode processing
- UTF-8 string handling
- Unicode normalization

**snowball** - Stemming algorithms
- Text search and analysis
- Multiple language support

**jaro_winkler** - String similarity
- Fuzzy string matching
- Distance calculations

### Networking

**httplib** - HTTP library
- HTTP client/server functionality
- Used for remote file access

### Benchmarking

**TPC-H** - Transaction Processing Performance Council benchmark
- Standard OLAP benchmark
- Data generation tools (dbgen)

**TPC-DS** - Decision Support benchmark
- Complex analytics benchmark
- Data generation tools (dsdgen)

**TPC-E** - OLTP benchmark
- Transaction processing benchmark

**IMDB** - Internet Movie Database benchmark
- Real-world dataset for testing

## Testing Framework

**Catch2** - C++ testing framework
- Unit testing
- BDD-style test cases
- Header-only library

**SQL Test Files** (.test format)
- Custom SQL testing format
- Declarative test specifications
- Regression testing

## Code Quality Tools

### Formatting & Linting

**clang-format** - Code formatter
- LLVM-based style
- Consistent code formatting
- Tab width: 4, Column limit: 120

**clang-tidy** - Static analyzer
- C++ code analysis
- Bug detection
- Best practices enforcement

### Build Optimization

**ccache** - Compiler cache
- Speeds up recompilation
- Caches compilation results

**sccache** - Shared compilation cache
- Alternative to ccache
- Distributed caching support

## Language Bindings & Clients

### Official Bindings

**Python** - Python client
- Native Python API
- Pandas integration
- NumPy support

**R** - R client
- dplyr integration
- Native R data frames

**Java** - JDBC driver
- Standard JDBC interface
- Java applications support

**Julia** - Julia package
- Native Julia integration
- DataFrames.jl support

**Swift** - Swift bindings
- iOS/macOS support
- Native Swift API

**WebAssembly (Wasm)** - Browser support
- In-browser analytics
- JavaScript integration

### API Wrappers

**SQLite3 API wrapper**
- SQLite compatibility layer
- Drop-in replacement capability

**C API** - Native C interface
- Low-level access
- Embedding support

## Development Tools

### Version Control

**Git** - Source control
- GitHub hosting
- Pull request workflow

### Documentation

**Doxygen** - API documentation
- C++ code documentation
- HTML/PDF output

**Markdown** - Documentation format
- README files
- Contributing guides

### CI/CD

**GitHub Actions** - Continuous integration
- Automated testing
- Multi-platform builds
- Release automation

### Scripts & Automation

**Python scripts** for:
- Amalgamation (single-file distribution)
- Extension management
- Release management
- Coverage checking
- Asset uploading
- Patch creation

**Shell scripts** for:
- Grammar building
- Extension hashing
- Coverage checking
- Extension uploading

## Platform Support

### Operating Systems
- Linux (primary development platform)
- macOS (Darwin)
- Windows (MSVC, MinGW)
- WebAssembly

### Compilers
- GCC (GNU Compiler Collection)
- Clang/LLVM
- MSVC (Microsoft Visual C++)

### Architectures
- x86_64 (primary)
- ARM64
- WebAssembly

## Extension System

DuckDB uses a modular extension architecture:

**Core Extensions** (built-in):
- core_functions - Essential SQL functions
- autocomplete - Shell autocomplete
- icu - International Components for Unicode
- json - JSON processing
- parquet - Parquet file format
- delta - Delta Lake support
- jemalloc - Memory allocator

**Extension Distribution**:
- CMake-based build system
- Loadable shared libraries
- Remote extension loading
- Version compatibility checking

## Summary

DuckDB leverages a modern C++ technology stack with:
- High-performance C++11 core
- Extensive third-party library ecosystem
- Comprehensive testing infrastructure
- Multi-language client support
- Cross-platform compatibility
- Modular extension architecture

The project emphasizes performance, correctness, and ease of use while maintaining a clean, well-tested codebase.
