## 1. Problems vs Algorithms & Easy vs Hard

### 1.1 Problems vs Algorithms

- **Algorithm**: a finite procedure for solving a problem (e.g., merge sort, quicksort are algorithms for **sorting**).
    
- **Problem**: mapping from _instances_ (inputs) to _solutions_ (outputs).
    
    - Example (optimization): SHORTEST-PATH-OPTMZ
        
        - Instance: graph (G), vertices (u, v).
            
        - Solution: a shortest path from (u) to (v).
            

In this unit we shift focus from _designing algorithms_ to understanding the **intrinsic difficulty of problems** (when efficient algorithms likely do _not_ exist).

---

### 1.2 “Efficient” = Polynomial Time

- We call an algorithm **efficient** if its worst-case running time on inputs of size (n) is (O(n^k)) for some constant (k).
    
- Polynomial time = tractable / “easy”.
    
- Superpolynomial (e.g., (2^n, n^{\log n})) = intractable / “hard” (for large enough (n)).
    

Not all problems have polynomial-time algorithms:

1. Some are **undecidable** (e.g., Halting Problem) → no algorithm can always decide YES/NO.
    
2. Some are **decidable** but require superpolynomial time (as far as we know).
    

---

### 1.3 Subtly Different Problems, Very Different Hardness

The slides highlight that “similar-looking” problems can have wildly different complexity:

- **Shortest vs Longest simple path** in a graph:
    
    - Shortest simple path: can be solved in polynomial time (BFS / Dijkstra, etc.).
        
    - Longest simple path: **NP-complete**.
        
- **Euler tour vs Hamiltonian cycle**:
    
    - _Euler tour_: cycle traversing each **edge** exactly once → can decide in (O(|E|)).
        
    - _Hamiltonian cycle_: simple cycle visiting each **vertex** exactly once → NP-complete.
        

Moral: superficial similarity does _not_ imply similar complexity.

---

## 2. Decision vs Optimization Problems

### 2.1 Definitions

- **Optimization problem**: find the best solution (max or min value).
    
    - Example: SHORTEST-PATH-OPTMZ: find a path with **fewest** edges.
        
- **Decision problem**: YES/NO question.
    
    - Example: SHORTEST-PATH:
        
        - Input: graph (G), vertices (u,v), integer (k).
            
        - Question: “Is there a path from (u) to (v) with at most (k) edges?”
            

NP-completeness theory is formulated **only** in terms of **decision problems**.

---

### 2.2 Why Decision Problems Are Enough

You can usually turn an optimization problem into an associated decision problem:

- Use the optimization algorithm as a subroutine:
    
    - Given SHORTEST-PATH-OPTMZ algorithm, to answer SHORTEST-PATH:
        
        1. Compute shortest-path length (\ell).
            
        2. Answer YES iff (\ell \le k).
            

So the **decision version** is _no harder_ than the optimization version.

**Key consequence:**

- If the _decision version_ is NP-hard (no poly-time algorithm unless P=NP),  
    then the _optimization version_ is at least as hard.
    
- Showing the decision version is hard effectively shows the “real-world” optimization problem is hard too.
    

---

## 3. Formal Problem & Decision Problem Definitions

- **Problem**:
    
    - An _instance_ is a finite description (e.g., graph + two vertices + integer (k)).
        
    - A _solution_ is one of possibly many valid answers (path, assignment, etc.).
        
- **Decision problem**: each instance must be classified as **YES** or **NO**.
    
    - Example: SHORTEST-PATH instance ((G,u,v,k)) → YES if (u) can reach (v) with path length (\le k).
        

---

## 4. Complexity Classes: P, NP, NPC, EXP

### 4.1 Class P (Polynomial Time)

- A problem is **polynomial-time solvable** if there is an algorithm that:
    
    1. Outputs a correct solution on all instances.
        
    2. Runs in (O(n^k)) time for some constant (k), where (n) is input size.
        
- **P** = set of decision problems that are polynomial-time solvable.
    

Examples: sorting, BFS/DFS reachability, MST, max-flow, shortest path, etc.

---

### 4.2 Certificates & Verifiers → NP

Some problems appear hard to **solve**, but **easy to check** a proposed solution.

**Example 1 – HAM-CYCLE**

- HAM-CYCLE: “Does an undirected graph (G) have a Hamiltonian cycle?”
    
- “Certificate”: a sequence of vertices claimed to form a Hamiltonian cycle.
    
- Verification:
    
    1. Check we see each vertex exactly once.
        
    2. Check each consecutive pair is an edge, and last to first is an edge.
        
- Verification runs in polynomial time in (|V| + |E|).  
    (Diagram on _page 35_ illustrates a Hamiltonian cycle on a dodecahedron and a graph without one.)
    

**Example 2 – SAT**

