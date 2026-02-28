# DuckDB Development Workflows

This document contains workflows and guidelines for working with the DuckDB codebase.

## Project Overview

DuckDB is an in-process SQL OLAP database management system. This repository contains the core database engine, tests, and extensions.

## Testing Workflows

### Running Tests

When asked to run a specific DuckDB test:

1. Ask the user which test to run (accept test name or file path)
2. Check if the debug build exists, if not, inform the user they need to run `make debug`
3. Run the test using: `./build/debug/test/unittest "[test-name]"`
4. Analyze the output:
   - Report pass/fail status clearly
   - If failed, extract and show the error details
   - Show the assertion that failed
   - Identify the file and line number of the failure
5. If the test failed, suggest potential causes:
   - Check if it's a known issue
   - Look at recent changes in related files
   - Suggest debugging approaches
6. Provide a concise summary with actionable next steps

### Adding New Tests

When helping create a new test:

1. Ask the user what functionality they want to test
2. Determine the appropriate test location:
   - SQL tests go in `test/sql/[category]/`
   - C++ unit tests go in `test/[category]/`
   - API tests go in `test/api/`
3. Look at similar existing tests for patterns and conventions
4. Create the test file:
   - For SQL tests: Use .test format with proper structure
   - For C++ tests: Use Catch2 framework with proper includes
5. Include:
   - Clear test description
   - Setup code if needed
   - Multiple test cases covering:
     - Normal/happy path
     - Edge cases
     - Error conditions
   - Proper assertions
6. Explain how to run the new test
7. Suggest additional test cases that might be valuable

### Explaining Tests

When asked to explain a test file:

1. Ask the user which test file to explain (or accept it as an argument)
2. Read the test file from the test/ directory
3. Analyze and explain:
   - What functionality is being tested
   - The test structure and organization
   - Key test cases and what they verify
   - Any edge cases or special scenarios covered
   - Dependencies on other components
4. If it's a SQL test (.test file):
   - Explain the SQL queries being tested
   - Show expected results
   - Explain any special test directives
5. If it's a C++ test (.cpp file):
   - Explain the test fixtures and setup
   - Describe each TEST_CASE
   - Show the assertions being made
6. Provide insights on:
   - Why this test is important
   - What bugs it might catch
   - Related tests the user might want to look at

## Debugging Workflows

### Debugging Crashes

When helping debug a DuckDB crash or segmentation fault:

1. Ask the user for crash details:
   - Error message or stack trace
   - Steps to reproduce
   - Which test or query caused it
2. Analyze the crash:
   - Identify the failing component (parser, planner, executor, storage)
   - Look at the stack trace to find the crash location
   - Read the relevant source files
3. Check common crash causes:
   - Null pointer dereferences
   - Out of bounds access
   - Use after free
   - Stack overflow
   - Assertion failures
4. Investigate the code path:
   - Trace the execution flow
   - Check for missing null checks
   - Look for memory management issues
   - Verify data structure invariants
5. Suggest debugging approaches:
   - Add debug prints
   - Use gdb/lldb with specific breakpoints
   - Enable sanitizers (ASAN, UBSAN)
   - Simplify the reproducer
6. Provide a fix or workaround if possible
7. Recommend adding a test case to prevent regression

## Code Navigation

### Finding Functions

When asked to locate and explain a function:

1. Ask the user which function they want to find (SQL function or C++ function)
2. Search for the function:
   - For SQL functions: Check `src/function/` and `extension/core_functions/`
   - For C++ functions: Use grep to search across the codebase
   - Check both declarations (headers) and implementations
3. Show the function location:
   - File path and line number
   - Full function signature
   - Brief code snippet
4. Explain the function:
   - What it does
   - Parameters and return type
   - Key implementation details
   - Any special cases or edge conditions
5. Show usage examples:
   - How to call it from SQL (if applicable)
   - How it's used in the codebase
   - Related functions
6. Point to relevant tests that exercise this function
7. If the function is complex, offer to explain specific parts in detail

## Build Management

### Build Checking

When checking build status or helping fix build errors:

1. Check the current build status:
   - Look for existing build directories
   - Check if builds are up to date
2. If the user reports a build error:
   - Ask for the full error message
   - Identify the error type (compilation, linking, CMake)
3. Analyze the error:
   - For compilation errors:
     - Show the problematic code
     - Explain what's wrong
     - Suggest fixes
   - For linking errors:
     - Identify missing symbols
     - Check library dependencies
   - For CMake errors:
     - Check CMakeLists.txt files
     - Verify dependencies are installed
4. Common build issues to check:
   - Missing dependencies
   - Compiler version compatibility
   - Incorrect CMake flags
   - Stale build artifacts
5. Suggest solutions:
   - Clean build: `make clean`
   - Rebuild: `make debug` or `make release`
   - Update dependencies
   - Fix the code issue
6. After fixing, verify the build succeeds
7. Recommend running tests to ensure nothing broke

## General Guidelines

- Always check if debug builds exist before running tests
- Use `./build/debug/test/unittest "[test-name]"` for running specific tests
- Look at existing code patterns before adding new code
- Add tests for any bug fixes to prevent regression
- Use sanitizers (ASAN, UBSAN) when debugging memory issues
