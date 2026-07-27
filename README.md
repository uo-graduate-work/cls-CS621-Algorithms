# CS 621 — Analysis of Algorithms

Personal archive of coursework from **CS 621, Analysis of Algorithms** (graduate), taught by
**Prof. Tao Hou**, Fall 2025. Kept as a reference for the core algorithm-design paradigms and
complexity theory covered in the course.

This is a **private backup** — see [Attribution](#attribution) before sharing it.

## Contents

| Path | What's in it |
|---|---|
| [`notes/`](notes/) | Study guides written up in Markdown, one directory per unit |
| [`slides/`](slides/) | Lecture slide decks (PDF, 556 pages total) |
| [`homework/`](homework/) | HW1–HW4 solutions (LaTeX → PDF) |

## Units

Notes and slides use matching numbering, so `notes/2-greedy/` corresponds to `slides/2-greedy.pdf`.

| # | Topic | Notes | Slides |
|---|---|---|---|
| 0 | Runtime analysis & asymptotics | [notes](notes/0-runtime-analysis/notes.md) | [54 pp](slides/0-runtime-analysis.pdf) |
| 1 | Divide & conquer | [notes](notes/1-divide-conquer/notes.md) | [87 pp](slides/1-divide-conquer.pdf) |
| 2 | Greedy algorithms | [notes](notes/2-greedy/notes.md) | [133 pp](slides/2-greedy.pdf) |
| 3 | Dynamic programming | [notes](notes/3-dynamic-programming/notes.md) | [108 pp](slides/3-dynamic-programming.pdf) |
| 4 | Elementary graph theory | [notes](notes/4-elem-graph-theory/notes.md) | [105 pp](slides/4-elem-graph-theory.pdf) |
| 7 | NP-completeness | [notes](notes/7-np-theory/notes.md) | [69 pp](slides/7.1-NP-theory.pdf) |

> **Gap:** units 5 and 6 were never written up, and no slides for them were saved. The numbering
> jumps from 4 to 7 to stay consistent with the course's own unit numbers.

## Topic index

Where to look for a specific algorithm or result.

**Unit 0 — Runtime analysis**
Time-complexity functions and input size · insertion sort analysis · Big-O / Ω / Θ · runtimes as
sets · transitivity and additivity · limits and L'Hôpital's rule · polynomial vs log vs exponential
hierarchy · polynomial time as "efficient" · Fibonacci, exponential vs linear.

**Unit 1 — Divide & conquer**
Merge sort and its recurrence · Master Theorem · iteration/expansion method · substitution
(induction) method · quicksort · randomized quickselect · deterministic linear-time selection
(median of medians) · closest pair of points · Karatsuba integer multiplication · Strassen matrix
multiplication.

**Unit 2 — Greedy**
Fractional knapsack · interval scheduling ("stays ahead" and exchange arguments) · interval
partitioning · minimizing maximum lateness (EDF) · induction templates and greedy proof patterns.

**Unit 3 — Dynamic programming**
DP vs divide & conquer · the 4-step DP recipe · longest increasing subsequence · text segmentation
(word break) · 0–1 knapsack · edit distance · matrix-chain multiplication.

**Unit 4 — Graph theory**
Graph and subgraph definitions · paths, cycles, degrees, connectivity · trees and forests ·
adjacency list vs matrix · DAGs · topological sort (Kahn's and DFS-based) · DFS · parenthesis
theorem · white-path theorem · edge classification · cycle detection · BFS vs DFS.

**Unit 7 — NP-completeness**
Problems vs algorithms · decision vs optimization problems · P, NP, EXP, NP-complete · certificates
and verifiers · polynomial-time reductions · P vs NP · proving NP-completeness.

## Reading the notes

The Markdown files use LaTeX math in `$...$` and `$$...$$` delimiters. They render correctly in
Obsidian, and on GitHub, which supports `$`-delimited math natively. Plain text editors will show
the raw LaTeX.

Several note files reference specific homework problems (e.g. unit 2 §7 works through the HW2
water-stops problem), so the notes and `homework/` are best read together.

## Attribution

The slide decks in `slides/` were authored by **Prof. Tao Hou** and are his course material,
included here only as a personal reference copy. They are not mine to redistribute — keep this
repository private, and don't republish `slides/` elsewhere.

The notes and homework solutions are my own work. The homework PDFs are graded submissions;
publishing them where current students could find them would be an academic-integrity problem.
