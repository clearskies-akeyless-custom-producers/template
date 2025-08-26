# Akeyless Custom Producer Template

This template allows you to quickly scaffold a new Akeyless Custom Producer for any third-party API that requires authentication. It follows the architecture and pattern used by other clearskies-akeyless-custom-producers.

## Prerequisites

- Python 3.11+
- [Copier](https://copier.readthedocs.io/en/stable/) for template generation

## Installation

First, install Copier:

```bash
pip install copier
```

## Usage

To create a new custom producer, run:

```bash
copier copy gh:clearskies-akeyless-custom-producers/template my-producer
```

This will prompt you for the necessary information to configure your custom producer:

- `service_name`: The name of the service (e.g., gitlab, snyk, wiz)
- `service_display_name`: Display name of the service (e.g., GitLab, Snyk, Wiz)
- `author_name`: Your name
- `author_email`: Your email
- `service_supports_rotation`: Whether the service supports credential rotation
- `service_supports_revocation`: Whether the service supports credential revocation

## Project Structure

After generation, your project will have the following structure:

```
my-producer/
├── .gitignore
├── LICENSE
├── MANIFEST.in
├── README.md
├── uv.lock  (will be generated when you run uv install)
├── pyproject.toml
├── ruff.toml
└── src/
    └── clearskies_akeyless_custom_myservice/
        ├── __init__.py
        ├── create.py
        ├── rotate.py  (only if rotation is supported)
        └── revoke.py  (only if revocation is supported)
```

## Implementation

After generating your project, you'll need to customize the method signatures and implement the API-specific functionality:

### 1. Define Your Schema (`__init__.py`)

Update the `PayloadSchema` class to define the input parameters your API needs:

```python
class PayloadSchema(clearskies.Schema):
    api_token = columns.String(validators=[validators.Required()])
    project_id = columns.Integer(validators=[validators.Required()])
    access_level = columns.Integer(default=30)
```

### 2. Customize Method Signatures

Update the function signatures in each file to match your API requirements:

**create.py example:**

```python
def create(
    api_token: str,
    project_id: int,
    access_level: int,
    requests: requests.Session,
    uuid: Any
) -> dict[str, Any]:
```

**rotate.py example (if enabled):**

```python
def rotate(
    current_token: str,
    project_id: int,
    token_id: str,
    requests: requests.Session,
    uuid: Any
) -> dict[str, Any]:
```

**revoke.py example (if enabled):**

```python
def revoke(
    admin_token: str,
    project_id: int,
    token_id: str,
    requests: requests.Session,
    uuid: Any
) -> dict[str, Any]:
```

### 3. Implement API Logic

Each file contains comprehensive documentation and examples showing different parameter patterns. The focus is on:

- Proper type hints for all parameters
- Clear parameter documentation
- Examples of different API patterns
- Return value specifications

## Development

To set up your development environment:

```bash
cd my-producer
# Install uv if not already installed
pip install uv

# Install the project and development dependencies
uv venv
uv install --editable ".[dev]"
```

## Testing Your Implementation

You can test your implementation with:

```bash
# Direct HTTP calls:
curl 'http://localhost:8080/sync/create' -d '{"payload":"{\"service_token\":\"your_token\",\"project_id\":123}"}'

# Integration with Akeyless:
# Follow the Akeyless documentation to register your custom producer
```

## License

MIT License - see the LICENSE file for details
