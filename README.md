# Professional-Python-Project-Setup-Industry-Best-Practices
The missing manual for professional Python project setup. Learn industry-standard structure, dependency management, testing, and deployment practices that separate pros from beginners. Stop building on shaky foundations—this guide has saved countless devs from embarrassing code reviews.
# Professional Python Project Setup: Industry Best Practices

## Introduction

In today's fast-paced tech landscape, Python has established itself as a cornerstone programming language across AI, web development, data science, and more. However, as newcomers rush to learn advanced topics, they often overlook the critical foundation of proper project setup and configuration.

This guide shares professional-grade Python project practices used in industry. Whether you're developing machine learning models, building web applications with Django or FastAPI, or creating data pipelines, these standards will help you:

- Write more maintainable code
- Collaborate effectively with other developers
- Produce work that meets industry expectations
- Streamline development workflows
- Prepare your projects for scaling

## Table of Contents

1. Project Structure
2. Virtual Environment Management
3. Dependency Management
4. Configuration Files
5. Documentation Standards
6. Testing Frameworks and Practices
7. Code Quality Tools
8. Version Control Best Practices
9. Continuous Integration/Continuous Deployment
10. Production Deployment Considerations

---

## 1. Project Structure

### Standard Project Layout

```
my_project/
├── src/                          # Source code
│   └── my_package/
│       ├── __init__.py
│       ├── core/
│       │   ├── __init__.py
│       │   └── module1.py
│       ├── utils/
│       │   ├── __init__.py
│       │   └── helpers.py
│       └── main.py
├── tests/                        # Test files
│   ├── __init__.py
│   ├── test_module1.py
│   └── conftest.py
├── docs/                         # Documentation
│   ├── index.md
│   └── api.md
├── notebooks/                    # Jupyter notebooks (if applicable)
├── data/                         # Data files (often gitignored)
│   ├── raw/
│   └── processed/
├── configs/                      # Configuration files
│   └── settings.yaml
├── scripts/                      # Utility scripts
│   └── setup_db.py
├── .github/                      # CI/CD configuration
│   └── workflows/
│       └── main.yml
├── .gitignore                    # Files to ignore in version control
├── pyproject.toml                # Project metadata and dependencies
├── README.md                     # Project overview
├── CHANGELOG.md                  # Version history
├── LICENSE                       # License information
└── Makefile                      # Common commands
```

### Using the src Layout

Modern Python projects now favor the `src` layout, which:
- Prevents accidental imports from development directory
- Forces installation of the package for testing
- Makes packaging more reliable
- Creates a clearer distinction between package code and project files

### Key Files Explained

- **`__init__.py`**: Makes directories importable Python packages
- **`pyproject.toml`**: Modern replacement for setup.py
- **`README.md`**: First document people read about your project
- **`LICENSE`**: Legal terms for using your code
- **`CHANGELOG.md`**: Documents version changes
- **`Makefile`**: Simplifies common commands

---

## 2. Virtual Environment Management

### Why Virtual Environments Matter

Virtual environments isolate project dependencies, preventing conflicts between projects and ensuring reproducible builds.

### Options for Creating Virtual Environments

#### 1. Built-in venv

```bash
# Create virtual environment
python -m venv .venv

# Activate on Linux/macOS
source .venv/bin/activate

# Activate on Windows
.venv\Scripts\activate

# Deactivate when done
deactivate
```

#### 2. Conda (for Data Science)

```bash
# Create environment
conda create -n myproject python=3.11

# Activate environment
conda activate myproject

# Deactivate
conda deactivate
```

#### 3. Poetry

```bash
# Create new project
poetry new myproject

# Create environment for existing project
cd myproject
poetry install

# Activate
poetry shell
```

### Best Practices

- Never install packages globally for project work
- Include activation instructions in your README
- Consider automating environment setup
- Don't commit the virtual environment to version control

---

## 3. Dependency Management

### Modern Approaches

#### 1. pyproject.toml (Recommended)

```toml
[build-system]
requires = ["setuptools>=61.0", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "mypackage"
version = "0.1.0"
description = "My awesome package"
readme = "README.md"
requires-python = ">=3.8"
license = {text = "MIT"}
authors = [
    {name = "Your Name", email = "your.email@example.com"},
]
dependencies = [
    "requests>=2.28.0",
    "pandas~=2.0.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0.0",
    "black>=23.0.0",
    "isort>=5.0.0",
    "flake8>=6.0.0",
    "mypy>=1.0.0",
]
docs = [
    "sphinx>=6.0.0",
    "sphinx-rtd-theme>=1.2.0",
]
```

