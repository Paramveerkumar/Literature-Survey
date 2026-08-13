# A Survey of Automated Techniques for Formal Software Verification

**Authors:** Vijay D’Silva, Daniel Kroening, and Georg Weissenbacher

---

## 1. Main Objective

The paper presents a survey of **automated formal techniques for verifying software correctness**.

The main motivation is that modern software systems are becoming increasingly complex, and manual inspection and conventional testing cannot realistically examine every possible execution of a large program.

Formal verification addresses this limitation by using mathematical techniques to establish rigorous guarantees about program behavior.

The paper focuses on techniques satisfying two important criteria:

1. **Rigorous Guarantee** — The technique should provide a formal guarantee about specific classes of software errors.
2. **Automation and Scalability** — The technique should require minimal manual intervention and should be capable of handling complex software systems.

---

## 2. Three Main Techniques

The paper focuses on three major automated techniques:

```text
Automated Formal Software Verification
│
├── Abstract Static Analysis
│
├── Model Checking
│   └── Predicate Abstraction
│
└── Bounded Model Checking (BMC)
```

---

## 3. Abstract Static Analysis

**Static analysis** examines a program **without executing it**.

It computes information about possible program behavior using an approximation.

For example, instead of calculating every possible value:

```text
{0, 2, 4, 6}
```

an analysis may use an interval:

```text
[0, 6]
```

The interval representation is less precise but significantly more scalable.

### General Process

```text
Program
   ↓
Control Flow Graph (CFG)
   ↓
Information Propagation
   ↓
Join
   ↓
Iteration
   ↓
Fixed Point
   ↓
Property Checking
```

The paper explains **Abstract Interpretation** as an important mathematical foundation for static analysis.

---

## 4. Sound Over-Approximation

Static analysis generally uses a **sound approximation**.

A sound over-approximation contains all actual behaviors of the program, although it may also contain additional behaviors that cannot occur in reality.

For example, suppose the actual possible values of a variable are:

```text
{0, 2, 4}
```

An interval abstraction may represent them as:

```text
[0, 4]
```

The interval also contains values such as `1` and `3`, even if these values can never occur.

However, it does not omit the actual values:

```text
{0, 2, 4} ⊆ [0, 4]
```

This is an example of an **over-approximation**.

### Why is Over-Approximation Important?

If the abstract analysis proves that an error is impossible, and the abstraction is sound, then the conclusion can be trusted.

However, the abstraction may introduce **spurious behaviors**, which can result in false warnings.

---

## 5. False Positives and False Negatives

### Spurious Warning / False Positive

A **spurious warning** occurs when the analyzer reports a potential bug that does not actually exist.

```text
Analyzer:
"Possible division-by-zero!"

Actual Program:
No division-by-zero occurs.
```

### Missed Bug / False Negative

A **missed bug** occurs when a real error exists but the analyzer fails to report it.

```text
Actual Program:
Division-by-zero exists.

Analyzer:
"No problem found."
```

Because general static-analysis problems are subject to **undecidability**, a perfect analysis that never produces either type of error is not possible for arbitrary programs.

---

# 6. Fixed-Point Computation

A major concept in the paper is the **fixed point**.

Information is repeatedly propagated through the Control Flow Graph.

For example:

```text
{0}
 ↓
{0,2}
 ↓
{0,2,4}
 ↓
{0,2,4,6}
 ↓
...
```

The process continues until further propagation does not change the result.

That stable result is called a **fixed point**.

Mathematically, if an analysis repeatedly applies a function `F`, a fixed point `X` satisfies:

```text
F(X) = X
```

### Fixed-Point Process

```text
Initial Information
        ↓
   Propagation
        ↓
       Join
        ↓
   New Information
        ↓
   Propagation Again
        ↓
       Join
        ↓
      ...
        ↓
No More Changes
        ↓
    Fixed Point
```

Fixed-point computation is fundamental to many static-analysis and model-checking algorithms.

---

# 7. Model Checking

The second major technique is **Model Checking**.

