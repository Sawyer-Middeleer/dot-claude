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

### Script Inclusion Decision Tree

```markdown
Should you include a script?

1. Is the operation critical? (data loss risk, production impact)
   → YES: Include script
   → NO: Go to 2

2. Is the operation complex with many edge cases?
   → YES: Include script
   → NO: Go to 3

3. Will this operation be repeated frequently?
   → YES: Include script
   → NO: Provide guidance, let Claude generate

4. Does the operation require exact ordering of steps?
   → YES: Include script
   → NO: Provide guidance, let Claude generate
```

### Example: When Scripts Are Needed

**Good use of pre-made script:**
```python
# Database migration script (exact sequence required)
def migrate_user_table():
    """
    Migrates user table from v1 to v2 schema.
    MUST execute steps in exact order.
    """
    try:
        # Step 1: Backup (REQUIRED first)
        backup_table('users', 'users_backup_v1')

        # Step 2: Add new column with default
        execute_sql("ALTER TABLE users ADD COLUMN status VARCHAR(20) DEFAULT 'active'")

        # Step 3: Populate based on business logic
        execute_sql("""
            UPDATE users
            SET status = CASE
                WHEN last_login > NOW() - INTERVAL '30 days' THEN 'active'
                WHEN last_login IS NULL THEN 'pending'
                ELSE 'inactive'
            END
        """)

        # Step 4: Add constraint (only after population)
        execute_sql("ALTER TABLE users ALTER COLUMN status SET NOT NULL")

        # Step 5: Verify
        result = execute_sql("SELECT COUNT(*) FROM users WHERE status IS NULL")
        if result[0][0] > 0:
            raise Exception("Migration validation failed: null values found")

        return {"success": True, "message": "Migration completed"}

    except Exception as e:
        # Rollback on any error
        rollback_from_backup('users', 'users_backup_v1')
        return {"success": False, "error": str(e)}
```

**Bad use of pre-made script (guidance is better):**
```python
# Don't include this as a script - provide guidance instead
def analyze_user_behavior(data):
    """
    This is too flexible for a fixed script.
    Better to provide analytical guidelines.
    """
    # Analysis approach varies by context
    # Claude should adapt based on specific questions
    pass
```

## Error Handling

Scripts should solve problems, not punt to Claude. Provide specific, actionable error messages.

### Poor Error Handling

❌ **What not to do:**
```python
def process_file(filename):
    try:
        with open(filename) as f:
            data = f.read()
        return data
    except Exception as e:
        print(f"Error: {e}")
        return None
```

**Problems:**
- Generic error message
- No guidance on resolution
- No context about what went wrong
- User doesn't know what to do next

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

### Error Handling Principles

**1. Be specific about what went wrong**
```python
# Bad
raise Exception("Invalid data")

# Good
raise ValueError(
    f"Invalid date format in row {row_num}, column 'order_date': '{value}'\n"
    f"Expected: YYYY-MM-DD (e.g., 2024-03-15)\n"
    f"Received: {value}"
)
```

**2. Provide context**
```python
# Bad
if missing_fields:
    raise Exception("Missing fields")

# Good
if missing_fields:
    raise ValueError(
        f"Missing required fields: {', '.join(missing_fields)}\n"
        f"Required fields: {', '.join(REQUIRED_FIELDS)}\n"
        f"Present fields: {', '.join(data.keys())}\n"
        f"Rows affected: {len([r for r in data if any(f not in r for f in missing_fields)])}"
    )
```

**3. Suggest solutions**
```python
# Bad
raise Exception("API call failed")

# Good
raise ConnectionError(
    f"API call failed after {retry_count} attempts\n"
    f"Endpoint: {endpoint}\n"
    f"Status code: {response.status_code}\n"
    f"Error message: {response.text}\n"
    f"\n"
    f"Possible solutions:\n"
    f"  1. Check API endpoint is correct: {endpoint}\n"
    f"  2. Verify API key is valid and not expired\n"
    f"  3. Check rate limits (current: {rate_limit_remaining} remaining)\n"
    f"  4. Review API documentation: {docs_url}\n"
    f"  5. Contact API support if issue persists"
)
```