#### 2. Poetry

```toml
[tool.poetry]
name = "mypackage"
version = "0.1.0"
description = "My awesome package"
authors = ["Your Name <your.email@example.com>"]
readme = "README.md"
packages = [{include = "mypackage", from = "src"}]

[tool.poetry.dependencies]
python = "^3.8"
requests = "^2.28.0"
pandas = "^2.0.0"

[tool.poetry.group.dev.dependencies]
pytest = "^7.0.0"
black = "^23.0.0"
isort = "^5.0.0"
flake8 = "^6.0.0"
mypy = "^1.0.0"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

#### 3. Traditional requirements.txt

```
# requirements.txt
requests>=2.28.0
pandas~=2.0.0

# requirements-dev.txt
pytest>=7.0.0
black>=23.0.0
isort>=5.0.0
flake8>=6.0.0
mypy>=1.0.0
```

### Version Pinning Strategies

- **`==`**: Exact version (use for reproducible builds)
- **`>=`**: Minimum version (allows updates)
- **`~=`**: Compatible release (minor updates only)
- **`^`**: Compatible release (Poetry syntax)

### Dependencies vs. Dev Dependencies

Separate your dependencies into:
- **Runtime dependencies**: Required to run your code
- **Development dependencies**: Tools for testing, linting, etc.

---

## 4. Configuration Files

### Environment Variables

Use environment variables for sensitive or environment-specific configuration:

```python
# settings.py
import os
from dotenv import load_dotenv

load_dotenv()  # Load variables from .env file

DATABASE_URL = os.getenv("DATABASE_URL", "sqlite:///default.db")
API_KEY = os.getenv("API_KEY")
DEBUG = os.getenv("DEBUG", "False").lower() in ("true", "1", "t")
```

### Configuration Files

For complex configurations, use YAML or JSON:

```yaml
# config.yaml
database:
  host: localhost
  port: 5432
  name: mydb
  user: ${DB_USER}  # Reference env variable
  password: ${DB_PASSWORD}

logging:
  level: INFO
  format: "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
```

Loading configuration:

```python
import os
import yaml
from string import Template

def load_config(config_path):
    with open(config_path) as f:
        template = Template(f.read())
        config_str = template.substitute(os.environ)
        return yaml.safe_load(config_str)

config = load_config("config.yaml")
```

### Settings Management

For Django-like settings management:

```python
# settings/__init__.py
import os

# Default to development settings
environment = os.getenv("ENVIRONMENT", "development")

if environment == "production":
    from .production import *
elif environment == "staging":
    from .staging import *
else:
    from .development import *
```

---

## 5. Documentation Standards

### Code Documentation

#### Docstrings

Use Google-style, NumPy-style, or reST docstrings consistently:

```python
def calculate_metrics(data, metric_type="mean"):
    """Calculate statistical metrics for the given data.
    
    Args:
        data (list): List of numerical values to analyze.
        metric_type (str, optional): Type of metric to calculate.
            Options are 'mean', 'median', or 'mode'. Default is 'mean'.
            
    Returns:
        float: The calculated metric value.
        
    Raises:
        ValueError: If metric_type is not one of the allowed values.
        TypeError: If data contains non-numeric values.
    
    Examples:
        >>> calculate_metrics([1, 2, 3, 4, 5])
        3.0
    """
```

#### Type Hints

Use type hints for better IDE support and static checking:

```python
from typing import List, Dict, Optional, Union, Tuple

def process_data(
    data: List[Dict[str, Union[str, int]]],
    options: Optional[Dict[str, str]] = None
) -> Tuple[List[int], float]:
    """Process the input data and return results."""
    # Implementation
