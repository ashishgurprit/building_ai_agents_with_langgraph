# Building AI Agents with LangGraph

This repository contains Jupyter notebooks for the "Building AI Agents with LangGraph" course. Learn how to build sophisticated AI agents using LangGraph, LangChain, and OpenAI. [Course Link](https://learning.oreilly.com/videos/building-ai-agents/0642572077884/0642572077884-video381607/)

## Prerequisites

- Python 3.12 or higher
- Poetry (Python package manager)
- API keys for OpenAI, Tavily, and Alpha Vantage

## Installation Instructions

### 1. Install Poetry

#### Windows
```powershell
(Invoke-WebRequest -Uri https://install.python-poetry.org -UseBasicParsing).Content | python -
```

#### macOS/Linux
```bash
curl -sSL https://install.python-poetry.org | python3 -
```

After installation, make sure to add Poetry to your system's PATH. The installer will tell you where Poetry was installed.

### 2. Clone the Repository

```bash
git clone https://github.com/yourusername/building-ai-agents-with-langgraph.git
cd building-ai-agents-with-langgraph
```

### 3. Install Dependencies

```bash
poetry install
```

This will create a virtual environment and install all required dependencies specified in `pyproject.toml`.

### 4. Set Up Environment Variables

Create a `.env` file in the root directory and add your API keys:

```env
# Required API Keys
OPENAI_API_KEY=your_openai_api_key_here
TAVILY_API_KEY=your_tavily_api_key_here
ALPHAVANTAGE_API_KEY=your_alphavantage_api_key_here

# Optional: LangSmith Tracing (for debugging and monitoring)
# LANGCHAIN_TRACING_V2=true
# LANGCHAIN_ENDPOINT="https://api.smith.langchain.com"
# LANGCHAIN_API_KEY="your_langsmith_api_key_here"
# LANGCHAIN_PROJECT="your_project_name"
```

To obtain the required API keys:
- OpenAI API key: [OpenAI Platform](https://platform.openai.com/)
- Tavily API key: [Tavily AI](https://tavily.com/)
- Alpha Vantage API key: [Alpha Vantage](https://www.alphavantage.co/)

The LangSmith tracing configuration is optional but recommended for debugging and monitoring your LangChain applications.

### 5. Launch Jupyter Lab

```bash
poetry run jupyter lab
```

This will start Jupyter Lab in your default web browser. You can now access and run the notebooks.

## Project Structure

```
building-ai-agents-with-langgraph/
├── notebooks/           # Jupyter notebooks for the course
├── .env                # Environment variables (create this file)
├── pyproject.toml      # Poetry project configuration
└── README.md           # This file
```

## Dependencies

The project uses the following main dependencies:
- LangGraph (^0.2.35)
- LangChain (^0.3.3)
- LangChain OpenAI (^0.2.2)
- OpenAI (^1.51.2)
- Jupyter Lab (^4.2.5)
- Matplotlib (^3.9.2)

For a complete list of dependencies, see `pyproject.toml`.

## Troubleshooting

### Common Issues

1. **Poetry not found in PATH**
   - Windows: Add `%APPDATA%\Python\Scripts` to your PATH
   - macOS/Linux: Add `$HOME/.local/bin` to your PATH

2. **Python version mismatch**
   ```bash
   poetry env use python3.12
   ```

3. **Jupyter not launching**
   ```bash
   poetry run python -m jupyter lab
   ```

### Poetry Commands Reference

- Update dependencies: `poetry update`
- Add a new dependency: `poetry add package_name`
- Remove a dependency: `poetry remove package_name`
- Show installed packages: `poetry show`
- Run a command in the virtual environment: `poetry run command`

## License

This project is licensed under the MIT License - see the LICENSE file for details.
