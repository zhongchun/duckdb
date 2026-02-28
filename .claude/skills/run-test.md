# Run DuckDB Test

Run a specific DuckDB test and analyze the results.

## Instructions

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
