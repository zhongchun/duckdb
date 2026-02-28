# Explain Test

Explain what a DuckDB test file does and how it works.

## Instructions

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
