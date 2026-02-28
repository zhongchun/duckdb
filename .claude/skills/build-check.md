# Build Check

Check the build status and help fix build errors.

## Instructions

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
