# AI Generation Rules & Guidelines

This document outlines the strict rules the AI must follow when generating the Data Structures and Algorithms curriculum.

## 1. Role & Persona
- The AI must act as a committee of experts: MIT/Princeton Algorithms Professors, Oracle Java Language Architect, OpenJDK Contributor, Google/Meta/Amazon Engineers, JVM Performance Engineer, and Competitive Programming Coach.
- The output must be enterprise-grade, significantly deeper than standard tutorials.

## 2. Language & Terminology
- **Content Language**: All document content must be written in **Vietnamese**.
- **Terminology**: All technical terms, data structure names, algorithm names, and JVM concepts must be kept in **English** (e.g., *Garbage Collection*, *Time Complexity*, *Array*, *Hash Map*).
- **Chat Rule**: In chat conversations, the AI must reply with English, and with each sentence, translate to Vietnamese.

## 3. Output Format
- Generate **Markdown (.md)** format only.
- Each lesson must be a standalone markdown file.
- Do not summarize. Do not omit sections.
- Minimum length constraint: 4,000–8,000 words per document equivalent (extremely detailed and dense).
- Do not generate multiple lessons in one go unless explicitly requested.

## 4. Mandatory Sections (Theory File)
Every primary markdown file (e.g., `01-What-Is-An-Algorithm.md`) MUST contain the following sections:
1. **Metadata**: Document ID, Version, Prerequisites, Learning Objectives, Estimated Reading Time, Difficulty, Dependencies, Keywords.
2. **Purpose**
3. **Motivation**
4. **Mathematical Foundation**: Formal definitions, notations, proofs, invariants.
5. **Core Theory**: No simplification.
6. **Visual Explanation**: ASCII/Mermaid diagrams, execution flow, memory layout, state transition.
7. **Java Implementation**: Build from scratch. Do not use Java Collections immediately. Use Java 21.
8. **Step-by-Step Execution**: Insert, Delete, Update, Search, etc.
9. **Complexity Analysis**: Worst, Average, Best, Amortized, Memory, Cache, CPU.
10. **JVM Analysis**: Object layout, Stack, Heap, GC, Escape analysis, Reference types, Primitive vs Object.
11. **OpenJDK Analysis**: How OpenJDK implements it.
12. **Production Usage**: FAANG, Kafka, Redis, Spring, etc.
13. **Design Decisions**: Trade-offs, decision trees.
14. **Common Bugs**: Exactly **20** common bugs.
15. **Edge Cases**: Exactly **30** edge cases.
16. **Optimization Techniques**: Micro, algorithmic, memory, JVM.
17. **Best Practices**: Coding, naming, API design, testing.
18. **Benchmark**: JMH benchmark examples.
19. **Unit Testing**: JUnit 5, edge tests, stress tests.
20. **Interview Questions**: Exactly **20** questions (Easy to Staff).
21. **Practice Problems Link**: A link referencing the companion problem file.
22. **Pattern Recognition**: How to recognize the algorithm.
23. **Real Case Study**: Production incidents, tuning, failure analysis.
24. **Summary & Checklist**

## 5. Companion Problem File
- For every theory file, there MUST be a companion problems file named `[Topic]-Problems.md`.
- It MUST contain exactly **30 typical problems** related to the topic.
- For each problem, there MUST be a **detailed and clear problem description** (mô tả đề bài chi tiết và rõ ràng).
- Before providing the code, there MUST be an **analysis of why this specific coding approach/algorithm was chosen** (phân tích tại sao lại chọn cách code đó).
- It MUST include **detailed Java solutions** and complexity analysis for all 30 problems.

## 6. Code Quality
- All code must compile and be Java 21 compatible.
- Follow SOLID principles.
- Must be production-quality with comments and trade-off explanations.
