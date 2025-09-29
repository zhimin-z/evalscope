# EvalScope - Step 2 Support Analysis

## Summary Table
| Process | Support Level | Evidence |
|---------|--------------|----------|
| S2P1 | 2 | Supports multiple modalities (text, image, audio) with dedicated data loaders but lacks comprehensive preprocessing pipelines |
| S2P2 | 2 | Has RAG evaluation backend with embedding support and limited retrieval methods, but lacks comprehensive infrastructure |
| S2P3 | 1 | Basic vector loading through MTEB/CMTEB but limited benchmark preparation utilities |
| S2P4 | 1 | Basic model loading utilities but minimal checksum/version validation |
| S2P5 | 2 | Has testset generation for RAG scenarios and collection sampling but limited adversarial capabilities |
| S2P6 | 1 | Basic sampling with seeding but no dedicated data splitting utilities |

## Detailed Analysis

### S2P1: Load and validate datasets with modality-specific preprocessing
**Rating:** 2
**Evidence:**
- **Multi-modality support**: Framework supports text, image, and video/audio evaluation through different backends:
  - Text evaluation via `general_mcq` and `general_qa` formats (CSV/JSONL)
  - Vision-Language Models via `VLMEvalKit` backend with image support (`docs/en/advanced_guides/custom_dataset/vlm.md`)
  - Text-to-Image generation evaluation (`evalscope/models/text2image_model.py`)
  - Image editing evaluation (`evalscope/models/image_edit_model.py`)
- **Data loading capabilities**: 
  - `evalscope/utils/io_utils.py` provides utilities for JSONL, CSV, YAML, JSON formats
  - Base64 image encoding/decoding support (`PIL_to_base64`, `base64_to_PIL` functions)
  - Custom dataset adapters in `evalscope/benchmarks/data_collection/`
- **Preprocessing**:
  - Basic text preprocessing through prompt templates
  - Image preprocessing for VLM evaluation (resize, base64 encoding)
  - Limited audio/video preprocessing capabilities
**Limitations:**
- No comprehensive preprocessing pipelines for each modality
- Limited data validation beyond basic format checks
- No schema validation or quality checks mentioned in documentation

### S2P2: Build retrieval infrastructure
**Rating:** 2
**Evidence:**
- **Embedding generation**: Full embedding model support via `evalscope/backend/rag_eval/utils/embedding.py`:
  - `SentenceTransformerModel` for local embeddings
  - `APIEmbeddingModel` for OpenAI-compatible APIs
  - `CrossEncoderModel` for reranking
- **Retrieval backends**:
  - MTEB (Multilingual Task Embedding Benchmark) support via `evalscope/backend/rag_eval/cmteb/`
  - RAGAS framework integration for RAG evaluation
- **Example configuration**: `examples/example_eval_rag.py` and `examples/example_eval_mteb.py`
- **Custom retrieval tasks**: `evalscope/backend/rag_eval/cmteb/tasks/CustomTask.py`
**Limitations:**
- No built-in FAISS or ColBERT index builders
- No BM25 implementation mentioned
- Limited corpus preprocessing utilities beyond basic text handling
- No dedicated chunking strategies

### S2P3: Prepare vector search benchmarks
**Rating:** 1
**Evidence:**
- **Vector loading**: Basic support through MTEB framework
- **Custom retrieval evaluation**: Support for loading corpus, queries, and qrels files (`docs/en/advanced_guides/custom_dataset/embedding.md`)
- **Ground truth handling**: Can load relevance judgments from TSV format
- **MTEB integration**: Provides access to standard embedding benchmarks
**Limitations:**
- No pre-computed embedding loading utilities
- No kNN computation tools
- No vector normalization utilities mentioned
- No distance metric computation tools beyond what MTEB provides

### S2P4: Validate model artifacts
**Rating:** 1
**Evidence:**
- **Model loading**: Basic model loading through `evalscope/models/` and `evalscope/utils/model_utils.py`
- **Hub integration**: Supports ModelScope and HuggingFace model loading
- **Configuration handling**: Model configuration support through `TaskConfig`
**Limitations:**
- No checksum verification mentioned in code or documentation
- No version management beyond revision strings
- No model integrity checks
- No dependency validation

### S2P5: Generate evaluation scenarios
**Rating:** 2
**Evidence:**
- **Testset generation**: RAGAS integration provides testset generation from documents (`evalscope/backend/rag_eval/ragas/tasks/testset_generation.py`)
- **Collection framework**: Data mixing and sampling through `evalscope/collections/`:
  - `WeightedSampler` and `UniformSampler` for data selection
  - Hierarchical dataset organization via `CollectionSchema`
- **Scenario configuration**: Support for custom evaluation scenarios through dataset args
- **Prompt templating**: Basic prompt template system for different evaluation formats
**Limitations:**
- No dedicated adversarial input generation tools
- Limited multi-turn dialogue scenario generation
- No advanced scenario generation beyond RAGAS capabilities

### S2P6: Create deterministic data splits
**Rating:** 1
**Evidence:**
- **Seed management**: `seed_everything()` function in `evalscope/utils/model_utils.py` for reproducibility
- **Collection sampling**: Data sampling with potential for deterministic behavior through `evalscope/collections/sampler.py`
- **Dataset organization**: Support for train/dev/test splits in custom dataset formats
**Limitations:**
- No dedicated data splitting functions
- No stable identifier assignment system
- No stratified splitting strategies
- Limited reproducibility guarantees beyond basic seeding