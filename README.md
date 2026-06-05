![caption](https://github.com/mjaroszewski1979/rag_qa_project/blob/main/rag_qa_image.jpg)

# automated-rag-qa-framework

[![QA - RAG Evaluation](https://img.shields.io/badge/QA-RAG%20Evaluation-white.svg)](#)
[![Testing Framework - Black Box](https://img.shields.io/badge/Methodology-Black%20Box%20%26%20Boundary-orange.svg)](#)
[![Status - Production Ready](https://img.shields.io/badge/Status-Completed-green.svg)](#)

A comprehensive, production-grade Quality Assurance benchmark suite designed to evaluate the factual accuracy, synthesis capabilities, and hallucination resistance of Retrieval-Augmented Generation (RAG) systems. 

This framework utilizes banking documentation (**HSBC Standard Terms and Conditions / Promo Account Regulations 2026**) as the Single Source of Truth to stress-test large language models (LLMs) deployed via NotebookLM (Gemini 1.5 Pro).

---

## Project Overview & Objective

The rapid adoption of AI assistants in banking requires rigorous validation to prevent financial and legal liabilities caused by AI hallucinations or false refusals. 

This project establishes a structured **Gold Dataset** (benchmark database) across four distinct test categories to mathematically evaluate two critical RAG metrics:
1. **Faithfulness (0/1):** Is the model's answer strictly derived from the provided context without external knowledge leakage?
2. **Answer Relevance (0/1):** Does the model directly address the user's intent without drifting or being overly conservative?

---

## Repository Structure

```text
my-rag-qa-project/
├── data/
│   └── Konto_Promocyjne_Regulamin_2026.pdf   # Source Document (Single Source of Truth)
├── test-suites/
│   ├── Cat_A_In_Context_Direct.csv           # Direct, factual retrieval test cases
│   ├── Cat_B_In_Context_Synthesis.csv        # Multi-clause logical connection cases
│   ├── Cat_C_Out_of_Context.csv              # Safe refusal & anti-hallucination verification
│   └── Cat_D_Adversarial_Boundary.csv        # Trick questions and edge cases
└── README.md                                 # Project documentation
```

## Test Suite Architecture (Gold Dataset)

The test cases are divided into a matrix designed to expose specific LLM vulnerabilities:

| Category | Test Type | Focus / Target Vulnerability | Sample Objective |
| :--- | :--- | :--- | :--- |
| **A** | In-Context Direct | Entity Extraction & Grounding | Verify exact retrieval of deadlines, interest day-counts (360/365), and addresses. |
| **B** | In-Context Synthesis | Cross-Clause Reasoning | Validate if the model can link multiple separate sections (e.g., cross-currency balance checks) to form a unified answer. |
| **C** | Out-of-Context | Hallucination Resistance | Ensure the model outputs a deterministic Safe Refusal when queries fall outside the document scope (macroeconomics, external procedures). |
| **D** | Adversarial & Boundary | Robustness & Guardrails | Stress-test the model with false presuppositions and strict boundary conditions (e.g., actions triggered at exactly 5:01 PM). |

---

## Defect Tracking & Key Insights (RCA)

During framework execution, defects are caught and logged systematically. A critical defect pattern identified during evaluation:

*   **Defect Type:** `False Negative Refusal` (Logged under `BUG-001` in execution sheets).
*   **Symptom:** When executing complex synthesis queries (e.g., cross-currency overdrawings in multi-account scenarios), the model fallback rules triggered prematurely, causing it to state *"Unfortunately, I do not have this information in my knowledge"* despite valid context being available.
*   **Root Cause Analysis (RCA):** An overly aggressive system prompt constraint meant to prevent Category C hallucinations caused the model to become "over-conservative" and reject valid multi-stage logical steps.

---

## How to Execute This Benchmark

1. **Load Knowledge Base:** Inject the PDF file from the `data/` folder into your RAG platform (e.g., Google NotebookLM / Gemini Sandbox).
2. **Deploy Prompts:** Run the inputs located in the `Prompt_Input` columns from the `test-suites/*.csv` files sequentially.
3. **Collect & Score:** Map the `Actual_Output` against the `Expected_Output`. Score `Faithfulness` and `Answer_Relevance` as binary flags (`0` or `1`).
4. **Determine Status:** Mark a test case as `PASS` only if both metrics equal `1`.