- SAT: given Boolean formula (\phi(x_1,\dots,x_n)), “Is there an assignment making (\phi) true?”
    
- Certificate: a length-(n) bitstring representing a truth assignment to variables.
    
- Verification: plug in assignment, evaluate (\phi) in time polynomial in (|\phi|).
    

---

### 4.3 Verification Algorithm (Verifier)

- For a decision problem (Q), a **verifier** is an algorithm (C(x, y)) such that:
    
    - (x): an instance of (Q).
        
    - (y): a **certificate** (bitstring).
        
    - (C(x,y)) returns YES or NO.
        
    - (x) is a YES-instance **iff** there exists some certificate (y) such that (C(x,y)) returns YES.
        

Important details:

- For a YES-instance (x):
    
    - Not all certificates have to work; just **at least one**.
        
- For a NO-instance (x):
    
    - No certificate may cause (C) to say YES.
        

**Writing a verifier**:

1. Decide how to **encode** a certificate as a bitstring.
    
2. In (C(x,y)), decode (y).
    
3. If decoding fails, return NO.
    
4. Otherwise, check that the decoded object is a valid witness for “YES”.
    

Examples:

- SAT:
    
    - Decode first (n) bits of (y) as assignment to (x_1,\dots,x_n).
        
    - Evaluate (\phi); return YES iff it evaluates to true.
        
- HAM-CYCLE:
    
    - Decode (y) into a sequence of (|V|) vertices.
        
    - Check they form a Hamiltonian cycle.
        

---

### 4.4 Class NP (Nondeterministic Polynomial Time)

- **NP** = set of decision problems that have a **polynomial-time verifier**.
    
    - i.e., there exists some verifier (C(x,y)) running in time polynomial in (|x|).
        

Equivalently: problems where YES instances have **polynomial-size certificates** that can be checked quickly.

**Key inclusion:** (P \subseteq NP).

- If a problem has a polynomial-time algorithm (A(x)), we can make a verifier (C(x,y)) that:
    
    - Ignores (y),
        
    - Runs (A(x)),
        
    - Returns (A)’s answer.
        
- So every poly-time solvable problem also has a poly-time verifier.
    

---

### 4.5 Class EXP

- **EXP**: decision problems solvable in **exponential time**.
    
    - Running time like (2^{\text{poly}(n)}).
        

**Proposition:** (NP \subseteq EXP).

- Sketch: to _solve_ a problem in NP, given verifier (C(x,y)):
    
    1. Enumerate all bitstrings (y) up to length (m = \text{poly}(|x|)).
        
    2. For each (y), run (C(x,y)).
        
    3. If any call returns YES → instance is YES; else NO.
        
- Number of certificates to try is at most (2^{\text{poly}(|x|)}), so overall time is exponential.
    

Thus we know:

[  
P \subseteq NP \subseteq EXP.  
]

Whether these inclusions are strict is the big open question (see below).

---

### 4.6 NP-Complete (NPC)

We want to identify the **“hardest” problems in NP**.

- A problem (Q \in NP) is **NP-complete** (in NPC) if:
    
    1. (Q \in NP) (has a polynomial-time verifier), and
        
    2. Every problem in NP **reduces** to (Q) in polynomial time.
        

Intuition: NP-complete problems are at least as hard as _any other_ problem in NP.

---

## 5. Polynomial-Time Reductions

### 5.1 Definition: Polynomial Reduction

- A decision problem (Q_1) **reduces** to (Q_2) (written (Q_1 \le_p Q_2)) if:
    
    There exists a polynomial-time algorithm (F) such that for any instance (x_1) of (Q_1),  
    (F) outputs an instance (x_2) of (Q_2) with:
    
    [  
    x_1 \text{ is YES for } Q_1 \iff x_2 \text{ is YES for } Q_2.  
    ]
    

**Implication:**

- If (Q_1 \le_p Q_2) and we have a poly-time algorithm for (Q_2), then:
    
    - We also get a poly-time algorithm for (Q_1) by:
        
        1. Compute (x_2 = F(x_1)).
            
        2. Run the algorithm for (Q_2) on (x_2).
            
        3. Return its answer.
            

Therefore, “(Q_1) reduces to (Q_2)” means “(Q_1) is **no harder** than (Q_2)”.

---

### 5.2 Example Reduction (Toy Example from Slides)

- (Q_1): “Does string (x_1) contain letter ‘a’?”
    
- (Q_2): “Does string (x_2) contain letter ‘b’?”
    

Reduction (F):

1. Take (x_1).
    
2. Swap all ‘a’ ↔ ‘b’ in (x_1).
    
3. Output resulting string as (x_2).
    

Then:

- (x_1) has an ‘a’ ⇔ (x_2) has a ‘b’.
    

So (Q_1 \le_p Q_2).

Although trivial, this mirrors what we do with more complicated NP-complete reductions: transform instances so YES/NO is preserved.

