# CipherGate
[![codecov](https://codecov.io/gh/ciphergate/ciphergate/branch/main/graph/badge.svg)]
[![PyPI](https://img.shields.io/pypi/v/ciphergate)](https://pypi.org/project/ciphergate/0.1.0/)
[![Python](https://img.shields.io/pypi/pyversions/ciphergate)](https://pypi.org/project/ciphergate/)
[![License](https://img.shields.io/github/license/ciphergate/ciphergate)](https://github.com/ciphergate/ciphergate/blob/main/LICENSE)

> Enterprise-grade AI Runtime & Governance SDK

CipherGate protects LLM and Agent by scanning text inputs and outputs for security threats. It detects **PII**, **secrets**, and **prompt injection** attempts, producing a unified risk score and detailed findings report.

## Features

- **PII Detection** — Identifies emails, phone numbers, credit cards, and other personally identifiable information.
- **Secrets Detection** — Detects API keys, tokens, passwords, and other hard-coded credentials.
- **Prompt Injection & Jailbreak Detection** — Flags attempts to override system instructions or manipulate model behavior.
- **Risk Scoring** — Aggregated 0–100 risk score with severity levels: `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`.
- **Rich CLI** — Beautiful terminal output with color-coded risk bars, findings tables, and recommendations.
- **Flexible Configuration** — Configure via Python API, YAML/TOML files, or environment variables.
- **Structured Logging** — Built-in structured logging via `structlog` with optional JSON output.

## Requirements

- Python **3.12+**

## Installation

```bash
pip install ciphergate
```

## Quick Start

### Python SDK

```python
from ciphergate import CipherGate, CipherGateConfig

# Use default configuration (all detectors enabled)
gate = CipherGate()
report = gate.scan("Your text to analyze")

print(f"Risk Score: {report.risk_score}")
print(f"Severity:   {report.severity.value}")
print(report.to_json(indent=2))
```

### Custom Configuration

```python
from ciphergate import CipherGate, CipherGateConfig

config = CipherGateConfig(
    detectors=["pii", "secrets"],
    risk_threshold=75,
    parallel_scan=True,
)

gate = CipherGate(config)
report = gate.scan("User input containing sensitive data...")
```

### Configuration from File

```python
config = CipherGateConfig.from_file("ciphergate.yaml")
gate = CipherGate(config)
```

### Configuration from Environment Variables

```bash
export CIPHERGATE_DETECTORS="pii,secrets"
export CIPHERGATE_RISK_THRESHOLD=30
export CIPHERGATE_PARALLEL_SCAN=true
```

```python
config = CipherGateConfig.from_env()
gate = CipherGate(config)
```

## CLI

CipherGate includes a powerful command-line interface built with [Typer](https://typer.tiangolo.com/) and [Rich](https://rich.readthedocs.io/).

### Scan text directly

```bash
ciphergate scan "Hello, my email is alice@example.com"
```

### Scan from stdin

```bash
echo "some text with sk-live-1234567890abcdef" | ciphergate scan
```

### JSON output

```bash
ciphergate scan "suspicious text" --json
```

### Select specific detectors

```bash
ciphergate scan "input text" --detector secrets --detector pii
```

### Use a custom configuration file

```bash
ciphergate scan "input text" --config ciphergate.yaml
```

### Set a risk threshold (exit code 1 if exceeded)

```bash
ciphergate scan "input text" --threshold 80
```

### Show version

```bash
ciphergate --version
```

## Configuration File Example

Create a `ciphergate.yaml` (or `.toml`) file:

```yaml
detectors:
  - pii
  - secrets
  - prompt_injection

risk_threshold: 50
max_input_size: 1048576
enable_logging: true
log_level: INFO
json_logs: false
parallel_scan: false
```

## API Overview

### `CipherGate`

The main facade that orchestrates detectors and scanning.

| Method | Description |
|--------|-------------|
| `CipherGate(config=None)` | Initialize with optional `CipherGateConfig`. |
| `scan(text: str) -> ScanReport` | Scan text and return a risk report. |

### `CipherGateConfig`

Global configuration model.

| Field | Default | Description |
|-------|---------|-------------|
| `detectors` | `["pii", "secrets", "prompt_injection"]` | Enabled detectors. |
| `risk_threshold` | `50` | Risk score threshold (0–100). |
| `max_input_size` | `1_048_576` | Maximum input size in bytes. |
| `enable_logging` | `True` | Enable structured logging. |
| `log_level` | `"INFO"` | Logging level. |
| `json_logs` | `False` | Output logs as JSON. |
| `parallel_scan` | `False` | Run detectors in parallel. |
| `cache_regex` | `True` | Cache compiled regex patterns. |

### `ScanReport`

Aggregated scan result.

| Attribute | Type | Description |
|-----------|------|-------------|
| `risk_score` | `int` | Aggregated risk score (0–100). |
| `severity` | `Severity` | Overall severity (`LOW`–`CRITICAL`). |
| `findings` | `list[Finding]` | Individual detector findings. |
| `recommendations` | `list[str]` | Actionable recommendations. |
| `execution_time_ms` | `int` | Total scan time in milliseconds. |
| `metadata` | `dict` | Additional scan metadata. |

## Development

Clone the repository and install development dependencies:

```bash
git clone https://github.com/ciphergate/ciphergate.git
cd ciphergate
pip install -e ".[dev]"
```

### Run tests

```bash
pytest
```

### Run linting

```bash
ruff check .
black --check .
mypy src
```

## Documentation

Full documentation is available at [https://ciphergate.readthedocs.io](https://ciphergate.readthedocs.io).

## License

[MIT](LICENSE)