The basic idea is to construct a model of the program and systematically explore its reachable states to determine whether a given correctness property holds.

```text
Program
   ↓
Model
   ↓
Reachable States
   ↓
Property Checking
   ↓
┌───────────────┐
│               │
▼               ▼
PASS           FAIL
│               │
▼               ▼
Verified    Counterexample
```

One of the major advantages of model checking is its ability to generate a **counterexample**.

A counterexample provides an execution trace showing how the property is violated.

---

# 8. Predicate Abstraction

Software programs usually have a very large state space.

Therefore, model checking cannot always explore the complete concrete state space directly.

The paper discusses **predicate abstraction** as a method for reducing this complexity.

Instead of representing every concrete variable value, the analysis represents important logical conditions.

For example:

```text
x > 0
x == 10
y < x
```

Instead of storing every possible value of `x` and `y`, the abstract model focuses on whether important predicates are true or false.

### Benefit

```text
Concrete Program
       ↓
Large State Space
       ↓
Predicate Abstraction
       ↓
Smaller Abstract Model
       ↓
Model Checking
```

---

# 9. Bounded Model Checking (BMC)

The third technique discussed in the paper is **Bounded Model Checking (BMC)**.

Traditional model checking attempts to explore the entire reachable state space.

BMC relaxes this requirement by exploring program behavior only up to a specified depth `k`.

```text
Initial State
     ↓
   Step 1
     ↓
   Step 2
     ↓
   Step 3
     ↓
    ...
     ↓
   Step k
     ↓
Property Violation?
```

The main question is:

> **Does a property fail within the first `k` execution steps?**

---

## 9.1 Advantage of BMC

BMC is particularly effective for finding **shallow bugs**.

For example:

```text
Bug occurs at step 10
Bound = 20
```

The bug can be detected.

BMC can also provide a concrete counterexample showing how the bug occurs.

---

## 9.2 Limitation of BMC

Suppose:

```text
Bug occurs at step 100
Bound = 20
```

Then BMC will not find the bug because the search only covers the first 20 steps.

Therefore:

> **BMC may miss bugs that require paths longer than the selected bound.**

---

# 10. Comparison of the Three Techniques

| Technique | Main Idea | Strength | Limitation |
|---|---|---|---|
| **Abstract Static Analysis** | Analyze program behavior using abstractions | Scalable and can prove absence of certain errors | May produce spurious warnings |
| **Model Checking** | Explore reachable states and check properties | Can generate counterexamples | State-space explosion |
| **Bounded Model Checking** | Explore behavior up to depth `k` | Excellent for finding shallow bugs | May miss deeper bugs |

---

# 11. State-Space Explosion

A common problem throughout the paper is **state-space explosion**.

A program may contain many:

- Variables
- Possible values
- Control locations
- Execution paths
- Function calls

Therefore, the number of possible states can become extremely large.

Conceptually:

```text
State Space
    =
Variables
×
Possible Values
×
Control Locations
×
Execution Paths
```

This makes direct exhaustive analysis computationally expensive.

### Techniques Used to Address the Problem

```text
State-Space Explosion
        │
        ├── Abstraction
        │
        ├── Predicate Abstraction
        │
        ├── Bounded Exploration
        │
        └── Efficient State Representation
```

---

# 12. Testing vs Formal Verification

One of the important messages of the paper is the difference between **testing** and **formal verification**.

## Testing

Testing checks selected executions:

```text
Input 1 → Execution 1 → PASS
Input 2 → Execution 2 → PASS
Input 3 → Execution 3 → PASS
```

Even if thousands of tests pass, there may still be an untested execution containing a bug.

---

## Formal Verification

Formal verification attempts to reason about all behaviors represented by the model:

```text
All Allowed Inputs
        ↓
All Relevant Behaviors
        ↓
Property
        ↓
┌───────────────┐
│               │
▼               ▼
PROVED       COUNTEREXAMPLE
```

Therefore, formal verification can provide a stronger guarantee than conventional testing.

---

# 13. Main Contribution of the Survey

The paper does not primarily introduce one completely new verification algorithm.

