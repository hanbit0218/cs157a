# Database Normalization

## I. Introduction to Normalization

- Definition: Process of organizing data to minimize redundancy and dependency
- Goal: Ensure data integrity and efficiency

## II. Normal Forms

1. First Normal Form (1NF)

   - Atomic values
   - No repeating groups

2. Second Normal Form (2NF)

   - Satisfies 1NF
   - All non-key attributes fully dependent on primary key

3. Third Normal Form (3NF)

   - Satisfies 2NF
   - No transitive dependencies

4. Boyce-Codd Normal Form (BCNF)

   - Stronger version of 3NF
   - For every non-trivial FD X → Y, X must be a superkey

5. Fourth Normal Form (4NF)
   - Addresses multi-valued dependencies
   - For every non-trivial MVD X →→ Y, X must be a superkey

## III. Key Concepts

### A. Functional Dependencies (FD)

- Notation: X → Y (X determines Y)
- Properties:
  - Right sides can be split
  - Left sides cannot be split

### B. Keys

- Candidate key: Minimal set of attributes that functionally determines all others
- Superkey: Set of attributes containing a key, not necessarily minimal

### C. Closure of Attributes

- Denoted as Y+
- Set of all attributes determined by Y given a set of FDs

### D. Multi-valued Dependencies (MVD)

- Notation: X →→ Y (X multi-determines Y)
- Generalizes functional dependencies

## IV. Decomposition

### A. Goals

1. Eliminate anomalies
2. Achieve lossless join
3. Preserve dependencies

### B. Processes

1. BCNF Decomposition
   - Recursively decompose relations with BCNF violations
2. 3NF Synthesis
   - Use minimal basis of FDs
3. 4NF Decomposition
   - Similar to BCNF, but consider MVDs

## V. Comparison of Normal Forms

| Property                  | 3NF | BCNF | 4NF |
| ------------------------- | --- | ---- | --- |
| Eliminates FD redundancy  | No  | Yes  | Yes |
| Eliminates MVD redundancy | No  | No   | Yes |
| Preserves FDs             | Yes | No   | No  |
| Preserves MVDs            | No  | No   | No  |

## VI. Inference Rules

1. Reflexivity: If B ⊆ A, then A → B
2. Augmentation: If A → B, then AC → BC
3. Transitivity: If A → B and B → C, then A → C

## VII. Testing Methods

1. Chase Test: For lossless join
2. Closure Computation: For determining keys and superkeys
3. Tableau Method: For inferring FDs and MVDs
