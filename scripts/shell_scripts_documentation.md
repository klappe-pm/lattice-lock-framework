# Shell Scripts and Automation Tools Documentation

## Overview
The zen-mcp-server project includes several shell scripts for automation, testing, and deployment. These scripts handle environment setup, code quality checks, integration testing, and Docker deployment.

---

## 1. code_quality_checks.sh
**Purpose:** Automated code quality control that runs linting, formatting, and unit tests before commits.

### Description
This script ensures all code meets quality standards required for CI/CD. It performs:
- Code linting with ruff
- Code formatting with black
- Import sorting with isort
- Unit test execution with pytest

### Command-line Options
- No command-line arguments required
- Script is designed to be run without parameters: `./code_quality_checks.sh`

### Dependencies
**Python Dependencies (automatically installed):**
- ruff (linting)
- black (code formatting)
- isort (import sorting)
- pytest (testing framework)

**System Requirements:**
- Python virtual environment (.zen_venv)
- Python 3.10+ 
- pip package manager

### Typical Workflow
1. **Initialization Phase:**
   - Detects Python environment (venv or activated virtual environment)
   - Validates virtual environment exists (exits if not found)
   - Checks and installs development dependencies from requirements-dev.txt

2. **Linting & Formatting Phase:**
   - Runs ruff with auto-fix capability
   - Applies black code formatting
   - Sorts imports with isort
   - Verifies all linting passes

3. **Testing Phase:**
   - Runs unit tests (excludes integration tests marked with @pytest.mark.integration)
   - Uses verbose output and stops on first failure

4. **Success Reporting:**
   - Displays summary of all passed checks
   - Confirms code is ready for commit and GitHub Actions

### Exit Codes
- 0: All checks passed successfully
- 1: Virtual environment not found or checks failed

### Usage Example
```bash
# Run before committing changes
./code_quality_checks.sh

# Output shows:
# 🔍 Running Code Quality Checks for Zen MCP Server
# ✅ Using venv
# 📋 Step 1: Running Linting and Formatting Checks
# 🧪 Step 2: Running Complete Unit Test Suite
# 🎉 All Code Quality Checks Passed!
```

---

## 2. run-server.sh
**Purpose:** Main setup and management script for the zen-mcp-server environment.

### Description
A comprehensive, platform-agnostic setup script that handles:
- Python environment detection and setup
- Virtual environment creation (supports uv, venv, virtualenv)
- Dependency installation
- API key configuration
- Claude Desktop/CLI integration
- Docker cleanup from previous installations

### Command-line Options
```bash
-h, --help       Show help message with all options
-v, --version    Display version information only
-f, --follow     Setup server and follow logs in real-time
-c, --config     Show configuration instructions for Claude clients
--clear-cache    Clear Python cache files (fixes import issues)
```

### Dependencies
**System Dependencies:**
- Python 3.10+ (preferably 3.12)
- pip or uv package manager
- Virtual environment support (python3-venv on Linux)

**Optional Tools:**
- pyenv (for Python version management)
- uv (for faster package installation)
- Docker (for cleanup of old containers)
- Claude CLI (for MCP registration)

**Python Dependencies (from requirements.txt):**
- mcp (Model Context Protocol)
- google-generativeai
- openai
- pydantic
- python-dotenv

### Environment Variables
Required in `.env` file (at least one):
- `GEMINI_API_KEY`
- `OPENAI_API_KEY`
- `XAI_API_KEY`
- `DIAL_API_KEY`
- `OPENROUTER_API_KEY`
- `CUSTOM_API_URL` (for local models)

### Typical Workflow

1. **Initial Setup (First Run):**
   ```bash
   ./run-server.sh
   ```
   - Checks for Python 3.10+
   - Creates virtual environment
   - Installs dependencies
   - Creates .env from .env.example
   - Validates API keys
   - Offers Claude Desktop/CLI integration

2. **Follow Logs:**
   ```bash
   ./run-server.sh -f
   ```
   - Performs setup if needed
   - Tails log file (logs/mcp_server.log)

3. **Show Configuration:**
   ```bash
   ./run-server.sh -c
   ```
   - Displays MCP configuration for Claude clients
   - Shows platform-specific config paths

4. **Clear Cache (Troubleshooting):**
   ```bash
   ./run-server.sh --clear-cache
   ```
   - Removes *.pyc files and __pycache__ directories
   - Helps resolve import issues

### Platform-Specific Features

**macOS:**
- Claude config: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Supports pyenv installation via Homebrew
- Uses BSD sed for file modifications

**Linux/WSL:**
- Claude config: `~/.config/Claude/claude_desktop_config.json` (Linux)
- Claude config: `/mnt/c/Users/$USER/AppData/Roaming/Claude/claude_desktop_config.json` (WSL)
- Auto-detects distribution and package manager
- Attempts automatic installation of python3-venv if missing

**Windows (Git Bash/WSL):**
- Supports Windows Python paths
- Handles APPDATA environment variable for config locations

### Key Functions
- **find_python()**: Locates suitable Python interpreter (3.10+)
- **setup_environment()**: Creates virtual environment (uv-first approach)
- **install_dependencies()**: Installs Python packages
- **setup_env_file()**: Creates and configures .env file
- **check_claude_cli_integration()**: Registers MCP with Claude CLI
- **check_claude_desktop_integration()**: Updates Claude Desktop config
- **cleanup_docker()**: Removes old Docker containers/images/volumes

### Exit Codes
- 0: Success
- 1: Error (missing dependencies, invalid environment, etc.)

---

