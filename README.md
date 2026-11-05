# Arclight-FEBE-v0
Arclight-FEBE is a local-first, air-gapped tactical tool designed for field epidemiologists operating in low-connectivity or crisis environments. The architecture functions as a deterministic "semantic compiler," transforming chaotic, unstructured field notes into schema-compliant epidemiological line lists without relying on cloud APIs or risking Protected Health Information (PHI) leakage.

To achieve maximum efficiency on resource-constrained edge devices (sub-3GB RAM), this project rejects bloated general-purpose models. Instead, it utilizes a custom medical Byte-Pair Encoding (BPE) tokenizer and follows a strict, empirically driven build-and-scale methodology.

## Technical Roadmap & Scaling Strategy
This repository is organized around a scale-up-as-needed methodology. Rather than immediately training a 350m parameter model, the architecture is bounded by a hardened evaluation pipeline, ensuring that every increase in compute or parameter count is mathematically justified by the model's performance on field data.

### Phase 1: The Evaluation Pipeline
Before initializing any model weights, the ground-truth evaluation pipeline is established. This framework isolates the model's intrinsic language abilities from its functional task performance.

Syntactic Probing: Establishing baseline grammar limits.

Downstream Task Accuracy: Defining strict success metrics for the final harnesses, including Exact Match (EM) for JSON extraction and Schema Adherence Rates.

RAG Benchmarks: Evaluating retrieval accuracy and the model's fidelity to referenced public health guidelines.

### Phase 2: The 124m Baseline Build
The core engine build begins at the absolute minimum capacity required for structural template filling.

Custom Tokenization: Training the bespoke medical BPE tokenizer (16k–32k vocabulary) to prevent the subword fragmentation of complex clinical terms.

Textbook Pretraining: Training a heavily constrained, slightly over-trained 124-million parameter model from scratch, fueled exclusively by a curated, high-density domain corpus rather than noisy web scrapes.

Baseline Evaluation: Running this 124m model through the Phase 1 pipeline to establish its baseline semantic extraction metrics.

### Phase 3: Application Harness & Experimentation
With the 124m baseline established, the model is wrapped in deterministic execution control layers (harnesses) to test its capabilities.

Prompt Engineering & Structural Masks: Testing zero-shot and few-shot extraction compliance utilizing constrained decoding (like GBNF or JSON masking) to physically restrict token probabilities into valid schemas.

Local RAG Integration: Implementing a simple offline vector retrieval harness (via local DuckDB or SQLite) to test if the 124m parameter footprint can process retrieved clinical guidelines without treating the context as disruptive noise.

### Phase 4: Empirical Scaling & Specialization
Based on the evaluation pipeline results from Phase 3, the architecture will fork down one of two targeted optimization paths to finalize the deployment:

Path A (Targeted Fine-Tuning): If the 124m model demonstrates sufficient baseline reasoning and RAG processing, the architecture remains at 124m. The weights are fine-tuned on instruction-response pairs specific to end-use tasks (like line-list extraction or syndromic classification).

Path B (The 350m Architectural Rebuild): If the 124m model hits a "factual emptiness" ceiling or fails at multi-hop RAG reasoning, the architecture undergoes a ground-up rebuild scaled to ~350-million parameters. This larger hidden dimension and increased circuit depth unlock the algorithmic expressivity required to process complex overlapping symptoms and case definitions.

The documentation and architecture decision records (ADRs) detailing these milestones will be updated in the repository as the baseline evaluation metrics are captured.