---

## 6. NP-Completeness & P vs NP

### 6.1 Why NP-Completeness Matters

- If **any one** NP-complete problem has a polynomial-time algorithm, then:  
    [  
    P = NP.  
    ]
    
- Reason: if (Q) is NP-complete and has a poly-time algorithm, and any (Q' \in NP) reduces to (Q), we get a poly-time algorithm for (Q') too.
    

Formally:

> If an NP-Complete problem can be solved in polynomial time, then **all** problems in NP can be solved in polynomial time.

Conversely, if we can show that **no** NP-complete problem has a poly-time algorithm, then (P \ne NP).

---

### 6.2 The Million-Dollar Question: Is P = NP?

The slides show two diagrams (_page 65_) indicating two possibilities:

1. **If (P \ne NP)**:
    
    - We have (P \subsetneq NP \subseteq EXP).
        
    - Some problems in NP are strictly harder than all problems in P.
        
2. **If (P = NP)**:
    
    - P and NP collapse to the same set, still contained in EXP.
        

We currently **do not know** which is true. Most researchers believe (P \ne NP), but no proof exists.

This is one of the Clay Mathematics Institute’s Millennium Prize Problems.

---

### 6.3 Relationships Summary

- Proven:
    
    - (P \subseteq NP \subseteq EXP)
        
- Unknown:
    
    - Is (P = NP)?
        
    - Is (NP = EXP)? (Almost certainly not, but not trivial.)
        

---

## 7. How to Show a Problem Is NP-Complete

General recipe (from the final slides):

To prove a problem (Q) is **NP-complete**:

1. **Show (Q \in NP)**:
    
    - Design a polynomial-time verifier (C(x,y)).
        
    - Example approach:
        
        - Certificate describes a candidate solution (tour / assignment / subset).
            
        - Verifier checks quickly that the candidate satisfies the constraints.
            
2. **Show (Q) is NP-hard**:
    
    - Need to show **every** problem in NP reduces to (Q).
        
    - In practice:
        
        - Start from a **known** NP-complete problem (Q^*).
            
        - Prove (Q^* \le_p Q) via a polynomial reduction.
            

Because reductions are **transitive**:

- If (Q_1 \le_p Q_2) and (Q_2 \le_p Q_3), then (Q_1 \le_p Q_3).
    
- So once we have a “first” NP-complete problem (historically SAT), we can build a whole **web** of NP-complete problems via chains of reductions.
    

**Strategy in practice:**

- Pick (Q^*) that is:
    
    - Already known NP-complete,
        
    - Structurally close to your target problem (Q).
        
- Design (F) to transform any instance of (Q^*) into an instance of (Q) that preserves YES/NO.
    
- Show (F) runs in polynomial time.
    

---

## 8. Why We Focus on Polynomial Time

The slides emphasize several reasons:

1. **Realistic efficiency**:
    
    - Poly-time algorithms we actually use (like (O(n \log n)), (O(n^2))) are usually practical.
        
    - Truly enormous exponents like (n^{100}) are rare in real algorithm design.
        
2. **Robustness across models**:
    
    - Poly-time on RAM, Turing machine, etc. corresponds closely.
        
3. **Closure properties**:
    
    - Polynomials closed under addition, multiplication, composition.
        
    - Composing poly-time algorithms still yields poly-time algorithms.
        
4. **Experience**:
    
    - Once one poly-time algorithm is found, improvements usually follow.
        

All of this makes **polynomial-time** a natural boundary between “feasible” and “infeasible” in theory.

---

## 9. Conceptual Cheat-Sheet Summary

When you see a new problem:

1. **Is it a decision problem?** If not, write a decision version (YES/NO).
    
2. **Can I recognize it as in P?**
    
    - Is it obviously reducible to shortest path, matching, flow, DP, etc.?
        
3. **Is there a short certificate for YES instances?**
    
    - If so, you can typically argue it’s in NP via a verifier.
        
4. **To prove NP-completeness:**
    
    - Step 1: Give a polynomial-time verifier → (Q \in NP).
        
    - Step 2: Find a known NP-complete problem (Q^*) and construct a poly-time reduction (Q^* \le_p Q).
        
5. **Interpret reductions correctly:**
    
    - If (Q_1 \le_p Q_2) and (Q_2) is easy (in P), then (Q_1) is also easy.
        
    - If (Q_1) is NP-complete and (Q_1 \le_p Q_2), then (Q_2) is **at least** NP-hard.
        

Mental Map:

- **P**: efficiently solvable problems.
    
- **NP**: problems with efficiently verifiable certificates.
    
- **NP-complete**: “hardest” problems in NP (if any one of them is easy, then _everything_ in NP is easy).
    
- **EXP**: problems solvable with exponential-time algorithms; contains NP.
    
