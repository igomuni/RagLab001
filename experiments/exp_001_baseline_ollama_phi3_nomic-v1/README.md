# Experiment 001: Baseline Ollama + Phi3 + Nomic v1

**Date**: 2026-01-13
**Status**: ✅ Completed

## Configuration

- **LLM**: Ollama phi3 (2.2GB)
- **Embedding**: nomic-embed-text v1 (768-dim)
- **Chunking**: 500 chars, 50 overlap
- **Data**: 3 Japanese Momotaro texts (21 chunks)

## Purpose

Baseline experiment to establish RAG vs Non-RAG performance with standard models.

## Key Findings

- RAG average response: 140.4 chars
- Non-RAG average response: 122.4 chars
- RAG responses were accurate and grounded in source material
- Non-RAG responses showed severe hallucinations (created fictional characters: "姉さん", "明")

## Performance Metrics

| Metric | RAG | Non-RAG |
|--------|-----|---------|
| Avg Response Length | 140.4 chars | 122.4 chars |
| Accuracy | High ✅ | Poor ❌ |
| Hallucinations | None | Severe |

## Comparison With

- **exp_002**: Same config but LM Studio + Phi-3.5 (more detailed responses, better instruction following)
- **exp_003**: Same LLM but nomic-v2-moe embedding (planned - will test MoE embedding impact)

## Files

- **Notebook**: [notebooks/01_mvp_rag_experiment_ollama.ipynb](../../notebooks/01_mvp_rag_experiment_ollama.ipynb)
- **Results**: [results/experiment_results_20260113_092450.json](results/experiment_results_20260113_092450.json)
- **Comparative Report**: [docs/20260113_0930_MVP実験結果_OllamaとLMStudio比較.md](../../docs/20260113_0930_MVP実験結果_OllamaとLMStudio比較.md)

## Conclusions

1. **RAG is essential**: Non-RAG mode is unusable due to severe hallucinations
2. **Phi-3 characteristics**:
   - Concise responses (avg 140 chars with RAG)
   - Tends to over-interpret context occasionally
   - Fast and lightweight
3. **Setup simplicity**: Single tool (Ollama) makes this the easiest configuration

## Next Steps

- Test with nomic-embed-text-v2-moe (exp_003) to compare embedding quality
- Consider trying larger LLM models (qwen2.5:7b) for comparison