```

### Project Documentation

Essential project documentation includes:

1. **README.md**:
   - Project overview and purpose
   - Installation instructions
   - Quick start guide
   - Basic usage examples
   - Links to more documentation

2. **API Documentation**:
   - Using Sphinx or MkDocs
   - Auto-generated from docstrings
   - Usage examples

3. **CONTRIBUTING.md**:
   - How to contribute
   - Development setup
   - Code review process

4. **CHANGELOG.md**:
   - Version history
   - Notable changes

---

## 6. Testing Frameworks and Practices

### Test Structure

```
tests/
├── __init__.py
├── conftest.py           # Shared fixtures
├── unit/                 # Unit tests
│   ├── __init__.py
│   └── test_module1.py
└── integration/          # Integration tests
    ├── __init__.py
    └── test_api.py
```

### Writing Tests with pytest

```python
# tests/unit/test_calculator.py
import pytest
from mypackage.calculator import add, divide

def test_add():
    assert add(1, 2) == 3
    assert add(-1, 1) == 0
    assert add(0, 0) == 0

def test_divide():
    assert divide(10, 2) == 5
    assert divide(5, 2) == 2.5
    
def test_divide_by_zero():
    with pytest.raises(ZeroDivisionError):
        divide(5, 0)
```

### Fixtures

```python
# tests/conftest.py
import pytest
import tempfile
import os

@pytest.fixture
def temp_file():
    """Provide a temporary file that's removed after the test."""
    fd, path = tempfile.mkstemp()
    yield path
    os.close(fd)
    os.unlink(path)
```

### Test Configuration

```toml
# In pyproject.toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = "test_*.py"
markers = [
    "slow: marks tests as slow",
    "integration: marks tests as integration tests",
]
```

### Test Coverage

```bash
# Install coverage tool
pip install pytest-cov

# Run tests with coverage
pytest --cov=mypackage tests/

# Generate HTML report
pytest --cov=mypackage --cov-report=html tests/
```

---

## 7. Code Quality Tools

### Formatter: Black

```toml
# In pyproject.toml
[tool.black]
line-length = 88
target-version = ["py39"]
include = '\.pyi?$'
exclude = '''
/(
    \.git
  | \.hg
  | \.mypy_cache
  | \.tox
  | \.venv
  | _build
  | buck-out
  | build
  | dist
)/
'''
```

### Import Sorter: isort

```toml
# In pyproject.toml
[tool.isort]
profile = "black"
line_length = 88
```

### Linter: Flake8

```
# .flake8
[flake8]
max-line-length = 88
exclude = .git,__pycache__,docs/source/conf.py,old,build,dist
extend-ignore = E203
```

### Static Type Checker: mypy

```toml
# In pyproject.toml
[tool.mypy]
python_version = "3.9"
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true
disallow_incomplete_defs = true

[[tool.mypy.overrides]]
module = "tests.*"
disallow_untyped_defs = false
```

### Pre-commit Hooks

```yaml
# .pre-commit-config.yaml
repos:
-   repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
    -   id: trailing-whitespace
    -   id: end-of-file-fixer
    -   id: check-yaml
    -   id: debug-statements
-   repo: https://github.com/psf/black
    rev: 23.3.0
    hooks:
    -   id: black
-   repo: https://github.com/pycqa/isort
    rev: 5.12.0
    hooks:
    -   id: isort
-   repo: https://github.com/pycqa/flake8
    rev: 6.0.0
    hooks:
    -   id: flake8
-   repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.3.0
    hooks:
    -   id: mypy
        additional_dependencies: [types-requests]
```

---

## 8. Version Control Best Practices

### .gitignore

```
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg
MANIFEST

# Virtual Environments
venv/
.venv/
ENV/
env/
env.bak/
venv.bak/

# Environment Variables
.env

# IDE
.idea/
.vscode/
*.swp
*.swo

# Testing
.coverage
htmlcov/
.pytest_cache/
.tox/

# Documentation
docs/_build/

# Project-specific
data/raw/
data/processed/
logs/
*.log
```

### Commit Messages

Follow the Conventional Commits standard:

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

Examples:
- `feat(api): add user authentication endpoint`
- `fix(db): resolve connection timeout issue`
- `docs: update README with new examples`
- `test: add unit tests for payment processing`
- `refactor: simplify data processing pipeline`

### Branching Strategy

For smaller teams, use GitHub Flow:
1. Create feature branches from main
2. Develop and commit changes
3. Open pull request
4. Review code
5. Deploy and test
6. Merge to main

For larger projects, consider Git Flow with develop and release branches.

---

## 9. Continuous Integration/Continuous Deployment

### GitHub Actions Example

```yaml
# .github/workflows/python-tests.yml
name: Python Tests

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.8", "3.9", "3.10", "3.11"]

    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v4
      with:
        python-version: ${{ matrix.python-version }}
        
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -e ".[dev]"
        
    - name: Lint with flake8
      run: |
        flake8 src tests
        
    - name: Check typing with mypy
      run: |
        mypy src
        
    - name: Test with pytest
      run: |
        pytest --cov=src tests/
        
    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v3
