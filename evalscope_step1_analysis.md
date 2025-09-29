# EvalScope - Step 1 Support Analysis

## Summary Table
| Process | Support Level | Evidence |
|---------|--------------|----------|
| S1P1 | 3 | Custom evaluation tasks via BenchmarkMeta, registry system, configurable metrics |
| S1P2 | 3 | Multiple evaluation methodologies: batch/online, human evaluation, arena mode |
| S1P3 | 3 | 50+ built-in benchmarks, custom dataset support, synthetic data via stress testing |
| S1P4 | 3 | Multiple model backends, API adapters, resource management, sandbox isolation |
| S1P5 | 3 | Rule-based, LLM-as-judge, human-in-loop evaluation through modular pipeline |
| S1P6 | 2 | Timeout configs, sandbox support, but limited built-in rate limiting and budget control |

## Detailed Analysis

### S1P1: Define Evaluation Tasks and Success Criteria
**Rating:** 3
**Evidence:**
- **Custom Task Definition**: Framework supports custom evaluation tasks through `BenchmarkMeta` class with configurable `name`, `dataset_id`, `tags`, `description`, `metric_list`
- **Task Registry**: Dynamic task registration system via `@register_benchmark` decorator in `evalscope/api/registry.py`
- **Success Criteria Configuration**: Multi-dimensional success criteria support:
  - Correctness metrics: accuracy, BLEU, ROUGE, exact match
  - Safety evaluation: through custom metrics and LLM judge
  - Performance: TTFT, TPOP, throughput via `evalscope perf` module
  - Retrieval quality: via RAGEval backend with MTEB/CMTEB support
- **Configurable Objectives**: Task objectives definable through:
  - Config files (YAML/JSON): `TaskConfig.from_yaml()`, `TaskConfig.from_json()`
  - Code configuration: `TaskConfig` dataclass with 40+ parameters
  - Command line: `evalscope eval --help` shows extensive CLI options
**Limitations:**
None significant - comprehensive task definition capabilities

### S1P2: Select Evaluation Methodologies
**Rating:** 3
**Evidence:**
- **Objective vs Subjective**: 
  - Objective metrics: Built-in accuracy, mathematical parsing, code execution
  - Subjective assessment: LLM-as-judge via `LLMJudge` class with customizable prompts
  - Human evaluation: Arena mode (`general_arena`) for pairwise comparisons
- **Real-time vs Batch**: 
  - Batch evaluation: Default mode via `DefaultEvaluator` class
  - Real-time evaluation: Service evaluation mode (`eval_type=service`) with API endpoints
  - Online monitoring: Performance stress testing with concurrent requests
- **Evaluation Paradigms**:
  - Classification: Multiple choice datasets (MMLU, C-Eval, ARC)
  - Generation: Text generation with configurable metrics
  - Ranking: Comparative evaluation in arena mode
  - Code execution: Sandboxed environment via `ms_enclave` integration
**Configuration Examples:**
```yaml
eval_type: service  # or checkpoint
judge_strategy: auto  # rule, single, pairwise  
eval_backend: Native  # OpenCompass, VLMEvalKit, RAGEval
```
**Limitations:**
None significant - supports major evaluation paradigms

### S1P3: Choose Appropriate Datasets and Benchmarks
**Rating:** 3
**Evidence:**
- **Built-in Benchmarks**: 50+ standard benchmarks including:
  - MMLU, CMMLU, C-Eval (knowledge)
  - GSM8K, MATH (mathematical reasoning)
  - HumanEval, LiveCodeBench (coding)
  - GPQA, SuperGPQA (advanced reasoning)
  - Custom benchmark categories: MCQ, QA, Arena, Coding, Math, Reasoning
- **Custom Dataset Support**:
  - `general_mcq`, `general_qa` adapters for custom data
  - Local dataset loading via `dataset_args.local_path`
  - Custom data adapters via `DataAdapter` inheritance
- **Synthetic Data Generation**:
  - Random dataset generation via `evalscope perf --dataset random`
  - Configurable prompt lengths and content
  - Multimodal synthetic data via `random_vl` dataset
