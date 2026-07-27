## 1. Greedy Algorithm Paradigm

**Idea:**  
Build a solution step by step, always making the **locally best choice** according to some heuristic, hoping it leads to a globally optimal solution.

Typical pattern:

1. **Model the problem** (constraints, objective).
    
2. **Propose a greedy rule** (which choice do we make each step?).
    
3. **Describe the algorithm** (often: sort + single pass).
    
4. **Prove correctness**:
    
    - _Greedy-choice property:_ We can assume there is an optimal solution that starts with the greedy choice.
        
    - _Optimal substructure:_ After fixing the greedy choice, the remaining subproblem is solved optimally by recursively doing the same.
        
    - Use **exchange arguments**, **“stays ahead” arguments**, or **induction**.
        
5. **Analyze running time** (often dominated by a sorting step, e.g. `O(n log n)`).
    

---

## 2. Fractional Knapsack

### 2.1 Problem

- Capacity: `W` (weight/volume limit).
    
- Items: `i = 1..n`, each with:
    
    - value `v_i`
        
    - weight `w_i`
        
- **Fractional**: you may take any fraction of an item.
    
- Goal: maximize total value subject to total weight ≤ `W`.
    

### 2.2 Greedy Rule

Take items in order of **decreasing value density**:

$$ 
\text{density}_i = \frac{v_i}{w_i}  
$$

### 2.3 Algorithm

1. Compute density `v_i / w_i` for each item.
    
2. Sort items so that `v_1/w_1 ≥ v_2/w_2 ≥ ... ≥ v_n/w_n`.
    
3. Initialize `value = 0`, `remaining = W`.
    
4. For `i = 1..n`:
    
    - If `w_i ≤ remaining`:
        
        - Take the whole item:  
            `remaining -= w_i`, `value += v_i`.
            
    - Else:
        
        - Take fraction `f = remaining / w_i`.
            
        - `value += f * v_i`.
            
        - Stop (knapsack is full).
            

Complexity: `O(n log n)` due to sorting.

### 2.4 Proof Idea (Exchange Argument)

Let:

- `P = {p_1, ..., p_n}` be greedy amounts,
    
- `Q = {q_1, ..., q_n}` be an **optimal** solution.
    

Assume items are sorted by **non-increasing density**.

1. Let `i` be the **first index** where `p_i ≠ q_i`.
    
2. In greedy, we always take as much as possible of item `i` before moving on, so:  
    $$  
    p_i ≥ q_i.  
    $$
    
3. Consider a modified solution `Q'`:
    
    - Increase the amount of item `i` by `p_i - q_i`.
        
    - Decrease total weight from items with index > `i` to maintain capacity.
        
    - Because items after `i` have **lower or equal density**, replacing fractions of them with item `i` **does not reduce** total value.
        
4. So `Q'` is also optimal and now satisfies `p_i = q_i` while matching greedy on all earlier indices.
    
5. Repeating this adjustment from left to right, we eventually transform some optimal solution into the greedy one.  
    → Greedy solution is optimal.
    

---

## 3. Interval Scheduling (Maximum Compatible Intervals)

### 3.1 Problem

Given `n` intervals (jobs, classes):

- Interval `i` has start time `s_i` and finish time `f_i`.
    
- Two intervals are **compatible** if they do not overlap in time.
    
- Goal: choose a maximum-size subset of pairwise compatible intervals.
    

### 3.2 Naive Greedy Ideas That Fail

Examples of bad heuristics (there are counterexamples):

- Choose the interval with:
    
    - earliest start time,
        
    - shortest length,
        
    - fewest conflicts,
        

These do **not** always give an optimal set.

### 3.3 Correct Greedy Rule

**Always pick the interval that finishes earliest** among the ones still compatible with what we’ve chosen so far.

Intuition: finishing earlier leaves more room for future intervals.

### 3.4 Algorithm (GREEDY-INTERVAL-SCHED)

```text
Input: intervals (s_i, f_i), i = 1..n

1. Sort intervals by non-decreasing finish time: f_1 ≤ f_2 ≤ ... ≤ f_n.
2. C = ∅
3. F = -∞         // finish time of last chosen interval
4. For i = 1..n:
5.     if s_i ≥ F:
6.         C = C ∪ {i}
7.         F = f_i
8. Return C
```

- Sorting: `O(n log n)`
    
