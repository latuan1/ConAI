# ConAI-Bench

ConAI-Bench is a curated dataset designed for fine-tuning Small Language
Models (SLMs) to generate C++ test inputs from partial methods.\
It is introduced in the paper:

**Combining Concolic Testing with Small Language Model to Generate Test
Data for C++ with Limited Training Set**\
VNU University of Engineering and Technology, 2026.

------------------------------------------------------------------------

## 1. Overview

ConAI-Bench supports research on:

-   Test data generation for C++
-   Hybrid Concolic Testing + AI approaches
-   Fine-tuning language models
-   Coverage-guided automated testing

Unlike traditional datasets that store full test drivers, ConAI-Bench
uses a compact **key-value representation** of test inputs, reducing
token length and making it suitable for small models.

------------------------------------------------------------------------

## 2. Dataset Format

Each sample follows a unified structure:

(Source, Target)

### 2.1 Source

The Source represents the prompt input to the SLM.

Structure:
```cpp
Generate test data to maximize statement coverage for the partial
method.

/*FC*/ class {FocalClass} {
    /*PM*/ {partial method}
    /*F*/  {used fields}
    /*C*/  {related constructors}
    /*M*/  {methods called by the partial method}
};
```

Components:

-   Instruction
-   Focal class
-   Partial method (derived from a specific test path)
-   Used fields
-   Constructors
-   Methods invoked by the partial method

The partial method contains only statements executed along one test
path, reducing noise and token length.

------------------------------------------------------------------------

### 2.2 Target

The Target encodes test input using a JSON-style key-value format.

Example:
```cpp
{
    "nums": [3],
    "nums_element0": ["1"],
    "nums_element1": ["3"],
    "nums_element2": ["2"] 
}
```
Mapping rules:

-   Primitive → "x": "10"
-   STL container → "nums": \[size\] + indexed elements
-   User-defined type → "obj": \["ClassName", "ConstructorSig"\]

Advantages:

-   \~60% token reduction compared to test drivers
-   Framework-independent
-   Easily convertible into executable C++ test drivers

------------------------------------------------------------------------

## 3. Dataset Splits

The dataset is carefully split to avoid data contamination.

### TrainP

-   2,712 samples
-   Used for SLM fine-tuning
-   Contains partial methods only

### TestP

-   250 samples
-   Used to evaluate SLM generation quality
-   No focal method overlap with training set

All samples derived from the same focal method are assigned to the same
split.

------------------------------------------------------------------------

## 4. Data Collection Pipeline

The dataset was constructed through four stages:

### Step 1 -- Data Collection

-   19 open-source GitHub repositories
-   C++ LeetCode solutions
-   Focus on primitive types and STL containers
-   Minimal external dependencies

### Step 2 -- Test Data Generation

-   Collect public LeetCode test cases
-   Manually generate additional cases to cover uncovered branches

### Step 3 -- Preprocessing & Normalization

-   Extract partial methods from execution paths
-   Convert to unified (Source, Target) format
-   Remove duplicate samples, conflicting targets, overlength samples,
    syntax errors, and runtime failures

### Step 4 -- Leak-Aware Splitting

-   Cluster samples by focal method
-   Ensure balanced token length, balanced data type distribution, and
    zero overlap across splits

------------------------------------------------------------------------

## 5. Dataset Statistics

Input Type Distribution:

| Type         | TrainP  | TestP   |
|--------------|---------|---------|
| Primitive    | 34.63%  | 26.85%  |
| STL          | 54.56%  | 64.96%  |
| User-defined | 10.81%  | 8.19%   |

------------------------------------------------------------------------

## 6. Intended Use

ConAI-Bench is intended for:

-   Fine-tuning small language models (e.g., CodeT5+, T5 variants)
-   Evaluating test data generation quality
-   Research on coverage-guided testing
-   Hybrid Concolic + AI approaches
-   Low-resource fine-tuning

------------------------------------------------------------------------

## 7. Citation

If you use ConAI-Bench in your research, please cite:
```cpp
@article{conai2026, 
    title={Combining Concolic Testing with Small Language Model to Generate Test Data for C++ with Limited Training Set},
    author={Tran, Linh Ngoc Truc and Luong Anh, Tuan and Le Khanh, Trinh and Nguyen, Duc-Anh and Pham Ngoc, Hung}, year={2026}
}
```
