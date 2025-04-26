# 2048 Protoboard Enumeration Program

This program is a simple enumeration program to calculate all possible "protoboards" of the game 2048. A protoboard is defined to be a 4x4 binary board in which cells are either "empty" or "filled". This allows us to categorize the sum possible "shapes" that might appear in 2048 boards, regardless of tile types.

## Premise [What is a "protoboard"?]

We consider that the game 2048 takes place on a 4x4 square grid. Assuming win conditions are a 2048 tile being present on the board (classic 2048 rules), we have 11 possible tiles in 2048, ranging from 2^1=2 to 2^11=2048. One might naively assume empty cells in the grid must then be something of "zero" tiles but this is false. In order to continue the "rule" that tiles are powers of two, it would imply that 2^0=1 should be a tile. 2048, however, has no "1" tile and therefore has no zero tile either. By rule, 2048 only has {2^1, 2^2, 2^3, ... 2^11} tiles, meaning empty cells are **not** zero tiles, they are fundamentally *different* from tiles themselves. Thus, it is proactive to consider what I call protoboards. These are 4x4 matrices that contain binary cells that are either empty or tiled. This tells us all possible permutations of 2048 without considering *what specific tiles* are present on the board. My further work will explain *why* we might want to do this (it has to do with solving what I call "classic" or "traditional" 2048), but, for now suspend your disbelief and go along for the ride!

### Method

Okay, that's all great, but how do we find the number of possible protoboards?

Well, this can be expressed as a simple combinatorics problem, but first, we need to realize one particular thing before we begin: 

    In 2048, you start with exactly two tiles. Every time one makes a "move" (we can define a move in 2048 as shifting the tiles *and* the appearance of the new tile; a ply
    might be considered just *one* of those events in isolation), there is necessarily an additional tile added to the board. Even if one manages to merge *every* existing
    tile on the board down to a single tile, there is always an additional tile added that is not considered in the first ply of the move. Thus, the initial number of two
    tiles represents the *minimum* number of tiles for any valid 2048 board. More concisely, boards with one or zero tiles are *not* valid game states.

This isn't *strictly* necessary to consider, as it only eliminates 17 possible protoboards, but it will be important for symmetry later on. Considering the concept of a protoboard, we can reason that the pertinent information about a given board is the number of "tiles" on it (we will define "filled" protoboard cells to be "tiles"). We will call this variable *t*, for tiles! It is then obvious that on a 4x4 grid, we have 16 "categories" of protoboards, based on how many tiles, or what value of *t*, a given protoboard has.

Now, generally, on a 4x4 grid-board with *two* discrete options for each square of the grid, we can say there are (cell types = tiles + cells = 2)^(total num of board cells = 16). Less generally, we have 2^16=65536 possible protoboards for a 4x4 game board with two possible entries for a given cell. However, notice that t=0 and t=1 are not valid board states. So, how did I know that these t-values represent specifically *17* of the possible *65536* protoboards for a 4x4 grid? We need a way to associate t-values to protoboards somehow, and we can do just that with a binomial coefficient, C(n, k). Considering C(n=16, k=t) for a given t-value, we can get the specific portion of the sum 65536 possible protoboards that a given *category defined by t-value* takes up. This is because a binomial coefficient represents the number of ways to choose *t* items from *n* with no regard to order, which is exactly what we are doing here! This also explains how we got 17 earlier, as C(16, 0) = 1 [an empty board] and C(16, 1) = 16 [a tile in every given possible position]. That means that there are *17* total boards that are not possible from the outset that we can readily exclude from here on out, bringing out total protoboard count *down* from 2^16 to 2^16 - 17 = 65519 possible protoboards.

This is exactly what this small Rust program *does*. We know there are 65519 possible protoboards and this lets us *categorize* and *index* them all in a manner that is logically consistent, and importantly, reversible. For example, for t=2, meaning "2048 boards that have two and only two tiles on them" (this includes all starting boards, note!), we can calculate C(16, 2) = 120, meaning that of the possible 65519 shapes a 2048 board can legally take, 120 of them represent board shapes with only two tiles.

---

### Addendum


t = 2: 120 boards

t = 3: 560 boards

t = 4: 1820 boards

t = 5: 4368 boards

t = 6: 8008 boards

t = 7: 11440 boards

t = 8: 12870 boards

t = 9: 11440 boards

t = 10: 8008 boards

t = 11: 4368 boards

t = 12: 1820 boards

t = 13: 560 boards

t = 14: 120 boards

t = 15: 16 boards

t = 16: 1 boards


The program will generate a small table (shown here) that outputs to the command line in addition to storing all indexed protoboards in a human-readable .txt format.

Because, by modern standards, 65519 really isn't *that large* of a number: we can just naively bruteforce all these possible boards, which is what the program does. Depending on hardware, this should likely take milliseconds at most. Astute readers will notice that this table is *symmetric* about t=8, if we realize that t=15 and t=16 account for 17 boards similar to the values for t=0 and t=1 we initially excluded. This is a general property of binomial coefficients, which can be stated as either C(n, k) is symmetric about k=n/2 *or* C(n, k) == C(n, n - k). This symmetry will be important later for optimizing, because while we only have to consider the relatively small number of protoboards, once we start to consider actual tile values we end up getting **massive** numbers of possible boards *generally* for the game. 

To realize *just how large* this number gets, we can relate this framework to the tileset system in 2048. Tiles in 2048 are a power of two, with lowest being 2 and the highest being, well, 2048 (if we abide by the "classic" 2048 defintion earlier). Defining our winning condition as the presence of a 2^11=2048 tile, we can consider there to be **11** possible tiles in legal, classic 2048 gameplay {2^1, 2^2, 2^3, ... 2^11}. 
This means, *for a* ***single*** *given protoboard*, there are **11^t** possible states when considering unique tiles, unlike when generalizing to tiles vs empty cells. Considering we have 65519 possible protoboards, and **each** one represents 11^t possible real board states, the real number of possible 2048 games is in the order of billions or quintillions of games. 11^8=214358881 alone, and having 12870 possible protoboards for t=8 means we have (11^8) * (12870) = **2.7587988e+12** possible real boards *just for boards with eight tiles alone*! This is, obviously, intractible.

Next write up we will explore exploiting both symmetry of binomial coefficients and the symmetry of the 2048 board itself to *massively* reduce this number, as well as anticipate a method for cataloguing **all** possible 2048 boards using techniques similar to Jonathan Basile's *genius* project, libraryofbabel.info in order to create something of a "Library of 2048". This will be the first step in devising a general solution/strategy for what we have defined as classic 2048. How fun!