- Single pass: `O(n)`
    
- Total: `O(n log n)`
    

### 3.5 Proof Idea 1: “Stays Ahead” Argument

Let:

- `G = {g_1, ..., g_k}` be intervals chosen by greedy, in order of selection.
    
- `O = {o_1, ..., o_t}` be an **optimal** solution (maximum number of intervals), ordered by finishing time.
    

Claim: For all `r`, we have `f(g_r) ≤ f(o_r)` (greedy’s r-th finish is no later than optimal’s r-th finish).

- **Base:** `r = 1`. Greedy chooses interval with **earliest finishing time** among all, so `f(g_1) ≤ f(o_1)`.
    
- **Inductive step:** suppose `f(g_r) ≤ f(o_r)`.
    
    - Since intervals in `O` are compatible, `o_{r+1}` starts after `o_r` finishes: `s(o_{r+1}) ≥ f(o_r)`.
        
    - From `f(g_r) ≤ f(o_r)`, we get `s(o_{r+1}) ≥ f(g_r)`, so `o_{r+1}` is **still compatible** when greedy is choosing `g_{r+1}`.
        
    - Greedy chooses the interval with minimum finishing time among all compatible intervals, so `f(g_{r+1}) ≤ f(o_{r+1})`.
        

Consequence: if optimal solution had more intervals than greedy (`t > k`), then `o_{k+1}` would start after `f(o_k) ≥ f(g_k)`, so it would also be available to greedy—which contradicts greedy stopping. Thus `k = t` and greedy is optimal.

### 3.6 Proof Idea 2: Exchange Argument

Alternate view:

- Let `O` be an optimal solution with maximal number of intervals.
    
- Show: there exists an optimal solution with the **same size** that uses the interval chosen by greedy as its first interval.
    
- Then reduce the problem to a smaller interval set and apply induction.
    

---

## 4. Interval Partitioning (Minimum Number of Resources)

### 4.1 Problem

Given intervals (lectures) with start and finish times:

- Assign each interval to a **resource** (e.g., classroom).
    
- No two intervals assigned to the same resource may overlap.
    
- Goal: **minimize** the number of resources used.
    

### 4.2 Key Fact

The minimum number of resources needed equals the **maximum number of intervals that overlap at any time** (maximum depth).

### 4.3 Greedy Rule & Algorithm

**Sort by start time**, then assign each interval to the resource that becomes free earliest.

Implementation sketch:

```text
1. Sort intervals by starting time.
2. Maintain a min-heap (priority queue) keyed by resource finish time.
3. For each interval i:
     - If the earliest-finish resource is free before s_i:
           assign i to that resource (update its finish time to f_i).
       Else:
           open a new resource for i, push its f_i in the heap.
```

- Complexity: `O(n log n)` due to heap ops.
    

**Correctness idea:** at each step you re-use the room that frees the earliest, which keeps the system as “tight” as possible and never forces extra rooms beyond the inherent overlap depth.

---

## 5. Scheduling to Minimize Maximum Lateness

### 5.1 Problem

Jobs `1..n`:

- Each job `j` has:
    
    - processing time `p_j`,
        
    - deadline `d_j`.
        
- Single machine, processes jobs one at a time, no preemption.
    
- Start at time `0`.
    

Let:

- `C_j` = completion time of job `j` in the schedule.
    
- `L_j = C_j - d_j` = lateness (can be negative if early).
    
- Goal: minimize `L_max = max_j L_j`.
    

### 5.2 Greedy Rule (EDF)

Schedule jobs in **non-decreasing order of deadlines** (earliest deadline first, EDF).

### 5.3 Algorithm

```text
1. Sort jobs by deadline: d_1 ≤ d_2 ≤ ... ≤ d_n.
2. t = 0
3. For j = 1..n:
       schedule job j at time t
       t = t + p_j
```

- Complexity: `O(n log n)` due to sort.
    

### 5.4 Proof Idea (Exchange Argument)

- Consider any schedule with **no idle time** (we can always compress one to remove idle without increasing lateness).
    
- If there is an **inversion** (job `j` scheduled before `k` but `d_j > d_k`), swap them.
    
- Show that swapping such an inverted pair **does not increase** `L_max`.
    
- Repeatedly fixing inversions leads to a schedule where deadlines are in order; since swaps never hurt, EDF is optimal.
    