**4. Handle known error conditions explicitly**
```python
def validate_data(df):
    """Validate DataFrame with specific error handling for each condition."""

    errors = []

    # Check 1: Required columns
    required_cols = ['id', 'name', 'email', 'date']
    missing_cols = [col for col in required_cols if col not in df.columns]
    if missing_cols:
        errors.append(
            f"Missing required columns: {', '.join(missing_cols)}\n"
            f"Present columns: {', '.join(df.columns)}\n"
            f"→ Add missing columns to your data source"
        )

    # Check 2: Data types
    if 'email' in df.columns and df['email'].dtype != 'object':
        errors.append(
            f"Column 'email' has wrong data type: {df['email'].dtype}\n"
            f"Expected: string (object)\n"
            f"→ Convert email column to string type"
        )

    # Check 3: Null values
    null_counts = df[required_cols].isnull().sum()
    cols_with_nulls = null_counts[null_counts > 0]
    if not cols_with_nulls.empty:
        for col, count in cols_with_nulls.items():
            errors.append(
                f"Column '{col}' has {count} null values ({count/len(df)*100:.1f}%)\n"
                f"Affected rows: {df[df[col].isnull()].index.tolist()[:10]}\n"
                f"→ Fill missing values or remove affected rows"
            )

    # Check 4: Email format
    if 'email' in df.columns:
        invalid_emails = df[~df['email'].str.contains('@', na=False)]
        if not invalid_emails.empty:
            errors.append(
                f"Found {len(invalid_emails)} invalid email addresses\n"
                f"Examples: {invalid_emails['email'].head().tolist()}\n"
                f"Rows: {invalid_emails.index.tolist()[:10]}\n"
                f"→ Correct email format or remove invalid rows"
            )

    # Return all errors together
    if errors:
        raise ValueError(
            "Data validation failed:\n\n" +
            "\n\n".join(f"{i+1}. {err}" for i, err in enumerate(errors))
        )

    return True
```

## Dependencies and Packages

Document required packages with versions and explain why they're needed.

### Documenting Dependencies

```markdown
## Required Dependencies

This skill requires the following Python packages:

| Package | Version | Purpose |
|---------|---------|---------|
| pandas | ≥1.5.0 | Data manipulation and DataFrame operations |
| openpyxl | ≥3.0.0 | Reading/writing Excel files (.xlsx format) |
| requests | ≥2.28.0 | HTTP requests to external APIs |
| python-dateutil | ≥2.8.0 | Flexible date parsing and timezone handling |

**Installation:**
\`\`\`bash
pip install pandas>=1.5.0 openpyxl>=3.0.0 requests>=2.28.0 python-dateutil>=2.8.0
\`\`\`

**Verification:**
Check if packages are available in Claude's execution environment:
\`\`\`python
import pandas as pd
import openpyxl
import requests
from dateutil import parser

print(f"pandas: {pd.__version__}")
print(f"openpyxl: {openpyxl.__version__}")
print(f"requests: {requests.__version__}")
\`\`\`

**Note:** If packages are not available, operations will fail with import errors.
```

### Checking Package Availability

```python
def check_dependencies():
    """
    Verify all required packages are available.

    Returns detailed information about missing packages.
    """
    required_packages = {
        'pandas': '1.5.0',
        'openpyxl': '3.0.0',
        'requests': '2.28.0',
    }

    missing = []
    version_issues = []

    for package, min_version in required_packages.items():
        try:
            mod = __import__(package)
            installed_version = getattr(mod, '__version__', 'unknown')

            if installed_version != 'unknown':
                from packaging import version
                if version.parse(installed_version) < version.parse(min_version):
                    version_issues.append(
                        f"{package}: installed {installed_version}, need >={min_version}"
                    )
        except ImportError:
            missing.append(package)

    if missing or version_issues:
        error_msg = "Dependency issues detected:\n\n"

        if missing:
            error_msg += f"Missing packages:\n"
            for pkg in missing:
                error_msg += f"  - {pkg}\n"

        if version_issues:
            error_msg += f"\nVersion issues:\n"
            for issue in version_issues:
                error_msg += f"  - {issue}\n"

        error_msg += f"\nInstall command:\n"
        error_msg += f"pip install " + " ".join(
            f"{pkg}>={ver}" for pkg, ver in required_packages.items()
        )

        raise ImportError(error_msg)

    return True
```

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

### Complete Path Handling Example

```python
from pathlib import Path

def process_files(input_dir, output_dir):
    """
    Process all CSV files from input directory to output directory.

    Args:
        input_dir: Input directory path (any format)
        output_dir: Output directory path (any format)

    Both paths are automatically normalized for cross-platform compatibility.
    """

    # Convert to Path objects (handles any format)
    input_path = Path(input_dir)
    output_path = Path(output_dir)

    # Verify input exists
    if not input_path.exists():
        raise FileNotFoundError(
            f"Input directory not found: {input_path.as_posix()}\n"
            f"Current working directory: {Path.cwd().as_posix()}\n"
            f"Please verify the path is correct"
        )

    # Create output directory if needed
    output_path.mkdir(parents=True, exist_ok=True)

    # Find all CSV files
    csv_files = list(input_path.glob("*.csv"))

    if not csv_files:
        print(f"No CSV files found in {input_path.as_posix()}")
        return

    # Process each file
    for input_file in csv_files:
        output_file = output_path / f"processed_{input_file.name}"

        print(f"Processing: {input_file.as_posix()}")
        print(f"Output to: {output_file.as_posix()}")

        # ... processing logic ...

    print(f"\nProcessed {len(csv_files)} files")
    print(f"Results saved to: {output_path.as_posix()}")
```

