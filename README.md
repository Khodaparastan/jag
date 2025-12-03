# jag - JSON Aggregator

A powerful Bash script for merging, filtering, projecting, and aggregating multiple JSON files.

## Overview

`jag` (JSON Aggregator) processes multiple JSON input files matching the pattern `in-[0-9]*.json` and combines them into a single output file with optional filtering, field selection, and aggregation capabilities.

## Prerequisites

- Bash 4.0 or higher
- `jq` command-line JSON processor

## Installation

1. Save the script as `jag`
2. Make it executable:
   
```bash
   chmod +x jag
   ```
   
3. Optionally, move it to a directory in your PATH:

```bash
   sudo mv jag /usr/local/bin/
```

## Usage

```bash
jag [output.json] \
  [--root jq_path] \
  [--select field1 field2 ...] \
  [--filter field=true field2=false ...] \
  [--agg field=op field2=op2 ...] \
  [--hist field1 field2 ...] \
  [--output merged|agg|both]
```

## Options

| Option | Description | Default |
|:---|:---|:---|
| `output.json` | Output file name | `merged.json` |
| `--root` | JQ path where records are located | `.result` |
| `--select` | Fields to include in output | All fields |
| `--filter` | Boolean filters (field=true/false) | No filters |
| `--agg` | Aggregation operations | No aggregations |
| `--hist` | Fields to generate histograms for | No histograms |
| `--output` | Output mode: `merged`, `agg`, or `both` | `merged` |

### Aggregation Operations

| Operation | Description |
|:---|:---|
| `sum` | Sum of all values |
| `count` | Count of records |
| `min` | Minimum value |
| `max` | Maximum value |
| `avg` | Average value |
| `true_count` | Count of true boolean values |
| `false_count` | Count of false boolean values |
| `true_rate` | Ratio of true values (0-1) |
| `false_rate` | Ratio of false values (0-1) |

## Examples

### Basic Merging

Merge all `in-*.json` files with default settings:

```bash
jag
```

### Custom Output File

Specify output filename:

```bash
jag all.json
```

### Custom Root Path

Read from a different JSON path:

```bash
jag all.json --root .data.items
```

### Field Selection

Select specific fields only:

```bash
jag all.json --select amount fee created_at
```

### Boolean Filtering

Filter records by boolean conditions:

```bash
jag all.json --filter is_success=true is_verified=true
```

### Combined Selection and Filtering

Use `--` as a separator for clarity:

```bash
jag all.json --select amount fee -- --filter is_success=true
```

### Aggregations Only

Generate aggregation statistics without merged data:

```bash
jag all.json --agg amount=sum fee=sum --output agg
```

### Histogram Generation

Create frequency counts for categorical fields:

```bash
jag all.json --hist bank_code --output agg
```

### Complex Example

Combine multiple operations:

```bash
jag summary.json \
  --root .data.transactions \
  --select amount fee status created_at \
  --filter is_verified=true \
  --agg amount=sum amount=avg fee=max \
  --hist status \
  --output both
```

## Input File Format

The script expects input files named:
- `in-1.json`
- `in-2.json`
- `in-10.json`
- etc.

Each file should contain JSON with a structure accessible via the `--root` path (default: `.result`).

### Example Input File Structure

```json
{
  "result": [
    {
      "amount": 100,
      "fee": 5,
      "is_success": true,
      "bank_code": "ABC"
    },
    {
      "amount": 200,
      "fee": 10,
      "is_success": false,
      "bank_code": "XYZ"
    }
  ]
}
```

## Output Formats

### Merged Mode (default)

```json
{
  "result": [
    { "amount": 100, "fee": 5 },
    { "amount": 200, "fee": 10 }
  ]
}
```

### Aggregation Mode

```json
{
  "aggregates": {
    "amount_sum": 300,
    "amount_avg": 150,
    "bank_code_counts": {
      "ABC": 1,
      "XYZ": 1
    }
  }
}
```

### Both Mode

```json
{
  "result": [...],
  "aggregates": {...}
}
```

## Error Handling

The script will exit with an error if:
- No input files matching `in-[0-9]*.json` are found
- Invalid arguments are provided
- Required argument values are missing
- Invalid aggregation operations are specified

## Features

- **Null-safe**: Uses `nullglob` to handle cases with no matching files
- **Strict mode**: Runs with `set -euo pipefail` for robust error handling
- **Flexible filtering**: Supports multiple boolean conditions
- **Multiple aggregations**: Apply several operations on different fields
- **Histogram support**: Generate frequency distributions
- **Custom root paths**: Work with various JSON structures

## License

This script is provided as-is for use in your projects.
