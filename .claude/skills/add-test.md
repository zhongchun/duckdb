# Add Test

Help create a new test for DuckDB functionality.

## Instructions

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
