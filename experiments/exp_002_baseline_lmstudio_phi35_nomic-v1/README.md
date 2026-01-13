# Experiment 002: Baseline LM Studio + Phi-3.5 + Nomic v1

**Date**: 2026-01-12
**Status**: ✅ Completed

## Configuration

- **LLM**: LM Studio Phi-3.5-mini-instruct (stored on external SSD)
- **Embedding**: nomic-embed-text v1 (768-dim, via Ollama)
- **Chunking**: 500 chars, 50 overlap
- **Data**: 3 Japanese Momotaro texts (21 chunks)

## Purpose

Baseline experiment using LM Studio with Phi-3.5 model to establish performance characteristics and compare against Ollama/Phi-3.

## Key Findings

- RAG average response: 200.4 chars
- Non-RAG average response: 276.8 chars
- RAG responses were accurate and detailed
- Non-RAG responses showed hallucinations (created fictional character: "弥三石")
- Phi-3.5 shows better instruction following compared to Phi-3

## Performance Metrics

| Metric | RAG | Non-RAG |
|--------|-----|---------|
| Avg Response Length | 200.4 chars | 276.8 chars |
| Accuracy | High ✅ | Poor ❌ |
| Hallucinations | None | Moderate |
| Detail Level | Good | Excessive |

## Comparison With

- **exp_001**: Same embedding but Ollama + Phi-3 (more concise, less detailed)
- **exp_004**: Same LLM but nomic-v2-moe embedding (planned - will test MoE embedding impact)

## Files

- **Notebook**: [notebooks/01_mvp_rag_experiment_lmstudio.ipynb](../../notebooks/01_mvp_rag_experiment_lmstudio.ipynb)
- **Results**: [results/experiment_results_lmstudio_20260112_090749.json](results/experiment_results_lmstudio_20260112_090749.json)
- **Comparative Report**: [docs/20260113_0930_MVP実験結果_OllamaとLMStudio比較.md](../../docs/20260113_0930_MVP実験結果_OllamaとLMStudio比較.md)

## Conclusions

1. **RAG is essential**: Non-RAG mode produces hallucinations, though less severe than Phi-3
2. **Phi-3.5 characteristics**:
   - More detailed responses (avg 200 chars with RAG)
   - Better context faithfulness
   - Superior instruction following
   - Somewhat verbose compared to Phi-3
3. **Setup complexity**: Requires both LM Studio and Ollama (for embeddings)

## Model Advantages

- **Better for production**: Higher accuracy and detail
- **Context handling**: Phi-3.5 shows improved ability to extract facts from context
- **Consistency**: More reliable responses across different question types

## Next Steps

- Test with nomic-embed-text-v2-moe (exp_004) to compare embedding quality
- Consider testing with different quantization levels for speed/quality trade-offs