## Configuration Documentation

Document why configuration values exist. No "magic numbers."

### Poor Configuration

❌ **What not to do:**
```python
MAX_RETRIES = 3
TIMEOUT = 30
BATCH_SIZE = 1000
CACHE_TTL = 3600
```

**Problems:**
- No explanation of values
- No guidance on changing them
- No context about constraints

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

# Records processed per batch
# Set to 1000 because:
#   - Balances memory usage (~50MB per batch) vs. performance
#   - Database supports up to 5000 row inserts, we use 1000 for safety margin
#   - Allows progress reporting every ~2 seconds
#   - Reduce to 100 for memory-constrained environments
BATCH_SIZE = 1000

# Cache time-to-live in seconds
# Set to 3600 (1 hour) because:
#   - Data freshness requirement: acceptable up to 1 hour stale
#   - Reduces API calls by ~90% based on usage patterns
#   - Memory impact: ~500MB for full cache (acceptable)
#   - Set to 300 (5 minutes) for time-sensitive data
CACHE_TTL = 3600

# Date format for all output files
# Using ISO 8601 format because:
#   - Sortable and unambiguous
#   - Widely supported by data tools
#   - No timezone confusion (always use UTC)
DATE_FORMAT = "%Y-%m-%d %H:%M:%S"
```

### Configuration with Validation

```python
class Config:
    """
    Application configuration with validation.

    All values are documented with rationale and constraints.
    """

    def __init__(self):
        # API Configuration
        self.max_retries = 3  # Retry attempts (1-5 reasonable range)
        self.timeout = 30  # Timeout in seconds (10-60 reasonable range)
        self.rate_limit = 100  # Requests per minute (API limit: 100)

        # Processing Configuration
        self.batch_size = 1000  # Records per batch (100-5000 range)
        self.max_workers = 4  # Parallel workers (1-8 reasonable range)

        # Validate configuration
        self._validate()

    def _validate(self):
        """Validate configuration values with helpful error messages."""

        if not 1 <= self.max_retries <= 5:
            raise ValueError(
                f"max_retries must be between 1 and 5, got {self.max_retries}\n"
                f"Reason: Higher values cause unacceptable wait times"
            )

        if not 10 <= self.timeout <= 60:
            raise ValueError(
                f"timeout must be between 10 and 60 seconds, got {self.timeout}\n"
                f"Reason: API SLA is 20 seconds, use 30 for safety margin"
            )

        if not 100 <= self.batch_size <= 5000:
            raise ValueError(
                f"batch_size must be between 100 and 5000, got {self.batch_size}\n"
                f"Reason: Database limit is 5000, lower values reduce memory"
            )

        if self.rate_limit > 100:
            raise ValueError(
                f"rate_limit cannot exceed 100 req/min (API limit), got {self.rate_limit}"
            )
```

## Script Execution vs. Reference

Clarify whether Claude should execute scripts or use them as reference patterns.

### Execute Scripts (Low Freedom)

```markdown
## Data Validation Script

**Execute this script directly** to validate the dataset:

\`\`\`python
import pandas as pd

# Claude should RUN this exact script
def validate_dataset(filepath):
    """
    Validates dataset against requirements.
    DO NOT modify - execute as-is.
    """
    df = pd.read_csv(filepath)

    errors = []

    # Check 1: Required columns
    required = ['id', 'name', 'email', 'created_date']
    missing = [col for col in required if col not in df.columns]
    if missing:
        errors.append(f"Missing columns: {missing}")

    # Check 2: No nulls in required fields
    if 'id' in df.columns:
        null_count = df['id'].isnull().sum()
        if null_count > 0:
            errors.append(f"Null IDs: {null_count} rows")

    # Check 3: Valid email format
    if 'email' in df.columns:
        invalid = (~df['email'].str.contains('@', na=False)).sum()
        if invalid > 0:
            errors.append(f"Invalid emails: {invalid} rows")

    if errors:
        return {"valid": False, "errors": errors}
    return {"valid": True, "message": "All checks passed"}

# Execute
result = validate_dataset("data.csv")
print(result)
\`\`\`
```

