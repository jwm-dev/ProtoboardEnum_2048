# ProtoboardEnum_2048

**Enumerating All Valid Protoboards in Classic 2048**

This Rust program enumerates all valid "protoboards"—binary representations of 2048 game boards where each 4×4 cell is either filled or empty. This abstraction allows analysis of the spatial structure of 2048 boards without considering specific tile values, providing a foundation for deeper combinatorial and probabilistic research on the game.

---

## 🔍 What Is a "Protoboard"?

A protoboard is a 4×4 grid that defines whether each cell is **filled** (contains a tile) or **empty**. Unlike actual 2048 boards that include tile values (powers of two), protoboards focus purely on the **presence** or **absence** of tiles. This abstraction is useful for categorizing the structural forms of possible 2048 game states.

### Why This Abstraction?

- In 2048, tiles are always powers of two: \(2^1=2\) through \(2^{11}=2048\).
- **Empty cells are not zero tiles**—they are *categorically distinct*.
- Because every game starts with exactly two tiles and adds one new tile per move, valid boards can never have fewer than two tiles.

By categorizing over the number of filled cells, ``t`` , we can compute all valid binary protoboards using combinatorics.

---

## 📐 Methodology

We define $\( t \in \{2, 3, ..., 16\} \)$ as the number of tiles (filled cells) on a 4×4 grid. There are $\binom{16}{t}$ protoboards for each value of $\( t \)$, as each combination corresponds to one specific filled pattern.

The total number of possible binary protoboards is:

$\[ 2^{16} = 65536 \]$

However, $\binom{16}{0} = 1$ and $\binom{16}{1} = 16$ represent boards with zero or one tile, which are **explicitly** invalid under 2048 rules. We exclude these 17 cases:

$\text{Valid protoboards} = 65536 - 17 = 65519$

This program enumerates and indexes all 65519 valid protoboards. We don't consider whether a board is *reachable* through legal moves, however. This is for research purposes, some "illegal" boardstates (excluding the aformentioned 17) might be of interest when it comes to solving the stochastic variant of the game.

### Summary Table of Protoboards by Tile Count \( t \):

```text
t = 2:    120

t = 3:    560

t = 4:   1820

t = 5:   4368

t = 6:   8008

t = 7:  11440

t = 8:  12870

t = 9:  11440

t = 10:  8008

t = 11:  4368

t = 12:  1820

t = 13:   560

t = 14:   120

t = 15:    16

t = 16:     1
```

This table is symmetric due to the identity $\( \binom{n}{k} = \binom{n}{n-k} \)$. This symmetry will later be used for computational and storage optimizations.

---

## 🚀 What This Program Does

- Iterates through $\( t = 2 \)$ to $\( t = 16 \)$
- Uses $\( \binom{16}{t} \)$ to enumerate all valid protoboard layouts
- Outputs:
  - A summary table of board counts by $\( t \)$
  - A `protoboards.txt` file with each board's binary representation and index

> Generation time: negligible on modern hardware (~milliseconds)

---

## 🔢 Why This Matters

Each protoboard represents a **template** into which actual tile values can be inserted. Given 11 valid tile values in 2048, a single protoboard with \( t \) tiles yields:

$\[ \text{Real boards per protoboard} = 11^t \]$

The full number of actual game states is:

$\[ \sum_{t=2}^{16} \binom{16}{t} \cdot 11^t \]$

For example:
- $\( t = 8 \)$: $\( 11^8 = 214,358,881 \)$
- $\( \binom{16}{8} = 12870 \)$
- $\( 214,358,881 \cdot 12870 = 2.76 \cdot 10^{12} \)$ boards *just for* $\( t = 8 \)$

This makes brute-force analysis of full 2048 states intractable—but protoboards give us a tractable entry point.

---

## 🛠️ How to Use

```bash
git clone https://github.com/jwm-dev/ProtoboardEnum_2048.git
cd ProtoboardEnum_2048
cargo run --release
```

- Output will go to `protoboards.txt` and print summary stats to terminal.
- File is usable by external tools like [`BoardViewer_2048`](https://github.com/jwm-dev/BoardViewer_2048)

---

## 📌 Next Steps

Future work will:
- Leverage symmetry to compress and index tile arrangements
- Merge tile value enumeration with spatial protoboards to create a deterministic 2048 state engine
- Serve as foundation for a solvable model of classic 2048

For a fully integrated GUI version with board browsing, see: [`LibraryOf2048`](https://github.com/jwm-dev/LibraryOf2048)

---

*This is the foundational layer of the "Library of 2048": a combinatorially exhaustive map of every board the game can present.*
