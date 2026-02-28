# Find Function

Locate and explain a function implementation in DuckDB.

## Instructions

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
