# TradingAgents User Manual

## Overview

TradingAgents is a multi-agent LLM financial trading framework that simulates real-world trading firms. It employs specialized LLM-powered agents (analysts, researchers, traders, risk managers) that collaboratively analyze market conditions and make trading decisions.

### Key Features
- **Multi-Agent Architecture**: Specialized agents for different financial analysis tasks
- **Multi-LLM Provider Support**: OpenAI, Google, Anthropic, xAI, DeepSeek, Ollama, and more
- **Flexible Data Sources**: yFinance (default) and Alpha Vantage APIs
- **Interactive CLI**: User-friendly terminal interface
- **Docker Support**: Containerized deployment options
- **Research-Focused**: Designed for financial research and experimentation

---

## Table of Contents
1. [System Requirements](#system-requirements)
2. [Installation Methods](#installation-methods)
   - [Method 1: Local Python Installation](#method-1-local-python-installation)
   - [Method 2: Docker Installation](#method-2-docker-installation)
   - [Method 3: Development Installation](#method-3-development-installation)
3. [Configuration & API Setup](#configuration--api-setup)
   - [LLM Provider Configuration](#llm-provider-configuration)
   - [Data Vendor Configuration](#data-vendor-configuration)
   - [Environment Variables](#environment-variables)
4. [Running TradingAgents](#running-tradingagents)
   - [Using the CLI](#using-the-cli)
   - [Python API Usage](#python-api-usage)
   - [Docker Usage](#docker-usage)
5. [Usage Examples](#usage-examples)
   - [Basic Stock Analysis](#basic-stock-analysis)
   - [Custom Configuration](#custom-configuration)
   - [Advanced Features](#advanced-features)
6. [Project Structure](#project-structure)
7. [Troubleshooting](#troubleshooting)
8. [FAQ](#faq)

---

## System Requirements

### Minimum Requirements
- **Python**: 3.10 or higher (3.13 recommended)
- **RAM**: 8GB minimum, 16GB recommended
- **Storage**: 2GB free space
- **Internet Connection**: Required for API calls and data fetching

### Recommended Environment
- **Operating System**: Linux, macOS, or Windows (WSL2 recommended for Windows)
- **Python Environment Manager**: Conda, venv, or uv
- **LLM API Access**: At least one LLM provider API key

---

## Installation Methods

### Method 1: Local Python Installation

#### Step 1: Clone the Repository
```bash
git clone https://github.com/TauricResearch/TradingAgents.git
cd TradingAgents
```

#### Step 2: Create Virtual Environment
Using Conda (recommended):
```bash
conda create -n tradingagents python=3.13
conda activate tradingagents
```

Using Python venv:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

#### Step 3: Install Dependencies
```bash
pip install .
```

This will install all required packages listed in `pyproject.toml`.

#### Step 4: Verify Installation
```bash
tradingagents --help
```
You should see the CLI help menu.

### Method 2: Docker Installation

#### Step 1: Clone the Repository
```bash
git clone https://github.com/TauricResearch/TradingAgents.git
cd TradingAgents
```

#### Step 2: Configure Environment
```bash
cp .env.example .env
# Edit .env file with your API keys
```

#### Step 3: Run with Docker Compose
```bash
docker compose run --rm tradingagents
```

#### Step 4: For Local Models (Ollama)
```bash
docker compose --profile ollama run --rm tradingagents-ollama
```

### Method 3: Development Installation

For contributors or those wanting to modify the code:

```bash
git clone https://github.com/TauricResearch/TradingAgents.git
cd TradingAgents
python -m venv venv
source venv/bin/activate
pip install -e ".[dev]"  # If dev dependencies are defined
```

---

## Configuration & API Setup

### LLM Provider Configuration

TradingAgents supports multiple LLM providers. You need at least one API key:

#### 1. OpenAI (GPT models)
```bash
export OPENAI_API_KEY="your-openai-api-key"
```

#### 2. Google (Gemini models)
```bash
export GOOGLE_API_KEY="your-google-api-key"
```

#### 3. Anthropic (Claude models)
```bash
export ANTHROPIC_API_KEY="your-anthropic-api-key"
```

#### 4. Other Providers
```bash
export XAI_API_KEY="your-xai-api-key"           # xAI (Grok)
export DEEPSEEK_API_KEY="your-deepseek-api-key" # DeepSeek
export DASHSCOPE_API_KEY="your-dashscope-key"   # Qwen (Alibaba)
export ZHIPU_API_KEY="your-zhipu-api-key"       # GLM (Zhipu)
export OPENROUTER_API_KEY="your-openrouter-key" # OpenRouter
```

#### 5. Local Models (Ollama)
```bash
# Install Ollama first: https://ollama.com/
ollama pull llama3.2:latest
# Then set in config: "llm_provider": "ollama"
```

### Data Vendor Configuration

By default, TradingAgents uses yFinance which requires no API key. For Alpha Vantage:

```bash
export ALPHA_VANTAGE_API_KEY="your-alpha-vantage-key"
```

### Environment Variables

Create a `.env` file in the project root:

```bash
cp .env.example .env
```

Edit `.env` with your API keys:
```env
# LLM Providers (set the one you use)
OPENAI_API_KEY=sk-...
GOOGLE_API_KEY=...
ANTHROPIC_API_KEY=...
XAI_API_KEY=...
DEEPSEEK_API_KEY=...
DASHSCOPE_API_KEY=...
ZHIPU_API_KEY=...
OPENROUTER_API_KEY=...

# Data Providers
ALPHA_VANTAGE_API_KEY=...
```

### Enterprise Configuration

For Azure OpenAI, AWS Bedrock, or other enterprise providers:

```bash
cp .env.enterprise.example .env.enterprise
```

Edit `.env.enterprise` with your enterprise credentials.

---

## Running TradingAgents

### Using the CLI

The CLI provides an interactive interface for running analyses:

#### Start the CLI
```bash
tradingagents
```
or
```bash
python -m cli.main
```

#### CLI Workflow
1. **Select Tickers**: Choose one or more stock symbols (e.g., NVDA, AAPL, TSLA)
2. **Choose Analysis Date**: Select historical date for analysis
3. **Select LLM Provider**: Choose from available LLM providers
4. **Configure Research Depth**: Set debate rounds and analysis depth
5. **Run Analysis**: Watch real-time progress as agents work
6. **View Results**: See final trading decision and reports

#### CLI Screens
- **Initial Screen**: Configuration selection
- **Progress Screen**: Real-time agent activity tracking
- **Results Screen**: Final decision and detailed reports
- **Transaction Screen**: Proposed trades and risk assessment

### Python API Usage

For programmatic usage or integration into other systems:

#### Basic Example
```python
from tradingagents.graph.trading_graph import TradingAgentsGraph
from tradingagents.default_config import DEFAULT_CONFIG

# Create a custom config
config = DEFAULT_CONFIG.copy()
config["llm_provider"] = "openai"
config["deep_think_llm"] = "gpt-5.4"
config["quick_think_llm"] = "gpt-5.4-mini"
config["max_debate_rounds"] = 2

# Initialize the framework
ta = TradingAgentsGraph(debug=True, config=config)

# Run analysis for a specific stock and date
_, decision = ta.propagate("NVDA", "2024-05-10")
print(f"Final Decision: {decision}")
```

#### Advanced Configuration
```python
from tradingagents.graph.trading_graph import TradingAgentsGraph
from tradingagents.default_config import DEFAULT_CONFIG

config = DEFAULT_CONFIG.copy()

# LLM Settings
config["llm_provider"] = "google"
config["deep_think_llm"] = "gemini-3.1-pro-preview"
config["quick_think_llm"] = "gemini-3-flash-preview"
config["google_thinking_level"] = "high"

# Data Vendor Settings
config["data_vendors"] = {
    "core_stock_apis": "alpha_vantage",      # Use Alpha Vantage for price data
    "technical_indicators": "yfinance",      # Use yFinance for indicators
    "fundamental_data": "alpha_vantage",     # Use Alpha Vantage for fundamentals
    "news_data": "yfinance",                 # Use yFinance for news
}

# Language and Output Settings
config["output_language"] = "English"  # Options: English, Chinese, Japanese, etc.
config["max_debate_rounds"] = 3
config["max_risk_discuss_rounds"] = 2

# Initialize with custom config
ta = TradingAgentsGraph(
    selected_analysts=["market", "fundamentals", "news", "social"],
    debug=True,
    config=config
)

# Run analysis
results, decision = ta.propagate("AAPL", "2024-12-01")
print(f"Analysis Results: {results}")
print(f"Trading Decision: {decision}")
```

### Docker Usage

#### Basic Docker Run
```bash
# Build and run
docker compose run --rm tradingagents

# With custom environment file
docker compose --env-file .env.custom run --rm tradingagents
```

#### Persistent Data Volume
```bash
# Data will persist in Docker volume
docker compose run --rm -v tradingagents_data:/home/appuser/.tradingagents tradingagents
```

#### Development with Docker
```bash
# Build image
docker build -t tradingagents:dev .

# Run with shell access
docker run -it --rm \
  -v $(pwd):/app \
  -v tradingagents_data:/home/appuser/.tradingagents \
  --env-file .env \
  tradingagents:dev /bin/bash
```

---

## Usage Examples

### Basic Stock Analysis

#### Example 1: Single Stock Analysis
```python
from tradingagents.graph.trading_graph import TradingAgentsGraph
from tradingagents.default_config import DEFAULT_CONFIG

config = DEFAULT_CONFIG.copy()
ta = TradingAgentsGraph(config=config)

# Analyze NVIDIA on a specific date
results, decision = ta.propagate("NVDA", "2024-05-10")
print(f"Decision for NVDA: {decision}")
```

#### Example 2: Multiple Stocks
```python
stocks = ["AAPL", "MSFT", "GOOGL", "AMZN"]
date = "2024-06-15"

for stock in stocks:
    _, decision = ta.propagate(stock, date)
    print(f"{stock}: {decision}")
```

#### Example 3: Custom Date Range Analysis
```python
import pandas as pd
from datetime import datetime, timedelta

start_date = datetime(2024, 1, 1)
end_date = datetime(2024, 3, 31)

current_date = start_date
while current_date <= end_date:
    date_str = current_date.strftime("%Y-%m-%d")
    _, decision = ta.propagate("TSLA", date_str)
    print(f"{date_str}: {decision}")
    current_date += timedelta(days=7)  # Weekly analysis
```

### Custom Configuration

#### Example: Using Different LLMs for Different Tasks
```python
config = DEFAULT_CONFIG.copy()
config["llm_provider"] = "openai"
config["deep_think_llm"] = "gpt-5.4"        # For complex reasoning
config["quick_think_llm"] = "gpt-5.4-mini"  # For quick tasks
config["openai_reasoning_effort"] = "high"  # Maximum reasoning effort
```

#### Example: Multi-Language Output
```python
config = DEFAULT_CONFIG.copy()
config["output_language"] = "Chinese"  # Output reports in Chinese

ta = TradingAgentsGraph(config=config)
_, decision = ta.propagate("BABA", "2024-08-01")
# Reports will be in Chinese, internal reasoning stays in English
```

### Advanced Features

#### Memory and Reflection
```python
# After running trades, reflect on performance
ta.reflect_and_remember(1000)  # Parameter is position returns

# The system learns from past trades for future decisions
```

#### Custom Agent Selection
```python
# Select specific analysts to include
ta = TradingAgentsGraph(
    selected_analysts=["fundamentals", "technical"],  # Only these analysts
    config=config
)
```

#### Debug Mode
```python
ta = TradingAgentsGraph(debug=True, config=config)
# Enables detailed logging of agent interactions and decisions
```

---

## Project Structure

```
TradingAgents/
├── tradingagents/           # Core framework
│   ├── agents/             # All agent implementations
│   │   ├── analysts/       # Fundamentals, market, news, technical analysts
│   │   ├── researchers/    # Bull/bear researchers
│   │   ├── risk_mgmt/      # Risk management debaters
│   │   ├── trader/         # Trader agent
│   │   └── utils/          # Agent utilities and tools
│   ├── dataflows/          # Data vendor interfaces
│   │   ├── y_finance.py    # yFinance integration
│   │   ├── alpha_vantage.py # Alpha Vantage integration
│   │   └── interface.py    # Unified data interface
│   ├── graph/              # LangGraph orchestration
│   │   ├── trading_graph.py # Main graph class
│   │   ├── propagation.py  # Graph propagation logic
│   │   └── reflection.py   # Learning from past trades
│   └── llm_clients/        # Multi-provider LLM support
│       ├── factory.py      # LLM client factory
│       ├── openai_client.py
│       ├── google_client.py
│       └── anthropic_client.py
├── cli/                    # Command-line interface
│   ├── main.py            # CLI entry point
│   ├── config.py          # CLI configuration
│   └── static/            # CLI assets
├── tests/                  # Test suite
├── main.py                # Example usage script
├── pyproject.toml         # Project dependencies
├── docker-compose.yml     # Docker configuration
├── Dockerfile            # Docker build file
└── README.md             # Project documentation
```

---

## Troubleshooting

### Common Issues and Solutions

#### Issue 1: "No API key provided" Error
**Solution**: Ensure your API key is set in environment variables:
```bash
# Check if variable is set
echo $OPENAI_API_KEY

# Set it if not
export OPENAI_API_KEY="your-key-here"
# Or add to .env file and load it
```

#### Issue 2: "Module not found" Errors
**Solution**: Reinstall the package:
```bash
pip install --upgrade .
# Or for development
pip install -e .
```

#### Issue 3: Docker Container Fails to Start
**Solution**: Check Docker logs and permissions:
```bash
# View logs
docker compose logs tradingagents

# Rebuild image
docker compose build --no-cache

# Check volume permissions
docker volume ls
```

#### Issue 4: Rate Limiting Errors
**Solution**: Configure rate limits or switch data vendors:
```python
config["data_vendors"] = {
    "core_stock_apis": "yfinance",  # yFinance has no rate limits
    # ... other categories
}
```

#### Issue 5: LLM Timeout Errors
**Solution**: Increase timeout or use faster models:
```python
config["quick_think_llm"] = "gpt-5.4-mini"  # Faster model for quick tasks
```

### Debug Mode

Enable debug mode for detailed logging:
```python
ta = TradingAgentsGraph(debug=True, config=config)
```

Or via CLI:
```bash
tradingagents --debug
```

### Log Files

Check log files for detailed error information:
- Application logs: `~/.tradingagents/logs/`
- Cache directory: `~/.tradingagents/cache/`

---

## FAQ

### Q1: Is TradingAgents for live trading?
**A**: No, TradingAgents is a research framework for simulating and analyzing trading decisions. It's not connected to live trading exchanges and should not be used for actual trading without proper risk management systems.

### Q2: Which LLM provider is recommended?
**A**: It depends on your needs:
- **OpenAI GPT-5.4**: Best overall performance
- **Google Gemini 3.1**: Good for financial reasoning
- **Anthropic Claude 4.6**: Excellent for complex analysis
- **Ollama (local)**: For privacy and cost control

### Q3: How much does it cost to run?
**A**: Costs depend on:
- LLM API usage (varies by provider and model)
- Data API usage (Alpha Vantage has limits, yFinance is free)
- Typically $1-10 per analysis depending on depth

### Q4: Can I add custom agents?
**A**: Yes, the framework is modular. You can:
1. Create new agent types in `tradingagents/agents/`
2. Register them in the graph setup
3. Configure them through the `selected_analysts` parameter

### Q5: How accurate are the trading decisions?
**A**: TradingAgents is a research tool. Accuracy depends on:
- Quality of input data
- LLM model capabilities
- Market conditions
- Configuration settings
- Always validate decisions with traditional analysis

### Q6: Can I backtest strategies?
**A**: Yes, by running analyses on historical dates:
```python
# Analyze multiple historical dates
dates = ["2024-01-01", "2024-01-08", "2024-01-15", "2024-01-22"]
decisions = {}

for date in dates:
    _, decision = ta.propagate("SPY", date)
    decisions[date] = decision
    print(f"{date}: {decision}")

# Calculate performance metrics
print(f"\nBacktest Results: {decisions}")
```

### Q7: How do I contribute to the project?
**A**: TradingAgents is open source! You can:
1. Fork the repository on GitHub
2. Submit bug reports or feature requests
3. Contribute code improvements
4. Improve documentation
5. Join the Tauric Research community

### Q8: Where can I get support?
**A**:
- **GitHub Issues**: For bugs and feature requests
- **Discord Community**: Join the TradingResearch Discord
- **Documentation**: Read the README and this manual
- **Academic Paper**: Refer to arXiv:2412.20138 for technical details

---

## Additional Resources

### Official Links
- **GitHub Repository**: https://github.com/TauricResearch/TradingAgents
- **Documentation**: https://tauric.ai/tradingagents
- **Research Paper**: https://arxiv.org/abs/2412.20138
- **Discord Community**: https://discord.com/invite/hk9PGKShPK

### Learning Resources
- **Video Demo**: https://www.youtube.com/watch?v=90gr5lwjIho
- **Example Notebooks**: Check the `examples/` directory (if available)
- **API Reference**: Generated from source code documentation

### Related Projects
- **Trading-R1**: Terminal implementation of trading research
- **Tauric Research**: Open-source financial AI research community
- **LangGraph**: Underlying orchestration framework

---

## Disclaimer

**Important**: TradingAgents is a research framework for academic and experimental purposes only.

1. **Not Financial Advice**: The framework does not provide financial, investment, or trading advice.
2. **Simulation Only**: All trading decisions are simulated and not connected to live markets.
3. **Risk of Loss**: Trading involves substantial risk of loss and is not suitable for all investors.
4. **Performance Not Guaranteed**: Past performance does not guarantee future results.
5. **Research Purpose**: Use at your own risk for research and educational purposes only.

For the full disclaimer, visit: https://tauric.ai/disclaimer/

---

## Version Information

This manual covers TradingAgents v0.2.3 (March 2026). Check for updates:
```bash
pip show tradingagents
```

Or visit the GitHub releases page for the latest version and changelog.

---

## Conclusion

TradingAgents provides a powerful framework for exploring multi-agent LLM systems in financial markets. With its modular architecture, multi-provider support, and research-focused design, it enables sophisticated analysis of trading decisions while maintaining flexibility for experimentation.

Remember to:
1. Start with simple configurations
2. Monitor API usage and costs
3. Validate results with traditional methods
4. Contribute back to the open-source community

Happy researching!

*Last Updated: April 2026*