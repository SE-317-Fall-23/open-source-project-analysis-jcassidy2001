# Assignment Submission

### Jack Cassidy

## Project Selected: PyTest

## I. Introduction
- I choose to look at PyTest which makes it easy to write test cases for python and figure how they work  


## II. Test Case 1: [test_module_not_found]
### A. Description
- This test module is trying to verify that pytest responds appropriately when a required Python module ('foo') is not found during test execution. It expects pytest to report an error with a specific error message indicating a 'ModuleNotFoundError.'
### B. Gherkin Syntax (if applicable)
- It doesn't use Gherkin Syntax 
### C. Test Steps
1. Setup
2. Test Initialization
3. Test Execution
4. Validation
5. Error Message Validation
### D. Code Segments Under Test

```python
def test_module_not_found(pytester: Pytester, file_structure) -> None:
    """Without the pythonpath setting, the module should not be found."""
    pytester.makefile(".ini", pytest="[pytest]\n")
    result = pytester.runpytest("test_foo.py")
    assert result.ret == pytest.ExitCode.INTERRUPTED
    result.assert_outcomes(errors=1)
    expected_error = "E   ModuleNotFoundError: No module named 'foo'"
    result.stdout.fnmatch_lines([expected_error])
```
### E. Initial State

-  The test is set up using the 'pytester' fixture and a test configuration file
-  The 'file_structure' parameter is assumed, which may define the expected directory structure
-  The python module 'foo' is not present in the expected location
-  The pytest framework is configured but not explicitly provided with the Python path for the 'foo'
### F. Transition
- The test script executes pytest using the pytest.runpytest("test_foo.py") command
- During the pytest execution, pytest attempts to import the 'foo' module, but it is not found in the Python path
### G. Expected State
- The result.ret should be equal to pytest.ExitCode.INTERRUPTED, indicating that the test execution was interrupted or failed due to the absence of the 'foo' module
- The test should report one error in the test outcomes, as specified by result.assert_outcomes(errors=1)
- The test output should contain the expected error message : "EModuleNotFoundError: No module named 'Foo'"

## III. Test Case 2: [test_fail_and_continue_with_stepwise]
### A. Description
This test case is focused on testing the behavior of pytest when using the stepwise mode to run tests. It specifically tests the ability of pytest to stop testing after a test fails and continue testing after the failure has been addressed. This is a more advanced test case used for checking pytest's stepwise execution feature.
### B. Gherkin Syntax (if applicable)
No Gherkin Syntax
### C. Test Steps
1. Run Tests with a Failing Second Test
2. Assertion
3. Check test output
4. "Fix" the failing test
5. Re-run tests
6. Assertion
7. Check Test Output
### D. Code Segments Under Test
- Identify specific code segments or functions that are being tested in this case.
```python
def test_fail_and_continue_with_stepwise(stepwise_pytester: Pytester) -> None:
    # Run the tests with a failing second test.
    result = stepwise_pytester.runpytest(
        "-v", "--strict-markers", "--stepwise", "--fail"
    )
    assert _strip_resource_warnings(result.stderr.lines) == []

    stdout = result.stdout.str()
    # Make sure we stop after first failing test.
    assert "test_success_before_fail PASSED" in stdout
    assert "test_fail_on_flag FAILED" in stdout
    assert "test_success_after_fail" not in stdout

    # "Fix" the test that failed in the last run and run it again.
    result = stepwise_pytester.runpytest("-v", "--strict-markers", "--stepwise")
    assert _strip_resource_warnings(result.stderr.lines) == []

    stdout = result.stdout.str()
    # Make sure the latest failing test runs and then continues.
    assert "test_success_before_fail" not in stdout
    assert "test_fail_on_flag PASSED" in stdout
    assert "test_success_after_fail PASSED" in stdout
```
### E. Initial State
The initial state includes a pytest environment configured with stepwise mode, strict marker handling, and a simulated failing test. The stepwise_pytester fixture is used to run pytest with these settings.
### F. Transition
The transition occurs when pytest is executed with specific command line options that simulate a failing test and then rerun after addressing the failure
### G. Expected State
The expected state or outcome after the test steps have been completed includes:

- The first pytest run should stop after the first failing test.
- The second pytest run should continue after the previously failing test has been fixed.
- The output and test result should align with these expectations, demonstrating the correct behavior of pytest's stepwise mode.


