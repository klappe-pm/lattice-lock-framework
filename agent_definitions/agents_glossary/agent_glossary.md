# Model Orchestrator Agent Glossary

This document defines the key variables, types, and configuration options used within the Model Orchestrator system. It serves as a reference for agents and developers working with the codebase.

## 1. Core Types (`types.py`)

### `TaskType` (Enum)

Categorizes the nature of the request to optimize model selection.

- **`CODE_GENERATION`**: Generating code snippets, functions, or entire modules. Requires high precision and syntax correctness.
- **`DEBUGGING`**: Analyzing code for errors and suggesting fixes. Requires strong reasoning and code understanding.
- **`ARCHITECTURAL_DESIGN`**: High-level system design, pattern selection, and structure planning. Requires large context window and reasoning.
- **`DOCUMENTATION`**: Writing docstrings, READMEs, or technical guides.
- **`TESTING`**: Generating unit tests or integration tests.
- **`DATA_ANALYSIS`**: Interpreting data or logs.
- **`GENERAL`**: General purpose queries or conversation.
- **`REASONING`**: Complex logic puzzles or multi-step reasoning tasks.

### `ModelProvider` (Enum)

Supported model providers.

- **`OPENAI`**: OpenAI (GPT-4o, o1, etc.)
- **`ANTHROPIC`**: Anthropic (Claude 3.5 Sonnet, Haiku, etc.)
- **`GOOGLE`**: Google (Gemini 1.5 Pro, Flash)
- **`XAI`**: xAI (Grok Beta)
- **`OLLAMA`**: Local models via Ollama.
- **`AZURE`**: Azure OpenAI Service.

## 2. Model Configuration (`ModelCapabilities`)

Each model in the registry is defined by a `ModelCapabilities` object with the following fields:

| Field | Type | Description |
|:--- |:--- |:--- |
| `name` | `str` | The display name of the model (e.g., "GPT-4o"). |
| `api_name` | `str` | The exact string identifier used in API calls (e.g., "gpt-4o-2024-08-06"). |
| `provider` | `ModelProvider` | The provider of the model. |
| `context_window` | `int` | Maximum number of tokens the model can process (input + output). |
| `input_cost` | `float` | Cost per 1M input tokens (in USD). |
| `output_cost` | `float` | Cost per 1M output tokens (in USD). |
| `reasoning_score` | `float` | Internal benchmark score (0-100) for reasoning capabilities. |
| `coding_score` | `float` | Internal benchmark score (0-100) for coding capabilities. |
| `speed_rating` | `float` | Rating (0-10) of the model's generation speed (10 = fastest). |
| `supports_vision` | `bool` | Whether the model can process image inputs. |
| `supports_function_calling` | `bool` | Whether the model supports tool/function calling. |

## 3. Task Requirements (`TaskRequirements`)

When a request is made, a `TaskRequirements` object is created to filter suitable models:

| Field | Type | Description |
|:--- |:--- |:--- |
| `task_type` | `TaskType` | The type of task being performed. |
| `min_context` | `int` | Minimum context window required. |
| `max_cost` | `float` | Maximum acceptable cost per 1M tokens (optional). |
| `min_reasoning` | `float` | Minimum reasoning score required. |
| `min_coding` | `float` | Minimum coding score required. |
| `priority` | `str` | Strategy preference: "speed", "cost", "quality", or "balanced". |

## 4. Selection Strategies

The orchestrator uses different strategies to select the best model based on `TaskRequirements.priority`:

- **`quality`**: Prioritizes `reasoning_score` and `coding_score` above all else. Ignores cost.
- **`speed`**: Prioritizes `speed_rating`. Good for simple, interactive tasks.
- **`cost`**: Prioritizes low `input_cost` and `output_cost`. Good for bulk processing.
- **`balanced`**: A weighted score combining quality, speed, and cost.

## 5. Local Model Automation

Variables related to local model management (Ollama):

- **`RAM_BUFFER_MB`**: (Default: 1024) Amount of RAM to leave free for the system.
- **`KEEP_ALIVE_MODELS`**: List of models to keep loaded in memory for faster response.
- **`MODEL_SIZES`**: Dictionary mapping model names to their approximate VRAM/RAM usage in GB.
