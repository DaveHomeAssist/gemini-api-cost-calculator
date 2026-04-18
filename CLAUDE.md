# CLAUDE.md

Inherits root rules from `/Users/daverobertson/Desktop/Code/AGENTS.md`.

## Project Overview

Static cost comparison calculator for LLM API pricing. Compares Gemini, Claude, GPT, Llama, Mistral, DeepSeek, and Qwen models. Users input token counts, requests per day, and optional batch/caching settings to see price breakdowns across providers.

## Stack

- Static HTML + inline CSS + inline JS (single file in docs/)
- GitHub Pages hosting (served from docs/ directory)
- No build step, no bundler, no framework

## Key Decisions

- Single file architecture for zero dependency deployment
- docs/ directory used as GitHub Pages root
- Model pricing data is hardcoded; needs manual updates when providers change rates

## Documentation Maintenance

- **Issues**: Track in the issue tracker table below
- **Session log**: Append to `/Users/daverobertson/Desktop/Code/95-docs-personal/today.csv` after each meaningful change

## Issue Tracker

| ID | Severity | Status | Title | Notes |
|----|----------|--------|-------|-------|

## Session Log

[2026-03-18] [LLMCalc] [docs] Add AGENTS baseline
