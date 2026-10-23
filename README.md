# FingerTree

A Haskell implementation of finger trees: one persistent data structure that becomes a sequence, a priority queue, a heap, a segment tree, or an interval tree depending on how you measure it.

| Operation | Cost |
|---|---|
| Add or remove at either end | O(1) amortized |
| Index, update, or search by measure | O(log n) |
| Concatenate two trees | O(log n) |

## Why it is interesting

A finger tree caches a summary, called a *measure*, at every node. The measure is any monoid: a value with an associative "combine" and an empty element. Change the monoid and you get a different data structure from the same code.

| Type alias | Measure | You get |
|---|---|---|
| `Seq a` | `Size` | Random-access sequence with `!`, `adjust`, `replaceAt` |
| `Heap a` | `Minimax a` | Min-max heap with `popMin`, `popMax` |
| `PriorityQueue a` | `Minimax Int` | Queue ordered by priority |
| `SegmentTree a` | `Segment a` | Indexed sequence that also tracks the sum |
| `IntervalTree` | `Minimax Int` | Overlap queries with `overlapInclusive`, `overlapExclusive` |

```haskell
class (Monoid m, Eq m) => Measured a m where
    measure :: a -> m

type Seq a        = FingerTree Size (Value a)
type Heap a       = FingerTree (Minimax a) (Value a)
type IntervalTree = FingerTree (Minimax Int) Interval
```

Other details:

- A `Structure` type class gives `FingerTree` and its node type `Vec` the same API: `<|`, `|>`, `popL`, `popR`, `mapS`, `traverseS`.
- `mapS` changes the measure type while mapping. A plain `Functor` instance cannot do that, because the measure depends on the element type.
- `IsList` instances let you write trees as list literals with `OverloadedLists`.

## Tests

22 QuickCheck properties check laws such as `popL` then `<|` returns the original tree, and that `mapS` round-trips.

```sh
cd FingerTree
cabal test
```

API docs, generated with Haddock, are in `FingerTree/doc/html/FingerTree/index.html`.

Built for the courses Programming II and Non-procedural Programming at Charles University (2019).
