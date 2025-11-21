# Scripts and Code

Best practices for including scripts, handling errors, managing dependencies, and documenting configuration in skills.

## Table of Contents

- [When to Include Scripts](#when-to-include-scripts)
- [Error Handling](#error-handling)
- [Dependencies and Packages](#dependencies-and-packages)
- [File Paths and Cross-Platform Compatibility](#file-paths-and-cross-platform-compatibility)
- [Configuration Documentation](#configuration-documentation)
- [Script Execution vs. Reference](#script-execution-vs-reference)
- [Validation Scripts](#validation-scripts)
- [MCP Tools Integration](#mcp-tools-integration)

## When to Include Scripts

Pre-made scripts are more reliable than code generation for critical operations.

### Scripts vs. Generated Code

**Use pre-made scripts for:**

- Critical operations requiring exact sequences
- Complex error handling
- Repeated utility functions
- Fragile operations (database migrations, API calls)
- Operations where order matters

**Use code generation for:**

- Creative solutions
- Context-specific implementations
- One-off operations
- Exploratory tasks

## Error Handling

Scripts should solve problems, not punt to Claude. Provide specific, actionable error messages.

### Good Error Handling

✓ **What to do:**
```python
def process_file(filename):
    """
    Process input file with comprehensive error handling.

    Args:
        filename: Path to the file to process

    Returns:
        Processed data string

    Raises:
        FileNotFoundError: With specific path and suggestions
        PermissionError: With permission requirements
        ValueError: With format expectations
    """
    import os

    # Check file exists
    if not os.path.exists(filename):
        raise FileNotFoundError(
            f"File not found: {filename}\n"
            f"Current directory: {os.getcwd()}\n"
            f"Please verify:\n"
            f"  1. File path is correct\n"
            f"  2. File exists in the expected location\n"
            f"  3. You have permission to access the directory"
        )

    # Check file is readable
    if not os.access(filename, os.R_OK):
        raise PermissionError(
            f"Cannot read file: {filename}\n"
            f"Current user: {os.getenv('USER')}\n"
            f"File permissions: {oct(os.stat(filename).st_mode)[-3:]}\n"
            f"Solution: Run 'chmod +r {filename}' or contact system administrator"
        )

    # Check file is not empty
    if os.path.getsize(filename) == 0:
        raise ValueError(
            f"File is empty: {filename}\n"
            f"Expected: Non-empty CSV file with headers\n"
            f"Please provide a file with data"
        )

    # Check file format
    try:
        with open(filename, 'r') as f:
            first_line = f.readline()
            if not first_line.strip():
                raise ValueError(
                    f"File appears to be empty or invalid: {filename}\n"
                    f"Expected: CSV file with comma-separated headers"
                )

            if ',' not in first_line:
                raise ValueError(
                    f"File does not appear to be CSV format: {filename}\n"
                    f"First line: {first_line[:100]}\n"
                    f"Expected: Comma-separated values\n"
                    f"Detected: {first_line.count('\t')} tabs, {first_line.count('|')} pipes\n"
                    f"Suggestion: Check delimiter or convert to CSV"
                )

            # Process file
            f.seek(0)
            data = f.read()
            return data

    except UnicodeDecodeError as e:
        raise ValueError(
            f"File encoding error: {filename}\n"
            f"Error: {str(e)}\n"
            f"File might be binary or use unsupported encoding\n"
            f"Solution: Convert to UTF-8 encoding or verify file type"
        )
```

## Dependencies and Packages

Document required packages with versions and explain why they're needed.

## File Paths and Cross-Platform Compatibility

Always use forward slashes for file paths to ensure cross-platform compatibility.

### Path Best Practices

**Always use forward slashes:**
```python
# ✓ Correct - works on all platforms
data_file = "data/input/customers.csv"
output_dir = "reports/2024/march"
config_path = "config/settings.json"

# ✗ Wrong - fails on Unix-like systems
data_file = "data\\input\\customers.csv"
output_dir = "reports\\2024\\march"
```

**Use pathlib for path operations:**
```python
from pathlib import Path

# Automatically handles platform differences
data_dir = Path("data") / "input"
output_file = data_dir / "processed_data.csv"

# Always converts to forward slashes for display
print(output_file.as_posix())  # "data/input/processed_data.csv"
```

**Convert user-provided paths:**
```python
def normalize_path(path_str):
    """Convert any path format to forward-slash format."""
    from pathlib import Path
    return Path(path_str).as_posix()

# Example usage
user_path = "data\\input\\file.csv"  # User on Windows
normalized = normalize_path(user_path)  # "data/input/file.csv"
```

## Configuration Documentation

Document why configuration values exist. No "magic numbers."

### Good Configuration

✓ **What to do:**
```python
# Maximum retry attempts for API calls
# Set to 3 because:
#   - API typically recovers within 3 attempts
#   - Higher values cause unacceptable user wait times
#   - Exponential backoff means 3 attempts = ~7 seconds total
MAX_RETRIES = 3

# Request timeout in seconds
# Set to 30 because:
#   - API SLA guarantees response within 20 seconds
#   - 10 second buffer prevents false timeouts
#   - Longer timeouts risk resource exhaustion
TIMEOUT = 30
```

## Script Execution vs. Reference

Clarify whether Claude should execute scripts or use them as reference patterns.

## Validation Scripts

Create validation scripts with explicit, actionable error messages for critical operations.

## MCP Tools Integration

When referencing MCP (Model Context Protocol) tools, always use fully qualified names.

### Fully Qualified Names

MCP tools must be referenced with the server name prefix:

✓ **Correct:**
```markdown
Use the `DatabaseServer:search_database` tool to find records.
Use the `FileServer:list_files` tool to see available files.
```

## Validation Checklist

Scripts and code checklist:

- [ ] Scripts included only for critical/complex/repeated operations
- [ ] Error messages are specific and actionable
- [ ] Error messages include context and solutions
- [ ] All error conditions explicitly handled
- [ ] Dependencies documented with versions and purposes
- [ ] File paths use forward slashes (cross-platform)
- [ ] Configuration values explained (no magic numbers)
- [ ] Clear indication whether to execute or reference scripts
- [ ] Validation scripts provide comprehensive error reports
- [ ] MCP tools referenced with fully qualified names (ServerName:tool_name)