## 3. run_integration_tests.sh
**Purpose:** Executes integration tests that require real API calls.

### Description
Runs integration tests using actual API keys to verify end-to-end functionality. Designed for local testing on development machines.

### Command-line Options
```bash
--with-simulator    Also run simulator tests after integration tests
```

### Dependencies
**Required:**
- Python virtual environment (.zen_venv)
- pytest test framework
- Configured .env file with API keys

**API Keys (at least one required):**
- GEMINI_API_KEY
- OPENAI_API_KEY
- XAI_API_KEY
- OPENROUTER_API_KEY
- CUSTOM_API_URL

### Typical Workflow

1. **Standard Integration Tests:**
   ```bash
   ./run_integration_tests.sh
   ```
   - Activates virtual environment
   - Checks API key availability
   - Runs tests marked with @pytest.mark.integration
   - Shows short traceback on failures

2. **With Simulator Tests:**
   ```bash
   ./run_integration_tests.sh --with-simulator
   ```
   - Runs integration tests
   - Additionally runs communication_simulator_test.py
   - Provides verbose output

### Test Scope
- **Integration Tests:** Real API calls to configured services
- **Simulator Tests:** Tool communication simulation
- **Excluded:** Unit tests (run via code_quality_checks.sh)

### Output Information
- Shows which API keys are configured
- Displays test progress with verbose output
- Provides tips for different test scenarios

### Exit Codes
- 0: All tests passed
- 1: Virtual environment not found or tests failed

---

## 4. docker/scripts/build.sh
**Purpose:** Builds the Docker image for containerized deployment.

### Description
Creates a Docker image for the zen-mcp-server using docker-compose. Handles initial setup including .env file creation.

### Command-line Options
- None (script takes no arguments)

### Dependencies
- Docker installed and running
- docker-compose available
- .env or .env.example file

### Typical Workflow
1. **Check Environment:**
   - Verifies .env exists (copies from .env.example if missing)
   - Warns user to configure API keys

2. **Build Process:**
   - Runs `docker-compose build --no-cache`
   - Forces fresh build without cached layers

3. **Verification:**
   - Confirms image exists in Docker
   - Displays image details

### Files Used
- docker-compose.yml (Docker service configuration)
- Dockerfile (Image build instructions)
- .env (Environment variables)

### Exit Codes
- 0: Build successful
- 1: Missing files or build failed

---

## 5. docker/scripts/deploy.sh
**Purpose:** Deploys the zen-mcp-server using Docker Compose.

### Description
Manages the complete deployment lifecycle including environment validation, service startup, and health monitoring.

### Command-line Options
- None (script takes no arguments)

### Dependencies
- Docker and docker-compose
- Configured .env file with at least one API key
- logs directory (created if missing)

### Environment Validation
Checks for at least one of:
- GEMINI_API_KEY
- GOOGLE_API_KEY
- OPENAI_API_KEY
- XAI_API_KEY
- DIAL_API_KEY
- OPENROUTER_API_KEY

### Typical Workflow

1. **Environment Setup:**
   - Loads variables from .env
   - Validates at least one API key exists
   - Creates logs directory

2. **Deployment:**
   - Stops existing containers (`docker-compose down`)
   - Starts services (`docker-compose up -d`)

3. **Health Monitoring:**
   - Implements exponential backoff for health checks
   - Maximum 6 attempts with increasing delays (2, 4, 8, 16, 32, 64 seconds)
   - Timeout after 60 seconds total

4. **Status Reporting:**
   - Shows service status
   - Provides useful management commands

### Health Check Strategy
- Uses Docker's built-in health check mechanism
- Exponential backoff prevents false failures during startup
- Shows logs if health check fails

### Management Commands
```bash
# View logs
docker-compose logs -f zen-mcp

# Stop service
docker-compose down

# Restart service
docker-compose restart zen-mcp
```

### Exit Codes
- 0: Deployment successful and healthy
- 1: Missing configuration, deployment failed, or unhealthy service

---

## Script Interdependencies

### Execution Order for Complete Setup:
1. `./run-server.sh` - Initial environment setup
2. `./code_quality_checks.sh` - Verify code quality
3. `./run_integration_tests.sh` - Test with real APIs
4. `docker/scripts/build.sh` - Build Docker image (optional)
5. `docker/scripts/deploy.sh` - Deploy with Docker (optional)

### Shared Resources:
- **.zen_venv/** - Virtual environment used by all Python scripts
- **.env** - Configuration file for API keys
- **logs/** - Log directory for server output
- **requirements.txt** - Production dependencies
- **requirements-dev.txt** - Development dependencies

### File Markers:
- **.docker_cleaned** - Indicates Docker cleanup completed
- **.desktop_configured** - Claude Desktop configuration done
- **.zen_venv/uv_created** - Virtual environment created by uv tool

---

## Best Practices

### For Development:
1. Always run `code_quality_checks.sh` before committing
2. Use `run_integration_tests.sh` to verify API functionality
3. Follow logs with `./run-server.sh -f` during debugging

### For Deployment:
1. Ensure .env has valid API keys
2. Use Docker scripts for containerized deployment
3. Monitor health checks and logs

### Troubleshooting:
1. Clear cache with `./run-server.sh --clear-cache` for import issues
2. Remove .zen_venv and re-run setup for environment problems
3. Check logs/mcp_server.log for runtime errors

### Cross-Platform Considerations:
- Scripts detect OS automatically (macOS, Linux, WSL, Windows)
- Use appropriate package managers per distribution
- Handle different Python installation methods (system, pyenv, uv)
