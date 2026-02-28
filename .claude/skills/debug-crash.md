# Debug Crash

Help debug a DuckDB crash or segmentation fault.

## Instructions

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