---

## 6. Induction & Greedy Proof Techniques

Slides emphasize mathematical induction and its use in greedy proofs.

### 6.1 Mathematical Induction Template

To prove a statement `P(i)` for all integers `i ≥ 0`:

1. **Base case:** Show `P(0)` (or smallest index) holds.
    
2. **Inductive step:** Assume `P(i-1)` is true, prove `P(i)` is true.
    
3. Conclude `P(i)` holds for all `i`.
    

In greedy algorithms, you often:

- Induct on the **number of steps** of the algorithm.
    
- Induct on the **size** of the instance (`n`).
    
- Combine induction with an **invariant**: a property that holds after each greedy choice.
    

### 6.2 Common Greedy Proof Patterns

1. **Stays-ahead argument**  
    Compare greedy and optimal solutions step-by-step; show that one measure (e.g., finish time, cumulative value) of the greedy solution is at least as good at every step.
    
2. **Exchange argument**  
    Take an optimal solution and _exchange_ parts of it with greedy choices without hurting optimality, gradually turning it into the greedy solution.
    
3. **Structural induction on choices**  
    After fixing the greedy choice, argue that the remaining subproblem is of the same form and must also be solved optimally by repeated greedy choices.
    

---

## 7. HW2 Problem 1 – Minimum Water Stops (Prof. Sharp Skating)

> This is a classic greedy “gas station” / refueling problem.

### 7.1 Problem Restatement

- Start at point `A`, end at point `B`.
    
- A single bottle lets Prof. Sharp travel at most `m` miles.
    
- There are `n` stops along the highway with distances:
    $$
    d_1 < d_2 < \dots < d_n,  
    $$

    where:
    
    - `d_1 = 0` (start `A`),
        
    - `d_n` is the destination `B`.
        
- Assume **feasible**: every gap `d_{i+1} - d_i ≤ m`.
    
- Prof. Sharp must **start** with a full bottle at `A`, and **finish** at `B`.
    
- Goal: choose a subset of stops to refill so that:
    
    - Prof. Sharp never runs out of water,
        
    - the number of refills is **minimal**.
        

### 7.2 Greedy Rule

At each refill, **go as far as possible**:

> While you can reach the next stop with your current water, move on; stop to refill only when you cannot go further.

### 7.3 Algorithm (GreedyRefills)

```text
Input: distances d[1..n] (sorted), maximum range m
Assumption: d[1] = 0, d[n] = B, and d[i+1] - d[i] ≤ m.

1. i = 1                 // current stop index
2. plan = [1]            // must include start A
3. While i < n:
4.     j = i
5.     // Move j as far as possible without exceeding range m
6.     while j+1 ≤ n and d[j+1] - d[i] ≤ m:
7.         j = j + 1
8.     if j == i:
9.         return "infeasible" // (under our assumption, won’t happen)
10.    i = j
11.    plan.append(i)    // we refill at stop i
12. return plan          // includes n (B) as last element
```

- Complexity: `O(n)` (single pass through stops).
    

### 7.4 Proof Idea (Induction / Stays-ahead)

Let:

- `G = (g_1 = 1 < g_2 < ... < g_k = n)` be the greedy stopping plan.
    
- `O = (o_1 = 1 < o_2 < ... < o_t = n)` be an optimal plan with minimal number of stops.
    

**Claim:** For every `r`, `g_r ≥ o_r`. (Greedy never stops _earlier_ along the road than an optimal plan at the same step.)

- **Base:** `r = 1`. Both start at `1`.
    
- **Step:** Assume `g_r ≥ o_r`.
    
    - From `o_r`, the optimal plan goes to `o_{r+1}` with `d[o_{r+1}] - d[o_r] ≤ m`.
        
    - Since `o_r ≤ g_r`, the distance from `g_r` to `o_{r+1}` is:
        
    $$  
        d[o_{r+1}] - d[g_r] ≤ d[o_{r+1}] - d[o_r] ≤ m  
    $$
        
        so `o_{r+1}` is **reachable** from `g_r` with one bottle.
        
    - Greedy chooses `g_{r+1}` as the **farthest reachable** stop from `g_r`, so:
        
        $$  
        g_{r+1} ≥ o_{r+1}.  
        $$
        

Thus `g_r ≥ o_r` for all `r`.