```

### CircleCI Example

```yaml
# .circleci/config.yml
version: 2.1

orbs:
  python: circleci/python@1.5

jobs:
  build-and-test:
    docker:
      - image: cimg/python:3.10
    steps:
      - checkout
      - python/install-packages:
          pkg-manager: pip
          packages:
            - ".[dev]"
          install-args: -e
      - run:
          name: Run tests
          command: pytest --cov=src tests/
      - store_test_results:
          path: test-results/

workflows:
  main:
    jobs:
      - build-and-test
```

---

## 10. Production Deployment Considerations

### Application Configuration

1. **Environment Variables**:
   - Store secrets as environment variables
   - Use different values for dev/staging/prod
   - Never commit sensitive values to version control

2. **Configuration Hierarchy**:
   - Default values in code
   - Overridden by config files
   - Overridden by environment variables
   - Overridden by command-line arguments

### Logging

Set up structured logging:

```python
import logging
import logging.config
import json
import os

def setup_logging():
    """Set up logging configuration."""
    log_level = os.getenv("LOG_LEVEL", "INFO")
    
    config = {
        "version": 1,
        "disable_existing_loggers": False,
        "formatters": {
            "json": {
                "()": "pythonjsonlogger.jsonlogger.JsonFormatter",
                "format": "%(asctime)s %(levelname)s %(name)s %(message)s"
            },
            "standard": {
                "format": "%(asctime)s [%(levelname)s] %(name)s: %(message)s"
            }
        },
        "handlers": {
            "console": {
                "class": "logging.StreamHandler",
                "formatter": "standard" if os.getenv("ENVIRONMENT") == "development" else "json",
                "level": log_level
            },
            "file": {
                "class": "logging.handlers.RotatingFileHandler",
                "filename": "app.log",
                "maxBytes": 10485760,  # 10 MB
                "backupCount": 5,
                "formatter": "json",
                "level": log_level
            }
        },
        "loggers": {
            "": {
                "handlers": ["console", "file"],
                "level": log_level
            }
        }
    }
    
    logging.config.dictConfig(config)
```

### Containerization with Docker

```dockerfile
# Dockerfile

# Use a specific version for reproducible builds
FROM python:3.11-slim

# Set working directory
WORKDIR /app

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=off \
    PIP_DISABLE_PIP_VERSION_CHECK=on

# Install system dependencies
RUN apt-get update \
    && apt-get install -y --no-install-recommends gcc \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install Python dependencies
COPY pyproject.toml .
RUN pip install --no-cache-dir -e .

# Copy project
COPY . .

# Run as non-root user
RUN useradd -m appuser
USER appuser

# Run the application
CMD ["python", "-m", "mypackage"]
```

### Deploy Checklist

Before deploying to production:

- [ ] Run comprehensive test suite
- [ ] Check code coverage
- [ ] Run security audits (`pip-audit`, `bandit`)
- [ ] Validate documentation
- [ ] Set up monitoring
- [ ] Configure proper logging
- [ ] Set up error tracking
- [ ] Review resource requirements
- [ ] Create backup strategy
- [ ] Document deployment process

---

## Conclusion

Establishing proper Python project setup and configuration is a critical foundation for successful development. By following these industry standards, you'll create more maintainable, collaborative, and professional code bases that scale with your needs.

Remember that these practices aren't just formalities—they're battle-tested approaches that solve real problems in software development. Whether you're building AI models, web applications, or data pipelines, these standards will serve you well throughout your Python journey.

---

## Further Resources

- [Python Packaging User Guide](https://packaging.python.org)
- [Hypermodern Python](https://cjolowicz.github.io/posts/hypermodern-python-01-setup/)
- [Python Application Layouts](https://realpython.com/python-application-layouts/)
- [pytest Documentation](https://docs.pytest.org/)
- [Type hints cheat sheet](https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.html)
- [12 Factor App Methodology](https://12factor.net/)