### Reference Patterns (Medium Freedom)

```markdown
## Data Validation Pattern

**Use this as a reference pattern** - adapt as needed for your specific use case:

\`\`\`python
# Claude should READ and ADAPT this pattern
def validate_dataset(df, requirements):
    """
    Example validation pattern.
    Modify based on your specific requirements.
    """

    errors = []

    # Pattern: Check required columns
    missing_cols = [
        col for col in requirements['columns']
        if col not in df.columns
    ]
    if missing_cols:
        errors.append(f"Missing: {missing_cols}")

    # Pattern: Check data types
    for col, expected_type in requirements['types'].items():
        if col in df.columns and df[col].dtype != expected_type:
            errors.append(f"{col}: expected {expected_type}, got {df[col].dtype}")

    # Pattern: Check value constraints
    for col, constraints in requirements['constraints'].items():
        if 'min' in constraints:
            violations = (df[col] < constraints['min']).sum()
            if violations > 0:
                errors.append(f"{col}: {violations} values below minimum")

    return errors

# Adapt this pattern to your needs
# For example, add domain-specific validation,
# change error formatting, add logging, etc.
\`\`\`
```

## Validation Scripts

Create validation scripts with explicit, actionable error messages for critical operations.

### Example: Comprehensive Validation Script

```python
def validate_financial_data(df):
    """
    Comprehensive validation for financial transaction data.

    This script MUST be executed before processing financial data.
    DO NOT skip validation steps.

    Args:
        df: pandas DataFrame with transaction data

    Returns:
        dict with keys:
            - valid (bool): True if all checks pass
            - errors (list): List of error messages
            - warnings (list): List of warning messages
            - summary (dict): Validation statistics
    """
    import pandas as pd
    from datetime import datetime

    errors = []
    warnings = []

    # Critical Check 1: Required columns exist
    required_columns = [
        'transaction_id', 'date', 'amount', 'currency',
        'account_from', 'account_to', 'status'
    ]
    missing_columns = [col for col in required_columns if col not in df.columns]
    if missing_columns:
        errors.append({
            'type': 'CRITICAL',
            'message': f"Missing required columns: {', '.join(missing_columns)}",
            'solution': f"Add columns: {', '.join(missing_columns)} to your data source",
            'affected': 'ALL_ROWS'
        })
        # Cannot continue without required columns
        return {
            'valid': False,
            'errors': errors,
            'warnings': warnings,
            'summary': {'total_rows': len(df), 'checks_completed': 1}
        }

    # Critical Check 2: No null values in critical fields
    critical_fields = ['transaction_id', 'amount', 'currency']
    for field in critical_fields:
        null_count = df[field].isnull().sum()
        if null_count > 0:
            null_rows = df[df[field].isnull()].index.tolist()[:10]
            errors.append({
                'type': 'CRITICAL',
                'message': f"Field '{field}' has {null_count} null values ({null_count/len(df)*100:.1f}%)",
                'solution': f"Remove rows with null {field} or fill with appropriate values",
                'affected': f"Rows: {null_rows}{'...' if null_count > 10 else ''}"
            })

    # Critical Check 3: Transaction IDs are unique
    duplicate_ids = df[df.duplicated(subset=['transaction_id'], keep=False)]
    if not duplicate_ids.empty:
        dupes = duplicate_ids.groupby('transaction_id').size()
        errors.append({
            'type': 'CRITICAL',
            'message': f"Found {len(dupes)} duplicate transaction IDs",
            'solution': "Remove duplicates or investigate data source integrity",
            'affected': f"Transaction IDs: {dupes.head().index.tolist()}"
        })

    # Critical Check 4: Amounts are positive numbers
    if 'amount' in df.columns:
        negative_amounts = df[df['amount'] < 0]
        if not negative_amounts.empty:
            errors.append({
                'type': 'CRITICAL',
                'message': f"Found {len(negative_amounts)} negative amounts",
                'solution': "Fix negative amounts or use absolute values",
                'affected': f"Rows: {negative_amounts.index.tolist()[:10]}"
            })

        zero_amounts = df[df['amount'] == 0]
        if not zero_amounts.empty:
            warnings.append({
                'type': 'WARNING',
                'message': f"Found {len(zero_amounts)} zero-amount transactions",
                'suggestion': "Review if zero-amount transactions are valid",
                'affected': f"Rows: {zero_amounts.index.tolist()[:10]}"
            })

    # Check 5: Valid currency codes
    valid_currencies = ['USD', 'EUR', 'GBP', 'JPY', 'AUD', 'CAD', 'CHF']
    if 'currency' in df.columns:
        invalid_currencies = df[~df['currency'].isin(valid_currencies)]
        if not invalid_currencies.empty:
            unique_invalid = invalid_currencies['currency'].unique()
            errors.append({
                'type': 'ERROR',
                'message': f"Found {len(invalid_currencies)} rows with invalid currency codes",
                'solution': f"Use valid currency codes: {', '.join(valid_currencies)}",
                'affected': f"Invalid codes: {unique_invalid.tolist()}, Rows: {invalid_currencies.index.tolist()[:10]}"
            })

    # Check 6: Valid date format
    if 'date' in df.columns:
        try:
            df['date_parsed'] = pd.to_datetime(df['date'])
            # Check for future dates
            future_dates = df[df['date_parsed'] > pd.Timestamp.now()]
            if not future_dates.empty:
                warnings.append({
                    'type': 'WARNING',
                    'message': f"Found {len(future_dates)} transactions with future dates",
                    'suggestion': "Verify dates are correct",
                    'affected': f"Rows: {future_dates.index.tolist()[:10]}"
                })
        except Exception as e:
            errors.append({
                'type': 'ERROR',
                'message': f"Date parsing failed: {str(e)}",
                'solution': "Ensure dates are in format YYYY-MM-DD or standard datetime format",
                'affected': "ALL_ROWS"
            })

    # Summary statistics
    summary = {
        'total_rows': len(df),
        'valid_rows': len(df) - len(errors),
        'critical_errors': len([e for e in errors if e['type'] == 'CRITICAL']),
        'errors': len([e for e in errors if e['type'] == 'ERROR']),
        'warnings': len(warnings),
        'checks_completed': 6
    }

    # Overall validation result
    valid = len([e for e in errors if e['type'] in ['CRITICAL', 'ERROR']]) == 0

    return {
        'valid': valid,
        'errors': errors,
        'warnings': warnings,
        'summary': summary
    }


# Example usage
if __name__ == "__main__":
    import pandas as pd

    # Load data
    df = pd.read_csv("transactions.csv")

    # Validate
    result = validate_financial_data(df)

    # Print results
    print(f"\n{'='*60}")
    print(f"VALIDATION RESULTS")
    print(f"{'='*60}\n")

    print(f"Total Rows: {result['summary']['total_rows']}")
    print(f"Critical Errors: {result['summary']['critical_errors']}")
    print(f"Errors: {result['summary']['errors']}")
    print(f"Warnings: {result['summary']['warnings']}")
    print(f"\nOverall Status: {'✓ PASSED' if result['valid'] else '✗ FAILED'}\n")

    if result['errors']:
        print(f"{'='*60}")
        print(f"ERRORS FOUND")
        print(f"{'='*60}\n")
        for i, error in enumerate(result['errors'], 1):
            print(f"{i}. [{error['type']}] {error['message']}")
            print(f"   Solution: {error['solution']}")
            print(f"   Affected: {error['affected']}\n")

    if result['warnings']:
        print(f"{'='*60}")
        print(f"WARNINGS")
        print(f"{'='*60}\n")
        for i, warning in enumerate(result['warnings'], 1):
            print(f"{i}. {warning['message']}")
            print(f"   Suggestion: {warning['suggestion']}")
            print(f"   Affected: {warning['affected']}\n")
```

## MCP Tools Integration

When referencing MCP (Model Context Protocol) tools, always use fully qualified names.

### Fully Qualified Names

MCP tools must be referenced with the server name prefix:

❌ **Wrong - will cause "tool not found" errors:**
```markdown
Use the `search_database` tool to find records.
Use the `list_files` tool to see available files.
```

✓ **Correct:**
```markdown
Use the `DatabaseServer:search_database` tool to find records.
Use the `FileServer:list_files` tool to see available files.
```

### Finding MCP Tool Names

```markdown
## Working with MCP Tools

To reference MCP tools correctly:

1. List available MCP servers and tools
2. Use format: `ServerName:tool_name`
3. Never use tool name alone

**Example workflow:**

\`\`\`python
# First, discover available MCP tools
# (This would be done via MCP protocol)
available_tools = [
    "GitHubServer:create_issue",
    "GitHubServer:list_pull_requests",
    "DatabaseServer:execute_query",
    "DatabaseServer:get_schema",
]

# Then reference them by fully qualified name
tool_to_use = "GitHubServer:create_issue"
\`\`\`

**In your skills, always specify the full name:**

To create a GitHub issue, use the `GitHubServer:create_issue` tool.
To query the database, use the `DatabaseServer:execute_query` tool.
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
