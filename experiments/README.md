# Experiments Index

## Overview

This directory contains all RAG experiments with complete configuration and results.
Each experiment is self-contained with its own vectors, results, and documentation.

**Purpose**: Systematic comparison of LLM models and embedding models for RAG-based question answering on Momotaro (桃太郎) Japanese folktale texts.

## Active Experiments

| ID | Name | LLM | Embedding | Status | Date | Report |
|----|------|-----|-----------|--------|------|--------|
| exp_001 | baseline_ollama_phi3_nomic-v1 | Ollama phi3 | nomic-v1 | ✅ Complete | 2026-01-13 | [Report](../docs/20260113_0930_MVP実験結果_OllamaとLMStudio比較.md) |
| exp_002 | baseline_lmstudio_phi35_nomic-v1 | LM Studio phi-3.5 | nomic-v1 | ✅ Complete | 2026-01-12 | [Report](../docs/20260113_0930_MVP実験結果_OllamaとLMStudio比較.md) |
| exp_003 | moe_ollama_phi3_nomic-v2-moe | Ollama phi3 | nomic-v2-moe | 🚧 Planned | - | - |
| exp_004 | moe_lmstudio_phi35_nomic-v2-moe | LM Studio phi-3.5 | nomic-v2-moe | 🚧 Planned | - | - |

## Experiment Structure

Each experiment directory contains:

```
exp_XXX_name/
├── config.json           # Complete experiment configuration (in git)
├── README.md             # Experiment notes and findings (in git)
├── vectors/
│   ├── index.faiss       # FAISS vector index (not in git)
│   └── metadata.json     # Chunk metadata (not in git)
└── results/
    └── experiment_results_YYYYMMDD_HHMMSS.json  # Experiment results (not in git)
```

## Comparisons

### By Embedding Model

- **nomic-v1** (baseline): exp_001, exp_002
- **nomic-v2-moe** (MoE architecture): exp_003, exp_004

### By LLM Provider

- **Ollama (phi3)**: exp_001, exp_003
- **LM Studio (phi-3.5)**: exp_002, exp_004

### Pairwise Comparisons

Controlled experiments varying only one dimension:

| Comparison | Variable Changed | Experiments | Purpose |
|------------|------------------|-------------|---------|
| LLM Model | phi3 vs phi-3.5 | exp_001 vs exp_002 | Compare model generations |
| Embedding Model | v1 vs v2-moe | exp_001 vs exp_003 | Test MoE embedding impact (Ollama) |
| Embedding Model | v1 vs v2-moe | exp_002 vs exp_004 | Test MoE embedding impact (LM Studio) |
| LLM Model | phi3 vs phi-3.5 | exp_003 vs exp_004 | Compare models with MoE embeddings |

## Experiment Results Summary

### Key Findings (exp_001 vs exp_002)

1. **RAG Necessity**: Both models show severe hallucinations without RAG
2. **Model Differences**:
   - Phi-3 (exp_001): Concise (140 chars avg), fast, lightweight
   - Phi-3.5 (exp_002): Detailed (200 chars avg), better accuracy, more verbose
3. **Embedding Model**: Both use nomic-v1 (768-dim) successfully

### Performance Comparison

| Metric | exp_001 (Ollama/phi3) | exp_002 (LM Studio/phi-3.5) |
|--------|----------------------|----------------------------|
| RAG Avg Length | 140.4 chars | 200.4 chars |
| Non-RAG Avg Length | 122.4 chars | 276.8 chars |
| Setup Complexity | ✅ Simple (1 tool) | ⚠️ Medium (2 tools) |
| Response Quality | ✅ Accurate | ✅ More detailed |
| Best For | Development, prototyping | Production, high accuracy |

## Running Experiments

### Using Existing Notebooks

Notebooks are configured to use the new experiment structure:

```python
EXPERIMENT_ID = "exp_001_baseline_ollama_phi3_nomic-v1"
EXPERIMENT_PATH = PROJECT_ROOT / "experiments" / EXPERIMENT_ID
```

1. Open the notebook (see experiment README for path)
2. Verify `EXPERIMENT_ID` matches the experiment you want to run
3. Run all cells
4. Results will be saved to the experiment's `results/` directory

### Creating New Experiments

For adding new experiments (e.g., exp_005):

1. **Determine experiment ID**: Next sequential number (exp_005, exp_006, etc.)
2. **Create directory structure**:
   ```bash
   mkdir -p experiments/exp_005_description/{vectors,results}
   ```
3. **Copy and modify notebook**: Base it on similar existing experiment
4. **Update configuration**:
   - Set `EXPERIMENT_ID` in notebook
   - Modify LLM/embedding model parameters
5. **Create config.json**: Document all parameters
6. **Run experiment**: Execute notebook end-to-end
7. **Document findings**: Write experiment README.md
8. **Update this index**: Add new row to Active Experiments table

## Migration Notes

### Historical Context

This experiment-centric structure was introduced in January 2026 to replace the previous provider-based organization (`data/vectors/ollama/`, `results/comparisons/lmstudio/`).

**Old structure** (deprecated but kept for backwards compatibility):
- `data/vectors/{provider}/`
- `results/comparisons/{provider}/`

**New structure** (recommended):
- `experiments/exp_XXX_{description}/`

### Why the Change?

The old structure couldn't answer questions like:
- "Which embedding model created this index?"
- "What chunk size was used?"
- "Can I compare the same LLM across different embeddings?"

The new structure makes these questions trivial to answer by encoding all parameters in the directory name and config.json.

## Future Experiments (Ideas)

### Embedding Model Variations
- exp_005: Different embedding dimensions
- exp_006: Multilingual embeddings (EN/ZH texts)

### LLM Model Variations
- exp_007: Larger models (qwen2.5:7b, qwen3:8b)
- exp_008: Different quantization levels (Q4 vs Q5)

### Chunking Strategy Variations
- exp_009: Smaller chunks (300 chars, 25 overlap)
- exp_010: Larger chunks (1000 chars, 100 overlap)
- exp_011: Sentence-based chunking

### Language Variations
- exp_012: English Momotaro texts
- exp_013: Chinese Momotaro texts
- exp_014: Multilingual (JP+EN+ZH)

## Tools & Utilities

### Experiment Comparison (Future)

Planned utility script: `scripts/compare_experiments.py`

```bash
python scripts/compare_experiments.py exp_001 exp_002
# Output: Detailed comparison report with metrics diff
```

### Experiment Creation (Future)

Planned utility script: `scripts/create_experiment.py`

```bash
python scripts/create_experiment.py \
  --llm ollama:phi3 \
  --embedding ollama:nomic-embed-text-v2-moe \
  --description "Testing MoE embedding"
# Output: exp_003 directory created with templates
```

## Related Documentation

- [Main Project README](../README.md) - Overall project documentation
- [MVP Experiment Report](../docs/20260113_0930_MVP実験結果_OllamaとLMStudio比較.md) - exp_001 vs exp_002 detailed analysis
- [Individual Experiment READMEs](./exp_001_baseline_ollama_phi3_nomic-v1/README.md) - Detailed findings per experiment