Its main contribution is a **systematic survey and tutorial** of automated formal software-verification techniques.

The paper explains:

- How the major techniques work.
- Their mathematical foundations.
- How abstraction is used.
- How large state spaces are handled.
- What types of errors can be detected.
- The advantages and limitations of each technique.
- Tools implementing these techniques.

---

# 14. Key Concepts

The most important concepts from the paper are:

```text
Abstract Interpretation
        ↓
Abstract Domains
        ↓
Sound Over-Approximation
        ↓
Control Flow Graph
        ↓
Join Operation
        ↓
Fixed-Point Computation
        ↓
Predicate Abstraction
        ↓
Model Checking
        ↓
Bounded Model Checking
        ↓
Counterexample Generation
```

---

# 15. Precision vs Scalability

One of the most important trade-offs discussed in the paper is:

> **Precision vs Scalability**

### Concrete Analysis

```text
High Precision
      ↓
Exact Values
      ↓
High Computational Cost
      ↓
Poor Scalability
```

### Abstract Analysis

```text
Approximation
      ↓
Lower Precision
      ↓
Lower Computational Cost
      ↓
Better Scalability
```

For example:

```text
Concrete:
{0, 2, 4}

        ↓ Abstraction

Abstract:
[0, 4]
```

The abstract representation is less precise but much easier to manipulate.

---

# 16. Relevance to Hardware Formal Verification

The concepts discussed in this software-verification paper are also highly relevant to **hardware formal verification**.

Hardware designs such as:

- CPUs
- GPUs
- AI accelerators
- Pipelines
- Memory systems
- Control units

also have enormous state spaces.

Therefore, similar techniques are used:

```text
Software Formal Verification
          │
          ├── Abstraction
          ├── Model Checking
          ├── Fixed-Point Analysis
          └── Counterexamples
                    │
                    ▼
        Hardware Formal Verification
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
        CPU       GPU     AI Accelerator
```

### Important Connection

The same fundamental problem appears in both software and hardware:

```text
Complex System
      ↓
Huge State Space
      ↓
State-Space Explosion
      ↓
Abstraction
      ↓
Formal Analysis
      ↓
Property Verification
```

This makes the paper useful as a theoretical foundation for studying **formal verification of AI accelerators and hardware systems**.

---

# 17. Overall Summary

The paper surveys three important automated formal-verification techniques:

```text
                 Formal Software Verification
                           │
          ┌────────────────┼────────────────┐
          │                │                │
          ▼                ▼                ▼
   Static Analysis    Model Checking      BMC
          │                │                │
    Abstraction       State Exploration   Depth k
          │                │                │
    Fixed Point       Predicate           Bounded
                     Abstraction         Exploration
          │                │                │
          └────────────────┼────────────────┘
                           ↓
                   Property Checking
                           │
                ┌──────────┴──────────┐
                ▼                     ▼
             PROVED              BUG FOUND
                                      │
                                      ▼
                               Counterexample
```

### One-Sentence Summary

> **The paper surveys automated formal techniques that analyze software without executing it, focusing on Abstract Static Analysis, Model Checking, and Bounded Model Checking, and explains how abstraction, state-space exploration, and bounded reasoning can provide rigorous guarantees while addressing the scalability challenges of complex software systems.**

---

# 18. Final Takeaway for Literature Review

For a formal-verification literature survey, the most important concepts to carry forward from this paper are:

1. **Abstract Interpretation**
2. **Abstract Domains**
3. **Sound Over-Approximation**
4. **Control Flow Graph (CFG)**
5. **Join Operation**
6. **Fixed-Point Computation**
7. **Predicate Abstraction**
8. **Model Checking**
9. **Bounded Model Checking**
10. **State-Space Explosion**
11. **Precision vs Scalability**
12. **Counterexample Generation**

These concepts provide a strong theoretical foundation for progressing from:

```text
Formal Software Verification
          ↓
Abstract Interpretation
          ↓
Model Checking
          ↓
Hardware Formal Verification
          ↓
AI Accelerator Verification
```

```
