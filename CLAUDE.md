# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is the **Hello-Agents course materials** repository — a 15-chapter progressive tutorial on building AI agents. It contains standalone Python scripts (chapters 1–12) and full-stack web applications (chapters 13–15), all built around the [HelloAgents framework](https://github.com/jjyaoao/HelloAgents). The owner's primary language is Chinese; comments, docs, and READMEs are in Chinese.

## Repository Structure

```
deep_learning/
├── .venv/                        # Shared Python 3.12 virtual environment
├── agent_memory_management.md    # Three-layer memory architecture design doc
└── hello-agents/code/
    ├── chapter01/   # First agent (notebook + script)
    ├── chapter02/   # ELIZA chatbot
    ├── chapter03/   # NLP fundamentals (BPE, N-gram, Transformer, Word2Vec, Qwen)
    ├── chapter04/   # Agent reasoning (ReAct, Plan-and-Solve, Reflection)
    ├── chapter05/   # (empty placeholder)
    ├── chapter06/   # Multi-agent frameworks (AgentScope, AutoGen, CAMEL, LangGraph)
    ├── chapter07/   # Agent testing (6 test files + modules)
    ├── chapter08/   # Memory & RAG (11 numbered scripts)
    ├── chapter09/   # Context engineering & tools
    ├── chapter10/   # MCP protocol (20+ scripts + weather-mcp-server/)
    ├── chapter11/   # Model fine-tuning (LoRA, SFT, GRPO + DeepSpeed configs)
    ├── chapter12/   # Agent evaluation (BFCL, GAIA, data generation)
    ├── chapter13/   # Full-stack: Trip Planner (FastAPI + Vue3 + Amap MCP)
    ├── chapter14/   # Full-stack: Deep Research (FastAPI + Vue3 + Tavily/DuckDuckGo)
    └── chapter15/   # Full-stack: AI Town NPC simulation (FastAPI + Godot 4.x)
```

## Environment Setup

The repo uses a shared virtual environment at `.venv/` (Python 3.12):

```bash
source .venv/bin/activate
```

Most chapters are standalone scripts — run directly with `python <filename>.py`. No global build system or test runner exists.

## Per-Chapter Commands

### Chapters 1–12 (standalone scripts)

```bash
cd hello-agents/code/chapter<N>
python <script>.py
```

Scripts are numbered sequentially (e.g., `01_basic_agent_example.py`, `02_bfcl_quick_start.py`) matching the textbook order.

### Chapter 7 (tests)

```bash
cd hello-agents/code/chapter07
python test_react_agent.py        # Individual test scripts
python test_simple_agent.py
```

### Chapter 11 (model fine-tuning)

Uses `accelerate` with DeepSpeed configs in `accelerate_configs/`:

```bash
cd hello-agents/code/chapter11
python 00_quick_test.py                          # Verify setup
python 04_sft_training.py                        # SFT training
python 05_grpo_training.py                       # GRPO training
accelerate launch --config_file accelerate_configs/deepspeed_zero2.yaml 08_distributed_training.py
```

### Chapter 12 (evaluation)

```bash
pip install hello-agents[evaluation]==0.2.3
cd hello-agents/code/chapter12
python 07_data_generation_complete_flow.py 30 3.0   # args: num_questions delay_seconds
```

### Chapter 13 (Trip Planner — full-stack)

```bash
# Backend
cd hello-agents/code/chapter13/helloagents-trip-planner/backend
pip install -r requirements.txt
cp .env.example .env  # fill in API keys (Amap, LLM)
uvicorn app.api.main:app --reload --host 0.0.0.0 --port 8000

# Frontend
cd ../frontend
npm install
cp .env.example .env  # fill in Amap JS API key
npm run dev            # http://localhost:5173
```

### Chapter 14 (Deep Research — full-stack)

```bash
# Backend (uses uv/pyproject.toml)
cd hello-agents/code/chapter14/helloagents-deepresearch/backend
uv sync
cp .env.example .env
uv run python -m src.main

# Frontend
cd ../frontend
npm install
npm run dev
```

### Chapter 15 (AI Town — Godot + FastAPI)

```bash
cd hello-agents/code/chapter15/Helloagents-AI-Town/backend
pip install -r requirements.txt
# Game client requires Godot 4.x editor
```

## Architecture Notes

**HelloAgents framework** (`hello-agents` package) is the common dependency across chapters:
- `SimpleAgent` — base agent class used in chapters 10, 13, 14
- `MCPTool` — wraps MCP servers as agent tools (chapter 10, 13)
- `HelloAgentsLLM` — LLM abstraction supporting OpenAI, DeepSeek, etc.
- Evaluation tools: `BFCLTool`, `GAIATool` (chapter 12)

**Full-stack apps (ch13–15)** share a common architecture:
- Backend: FastAPI with `/api/` routes, `services/` layer, `models/schemas.py` for Pydantic models
- Frontend: Vue 3 + TypeScript + Vite, API service layer in `src/services/api.ts`
- Agent integration: Backend creates HelloAgents `SimpleAgent` with MCP tools, exposes via REST endpoints

**Chapter 6** has four independent sub-projects (AgentScope, AutoGen, CAMEL, LangGraph), each with its own `requirements.txt`.

## API Keys Required

Most chapters need LLM API keys. Full-stack apps need additional service keys:
- **Ch13**: Amap (高德地图) Web API key + JS API key
- **Ch14**: Tavily API key (web search)
- **Ch15**: OpenAI API key (GPT-4 for NPC dialogue)

All configured via `.env` files — see `.env.example` in each project.