If greedy used **more** stops than optimal (`k > t`), then by the above inequality, `g_t ≥ o_t = n`, but `g_t` should be the `t`-th stop of greedy; that forces greedy to reach `n` in at most `t` stops. Contradiction.

So greedy uses the **minimum** number of refills.

---

## 8. HW2 Problem 2 – Coin Changing with Base-k Denominations

> Coin system: `A_n = {k^{n-1}, k^{n-2}, ..., k^0}` for integer `k > 1`.

### 8.1 Problem Restatement

- Coin types: `a_1, a_2, ..., a_n`, with:
    
    - `a_1 > a_2 > ... > a_n`,
        
    - `a_i` are positive integers,
        
    - unlimited coins of each type.
        
- In this special case:
    
    $$  
    A_n = { k^{n-1}, k^{n-2}, \dots, k^0 }  
    $$
    
    for some integer `k > 1`.
    
- Given `C > 0`, we want to make **exactly** `C` using the **minimum number of coins**.
    

### 8.2 Greedy Rule

Take as many coins as possible of the **largest denomination** that fits into the remaining amount, then move to the next smaller denomination, and so on.

This is exactly converting `C` to base-`k` and using digits as coin counts.

### 8.3 Algorithm (GreedyKBaseCoins)

```text
Input: C, k, n
       denominations: k^(n-1), k^(n-2), ..., k^0

1. count[0..n-1] = 0
2. for i from n-1 down to 0:      // coin = k^i
3.     coin = k^i
4.     count[i] = C // coin       // integer division
5.     C = C % coin
6. return count                   // total coins = sum_i count[i]
```

- Complexity: `O(n)` (single pass through denominations).
    

### 8.4 Proof Idea: Base-k Representation

1. **Uniqueness of Base-k Expansion**  
    Every nonnegative integer `C` can be written uniquely as:
    $$  
    C = \sum_{i=0}^{n-1} b_i k^i, \quad 0 \le b_i \le k - 1.  
    $$
    
    Interpreting `b_i` as “number of coins of denomination `k^i`” gives a valid coin combination with `∑ b_i` coins.
    
2. **Any Optimal Solution Obeys Digit Bounds**
    
    Suppose some solution uses more than `k-1` coins of some denomination `k^i` (i.e., at least `k` coins). Then:
    
    - Replace `k` coins of `k^i` by one coin `k^{i+1}`.
        
    - This reduces the total number of coins (since `k` coins → 1 coin) while preserving the sum.
        
    - Repeating, any **optimal** solution must have coin counts `c_i` satisfying `0 ≤ c_i ≤ k-1`.
        
    
    So any optimal solution corresponds to a choice of digits that fits a base-`k` expansion.
    
3. **Greedy Coin Counts Match the Unique Base-k Digits**
    
    The greedy algorithm chooses:
    
    - `g_{n-1} = floor(C / k^{n-1})`,
        
    - then sets `C ← C mod k^{n-1}` and continues.
        
    
    This exactly computes the digits of the base-`k` representation, so the resulting coins `(g_{n-1}, ..., g_0)` are exactly `(b_{n-1}, ..., b_0)` in the unique expansion.
    
4. **Optimality**
    
    - Any feasible solution that matches the base-`k` digits has total coins `∑ b_i`.
        
    - No solution with fewer coins exists due to the replacement argument above.
        
    - Therefore, the greedy solution is **optimal**.
        

---

## 9. What to Practice

When you study / drill this topic, I’d focus on:

- **Design patterns:**
    
    - Can I quickly identify when a **density-based** greedy rule makes sense? (like fractional knapsack)
        
    - Can I recognize priority based on **finish time** (interval scheduling), **deadlines** (lateness), or **farthest reachable** (refills)?
        
- **Proofs:**
    
    - Practice writing an **exchange argument** for:
        
        - Fractional knapsack,
            
        - Base-`k` coins,
            
        - Interval scheduling.
            
    - Practice a **stays-ahead** argument (like in interval scheduling and water stops).
        
- **Implementations & Complexity:**
    
    - Be able to write pseudocode and justify `O(n)` vs. `O(n log n)` for:
        
        - Fractional knapsack,
            
        - Interval scheduling,
            
        - Interval partitioning,
            
        - Min lateness scheduling,
            
        - Water-stops, base-`k` coins.
            
