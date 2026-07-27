## 1. Graph Basics

### 1.1 Graph Definitions

- A **graph** is (G = (V, E))
    
    - (V): set of vertices (nodes)
        
    - (E): set of edges
        
- **Directed graph (digraph)**:
    
    - Edge ( (u, v) ) goes _from_ (u) _to_ (v)
        
- **Undirected graph**:
    
    - Edge has no direction; ((u, v) = (v, u))
        
- In this course we assume **simple graphs** unless stated otherwise:
    
    - No self-loops ((v, v))
        
    - No parallel edges (at most one edge between a pair of vertices)
        

---

### 1.2 Subgraphs and Induced Subgraphs

Given (G = (V, E)):

- A **subgraph** (G' = (V', E')) of (G):
    
    - (V' \subseteq V) and (E' \subseteq E)
        
- For a vertex set (V' \subseteq V), the **induced subgraph** on (V'):
    
    - (G' = (V', E')) where  
        (E' = {(u, v) \in E \mid u \in V', v \in V'})
        

---

### 1.3 Paths and Cycles

- A **path** in (G):
    
    - Sequence of vertices (v_1, v_2, \dots, v_k) such that each ((v_i, v_{i+1})) is an edge
        
    - Works for both directed and undirected graphs
        
- A **simple path**:
    
    - No repeated vertices
        
- A **cycle**:
    
    - A path that starts and ends at the same vertex
        
- A **simple cycle**:
    
    - No repeated vertices other than the start/end
        

---

### 1.4 Degrees

For **directed** (G = (V, E)):

- **Out-degree** of vertex (x):
    
    out-deg⁡(x)=∣{(u,v)∈E∣u=x}∣\operatorname{out\text{-}deg}(x) = |\{(u, v)\in E \mid u = x\}|
- **In-degree** of vertex (x):
    
    in-deg⁡(x)=∣{(u,v)∈E∣v=x}∣\operatorname{in\text{-}deg}(x) = |\{(u, v)\in E \mid v = x\}|
- Degree sums:
    
    ∑v∈Vout-deg⁡(v)=∑v∈Vin-deg⁡(v)=∣E∣\sum_{v\in V} \operatorname{out\text{-}deg}(v) = \sum_{v\in V} \operatorname{in\text{-}deg}(v) = |E|

For **undirected** (G = (V, E)):

- **Degree** of vertex (x):
    
    deg⁡(x)=∣{(u,v)∈E∣u=x or v=x}∣\deg(x) = |\{(u, v)\in E \mid u = x \text{ or } v = x\}|
- Degree sum:
    
    ∑v∈Vdeg⁡(v)=2∣E∣\sum_{v\in V} \deg(v) = 2|E|

---

### 1.5 Connectivity

For an **undirected** graph (G = (V, E)):

- Vertices (u, v) are **connected** if there is a path from (u) to (v).
    
- A **connected component** (U \subseteq V):
    
    - Maximal set where every pair of vertices in (U) is connected.
        
- (G) is **connected**:
    
    - Has exactly one connected component.
        

For **directed** graphs:

- Analog is **strongly connected components** (SCCs), but those are not the main focus here.
    

---

## 2. Trees and Forests

### 2.1 Definitions

- A **tree**:
    
    - Undirected, connected, acyclic graph.
        
- A **forest**:
    
    - Undirected, acyclic graph.
        
    - Each connected component is a tree (disjoint union of trees).
        
- A **rooted tree**:
    
    - Start with an undirected tree.
        
    - Choose a root (r).
        
    - Direct each edge from parent to child (e.g., via DFS from the root).
        
    - Typically what we mean by “tree data structure” (binary trees, heaps, etc.).
        

In a rooted tree:

- Every vertex (except the root) has exactly **one incoming edge** from its parent.
    
- If there is a directed path from (u) to (v):
    
    - (u) is an **ancestor** of (v)
        
    - (v) is a **descendant** of (u)
        

---

### 2.2 Key Facts About Trees

Let (T) be a tree with (n) vertices:

- **Edge count**:
    
    - (T) has exactly (n - 1) edges.
        
- Characterizations of a tree (for undirected graphs):
    
    - Connected and acyclic.
        
    - Connected and has (n - 1) edges.
        
    - Acyclic and has (n - 1) edges.
        
    - Between any two vertices there is **exactly one simple path**.
        

These equivalences are often used in proofs and algorithms that check whether a graph is a tree.

---

## 3. Graph Representations

### 3.1 Adjacency List

- For each vertex (u), store a list of its neighbors.
    
- Space:
    
    Θ(∣V∣+∣E∣)\Theta(|V| + |E|)
- Pros:
    
    - Efficient for sparse graphs.
        
    - Easy to iterate over neighbors of a vertex.
        
- Operations:
    
    - Iterate all neighbors of (u): (O(\deg(u))).
        
    - Traverse all edges: (O(|V| + |E|)).
        
    - Check if ((u, v)) is an edge: (O(\deg(u))) (linear in list length).
        

---

### 3.2 Adjacency Matrix

- ( |V| \times |V| ) matrix (A) where:
    
    - (A[u, v] = 1) if edge ((u, v)) exists, else (0).
        
- Space:
    
    Θ(∣V∣2)\Theta(|V|^2)
- Pros:
    
    - Check adjacency ((u, v)) in (O(1)).
        
- Cons:
    
    - Bad for sparse graphs (wastes space).
        
- Operations:
    
    - Check if ((u, v)) is an edge: (O(1)).
        
    - Iterate neighbors of (u): (O(|V|)) (scan row/column).
        

---

## 4. DAGs and Topological Sort

### 4.1 DAGs

- A **DAG**: Directed Acyclic Graph.
    
    - No directed cycles.
        
- A **topological order** of a DAG:
    
    - Ordering of vertices (v_1, \dots, v_n) such that for every edge ((u, v)), (u) comes **before** (v) in the order.
        

---

### 4.2 Kahn’s Algorithm (Indegree-Zero Topological Sort)

**Idea:** Always pick a vertex with indegree 0, output it, and remove it and its outgoing edges.

```text
TOPOLOGICAL-SORT(G = (V, E)) using indegrees
1  for each v in V:
2      indeg[v] = number of incoming edges to v
3  Q = { v in V | indeg[v] = 0 }   # can be a queue
4  order = [ ]
5  while Q is not empty:
6      u = remove any vertex from Q
7      append u to order
8      for each (u, v) in E:
9          indeg[v] -= 1
10         if indeg[v] == 0:
11             add v to Q
12 if |order| < |V|:
13     # there was a cycle
14     report "no topological order (graph has cycle)"
15 else:
16     return order
```

- Complexity:
    
    - Initial indegree computation: (O(|V| + |E|))
        
    - Main loop: each edge processed once ⇒ (O(|V| + |E|)) total.
        

**Correctness sketch:**

- If there is a cycle, some vertices on the cycle can never reach indegree 0 ⇒ we detect it via |order| < |V|.
    
- In a DAG, there is always at least one vertex with indegree 0, and removing it preserves DAG-ness. Repeating gives valid topological order.
    

---

### 4.3 DFS-Based Topological Sort

Alternative approach using DFS finishing times:

1. Run DFS on the directed graph (G).
    
2. Record finishing time (f[u]) when each vertex’s recursion finishes.
    
3. Sort vertices in **decreasing** order of (f[u]).
    

Algorithm:

```text
TOPOLOGICAL-SORT-DFS(G)
1  DFS(G)   # compute discovery & finish times d[u], f[u]
2  return vertices sorted in decreasing order of f[u]
```

Key property to prove:

- For every edge ((u, v)) in a DAG, we have:
    
    f[v]<f[u]f[v] < f[u]
- Therefore, sorting by decreasing (f[\cdot]) gives a valid topological order.
    

Reason (sketch):

- When exploring edge ((u, v)), (v) cannot be gray (would be a back edge).
    
- In a DAG, DFS has **no back edges** (see cycle detection below).
    
- If (v) is white, DFS will fully explore (v) before finishing (u) ⇒ (f[v] < f[u]).
    
- If (v) is black, (v) is already done ⇒ also (f[v] < f[u]).
    

---

## 5. Depth-First Search (DFS)

### 5.1 DFS Algorithm (Recursive Version)

Standard DFS on directed or undirected graphs:

- Maintain:
    
    - `color[u] ∈ {WHITE, GRAY, BLACK}`
        
        - WHITE: not yet discovered
            
        - GRAY: discovered, in recursion stack
            
        - BLACK: finished
            
    - Discovery time (d[u])
        
    - Finishing time (f[u])
        
    - Parent (\pi[u]) in DFS forest
        
    - Global time counter `time`
        

```text
DFS(G):
1  for each vertex u in V:
2      color[u] = WHITE
3      pi[u] = NIL
4  time = 0
5  for each vertex u in V:
6      if color[u] == WHITE:
7          DFS-VISIT(G, u)

DFS-VISIT(G, u):
1  color[u] = GRAY
2  time = time + 1
3  d[u] = time
4  for each v in Adj[u]:
5      if color[v] == WHITE:
6          pi[v] = u
7          DFS-VISIT(G, v)
8  color[u] = BLACK
9  time = time + 1
10 f[u] = time
```

- Complexity:
    
    - (O(|V| + |E|)) (each vertex/edge processed constant number of times).
        

DFS produces a **DFS forest**:

- Each tree corresponds to one call to `DFS-VISIT` from the outer loop.
    
- For connected undirected graphs, it’s a single DFS tree.
    

---

### 5.2 Parenthesis Theorem

- Each vertex (u) has an interval ([d[u], f[u]]) on the time line.
    
- **Parenthesis Theorem:**
    
    - For any two vertices (u, v), their intervals are either:
        
        - Disjoint, or
            
        - One is entirely contained in the other.
            
    - Moreover, (v) is a descendant of (u) in the DFS tree iff:
        
        d[u]<d[v]<f[v]<f[u]d[u] < d[v] < f[v] < f[u]
- Intuition: Recursion behaves like properly nested parentheses; descendants are “inside” their ancestors’ intervals.
    

---

### 5.3 White-Path Theorem

**White-Path Theorem:**

- In a DFS forest of a (directed or undirected) graph (G), a vertex (v) is a descendant of (u) **iff**, at time (d[u]), there exists a path from (u) to (v) consisting **only of white vertices**.
    

Intuition:

- When DFS discovers (u), all vertices on a path of whites reachable from (u) will get explored recursively before we finish (u), so they become descendants.
    
- Conversely, any descendant is reached along a path that was white at the time of discovery.
    

This theorem is particularly useful in proofs about DFS and in correctness arguments for algorithms like cycle detection and DFS-based topological sort.

---

### 5.4 Edge Classification (Directed Graphs)

During DFS on a **directed** graph, edges can be classified by colors and times:

- **Tree edges**:
    
    - Edges in the DFS forest (used to discover new vertices).
        
- **Back edges**:
    
    - Edges from a vertex to one of its **ancestors** in a DFS tree.
        
- **Forward edges**:
    
    - Non-tree edges from a vertex to one of its **descendants**.
        
- **Cross edges**:
    
    - All other edges (between different subtrees, or “sideways”).
        

Key fact (for **DAGs**):

- A directed graph has a cycle **iff** DFS finds a **back edge**.
    
- So:
    
    - **No back edges** ⇔ the graph is a DAG.
        

This is exactly what’s used in the proof that DFS-based topological sort works: if a back edge exists, the graph is not a DAG, and topological order doesn’t exist.

---

### 5.5 Detecting Cycles via DFS

For a directed graph (G):

- Run DFS.
    
- If any edge ((u, v)) is discovered with `color[v] == GRAY` (meaning (v) is an ancestor of (u) in the recursion stack), then:
    
    - ((u, v)) is a **back edge**, and there is a cycle.
        

Lemma:

- A directed graph has a cycle ⇔ DFS discovers at least one back edge.
    

---

## 6. Homework 4 – Key Ideas & Example Algorithms

HW4 problems give concrete applications of the lecture material.

---

### 6.1 Reconstructing a Rooted Tree from Its Pedigree Graph

**Problem setup:**

- We are given a rooted tree (T = (V, E)).
    
- Its **pedigree graph** (G = (V, E')) has:
    
    - Same vertex set (V).
        
    - Edge ((u, v) \in E') iff (v \neq u) and (v) is a descendant of (u) in (T).
        
- But we are only given (G) (the pedigree graph) and must reconstruct the original tree (T).
    

**Observations:**

- For a vertex (v):
    
    - Its ancestors in (T) are exactly its **in-neighbors** in (G):
        
        A(v)={u∈V:(u,v)∈E′}A(v) = \{ u \in V : (u, v) \in E' \}
- In a rooted tree:
    
    - The ancestors of (v) lie on a unique path from the root to (v).
        
    - Thus (A(v)) is **totally ordered** by the ancestor relation.
        
- The **parent** (p(v)) is the ancestor closest to (v):
    
    - The **unique maximal** element of (A(v)) under the ancestor relation.
        

**How to compare ancestors in (G):**

- For distinct (x, y \in A(v)), one of them is an ancestor of the other.
    
- So in the pedigree graph:
    
    - Exactly one of ((x, y)) or ((y, x)) is in (E').
        
- This induces a total order on (A(v)) using the directed edges of (G).
    

**Algorithm (high-level):**

1. Preprocess:
    
    - Build in-neighbor lists to get incoming edges:
        
        - `in_neighbors[v] = { u : (u, v) ∈ E' }`
            
    - This takes (O(|V| + |E'|)).
        
2. For each vertex (v \in V):
    
    - Compute ancestor set (A(v) = \text{in_neighbors}[v]).
        
    - If (A(v) = \emptyset), then (v) is the **root** (unique).
        
3. If (A(v) \neq \emptyset), find **parent** (p(v)):
    
    ```text
    pick any p in A(v)
    for each w in A(v):
        if w == p: continue
        if (w, p) in E': 
            # w is an ancestor of p (higher)
            # p is deeper, so keep p
        else if (p, w) in E':
            # p is an ancestor of w (shallower)
            # so w is closer to v
            p = w
    # after the loop, p is the maximal ancestor in A(v)
    parent[v] = p
    ```
    
4. Build the tree:
    
    ```text
    E = { (parent[v], v) : v ∈ V and A(v) ≠ ∅ }
    root = the unique v with A(v) = ∅
    T = (V, E)
    ```
    

**Why this matches the lecture themes:**

- Uses the concept of **rooted trees**, **ancestor/descendant**, and directed edges.
    
- Exploits that in a rooted tree, ancestors of a vertex form a **chain** (like DFS ancestor chains).
    
- The partial order from the tree is encoded as directed edges in (G).
    

---

### 6.2 Testing Whether an Undirected Graph Is a Tree in (O(m)) (Average Case)

We are given an undirected graph (G) with (n) vertices and (m) edges, via adjacency lists. Need an (O(m)) average-case algorithm to check if (G) is a tree.

**Key characterization:**

- An undirected graph (G) is a tree iff:
    
    - It is **connected**, and
        
    - It has **no cycles** (acyclic), and
        
    - Equivalently (by tree facts) it has exactly (n - 1) edges.
        

**Algorithm (DFS/BFS with parent tracking):**

```text
IS-TREE(G):
1  pick any vertex r as root
2  Visited = empty hash set
3  Parent  = empty hash map
4  edge_count = 0
5  frontier = {r}
6  Visited.add(r)
7  Parent[r] = NIL

8  while frontier not empty:
9      u = remove some vertex from frontier
10     for each neighbor v of u in adjacency list:
11         if v not in Visited:
12             Visited.add(v)
13             Parent[v] = u
14             frontier.add(v)
15             edge_count += 1   # discovered a new undirected edge {u, v}
16         else if v != Parent[u]:
17             # found a back edge (u, v) → cycle in undirected graph
18             return "G is NOT a tree"

19  k = |Visited|
20  if k != n:
21      # graph is disconnected
22      return "G is NOT a tree"

23  if edge_count != n - 1:
24      # wrong number of edges
25      return "G is NOT a tree"

26  return "G IS a tree"
```

**Cycle check (undirected):**

- If we see an edge ({u,v}) where (v) is already visited and (v \neq \text{Parent}[u]), we’ve found a cycle.
    

**Complexity:**

- Using **hash set** and **hash map**:
    
    - Each insert/lookup is (O(1)) on average.
        
- DFS/BFS traverses each vertex at most once and inspects each edge a constant number of times.
    
    - Total time: (O(m)) on average.
        
- No need to initialize (O(n))-sized arrays; using hash tables avoids an extra (O(n)) term.
    

**Connections to lecture:**

- Uses the **graph connectedness** notion.
    
- Uses “tree facts” such as “tree with (n) vertices has (n-1) edges”.
    
- Uses a search algorithm (DFS/BFS) plus the idea of **back edges** to detect cycles.
    

---

## 7. Quick Comparison: BFS vs DFS (Conceptual)

Just to orient this topic with previous material:

- **BFS**:
    
    - Explores neighbors in **layers** (shortest path in unweighted graphs).
        
    - Uses a queue; good for shortest path and connectedness.
        
- **DFS**:
    
    - Explores as deep as possible along each branch (recursive/stack).
        
    - Produces **DFS tree**, discovery/finish times, and supports:
        
        - Edge classification (tree/back/forward/cross).
            
        - Topological sorting (for DAGs).
            
        - Cycle detection (via back edges).
            
        - Structure theorems like Parenthesis and White-Path Theorems.
            

Both are (O(|V| + |E|)) but have very different structural uses, as highlighted in this lecture and HW4.