- **Dataset Registry**: Dynamic dataset registration through `BENCHMARK_REGISTRY`
- **Flexible Loading**: Supports ModelScope Hub, HuggingFace Hub, and local datasets
**Configuration Examples:**
```python
datasets=['gsm8k', 'custom_benchmark']
dataset_args={
    'gsm8k': {'few_shot_num': 4},
    'custom_benchmark': {'local_path': '/path/to/data'}
}
```
**Limitations:**
None significant - comprehensive dataset ecosystem

### S1P4: Configure Evaluation Infrastructure  
**Rating:** 3
**Evidence:**
- **Model Backend Support**:
  - OpenAI-compatible APIs: `OpenAICompatibleAPI` class
  - Local models: `ModelScopeAPI` with transformers integration
  - Specialized APIs: Text2Image, Image editing, embedding models
  - Service evaluation: Full API compatibility with major providers
- **API/Backend Configuration**:
  - ModelScope, HuggingFace, OpenAI, Anthropic, vLLM support
  - Custom model APIs via `@register_model_api` decorator
  - Multiple evaluation backends: Native, OpenCompass, VLMEvalKit, RAGEval
- **Resource Management**:
  - Compute allocation via `device_map`, `precision` parameters
  - Batch size control: `eval_batch_size`, `generation_config.batch_size`
  - Memory optimization: Mixed precision support
- **Isolation/Sandboxing**:
  - Code execution sandbox via `ms_enclave` integration
  - Docker-based isolation: `sandbox_type: docker`
  - Local and remote sandbox managers
**Configuration Examples:**
```yaml
model_args:
  revision: master
  precision: torch.float16
  device_map: auto
use_sandbox: true
sandbox_type: docker
```
**Limitations:**
None significant - comprehensive infrastructure support

### S1P5: Design Evaluator Pipeline
**Rating:** 3
**Evidence:**
- **Rule-based Evaluators**:
  - Built-in metrics: accuracy, BLEU, ROUGE, exact match
  - Regex filters: `RegexFilter` for answer extraction
  - Mathematical parsing: `math_parser.py` for numerical answers
- **LLM-as-Judge**:
  - `LLMJudge` class with configurable prompts and scoring
  - Pattern-based and numeric scoring strategies
  - Judge model configuration via environment variables or parameters
- **Human-in-the-Loop**:
  - Arena mode: `general_arena` benchmark for human comparison
  - Custom annotation interfaces via gradio integration
  - Pairwise comparison framework
- **Custom Evaluator APIs**:
  - `DataAdapter` pipeline with hook methods
  - Modular metric system via `@register_metric`
  - Filter ensemble for output processing
- **Pipeline Configuration**:
  - Evaluation flow: load_dataset → run_inference → calculate_metrics → aggregate_scores
  - Customizable via inheritance and hook methods
**Examples:**
```python
judge_strategy: 'pairwise'
judge_model_args: {'model_id': 'Qwen/Qwen3-235B-A22B'}
metric_list: ['acc', 'Pass@1', 'llm_judge']
```
**Limitations:**
None significant - comprehensive evaluator pipeline support

### S1P6: Set up Security and Resource Constraints
**Rating:** 2
**Evidence:**
- **Timeout Configuration**:
  - Request timeouts: `timeout` parameter in `GenerateConfig`
  - Network timeouts: `connect_timeout`, `read_timeout` in stress testing
  - Task-level timeouts via `TaskConfig`
- **Resource Limits**:
  - Batch size controls: `eval_batch_size`, concurrent connection limits
  - Memory constraints: device mapping, precision settings
  - Compute allocation: GPU/CPU resource management
- **Security Features**:
  - Credential management: API key handling via environment variables
  - Sandbox isolation: Docker-based code execution via `ms_enclave`
  - Safe model loading: ModelScope hub authentication
- **Rate Limiting**: 
  - Basic rate control in stress testing via `--rate` parameter
  - Request generation control via Poisson process
  - Connection pooling and timeout management
- **Cost Tracking**:
  - Token counting via tokenizer integration
  - Request logging and monitoring
  - Performance metrics tracking
**Configuration Examples:**
```yaml
generation_config:
  timeout: 120
  batch_size: 4
use_sandbox: true
sandbox_config:
  timeout: 300
  memory_limit: "2G"
```
**Limitations:**
- No built-in budget constraints or cost limits per evaluation run
- Limited sophisticated rate limiting policies (only basic Poisson rate control)
- No native integration with cloud cost management APIs
- Manual configuration required for most resource constraints