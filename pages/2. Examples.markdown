---
title: Examples
---

For a quick look of the language, here are some sample programs in Guabao's variation of Guarded Command Language.

# Greatest Common Divisor

The following program computes the greatest common divisor (`gcd`) of constants `A` and `B` and stores it in `r`, using what is perhaps one of the oldest algorithm.

Note the `do` notation for while loops, the guarded commands, and that every loop must have an invariant (in this case `gcd x y = gcd A B ∧ x > 0 ∧ y > 0`) and a bound (in this case `x + y`).

```
con A, B : Int { A > 0 ∧ B > 0}
var gcd : Int -> Int -> Int
var x, y : Int

x, y := A, B

{ gcd x y = gcd A B ∧ x > 0 ∧ y > 0,
  bnd: x + y }
do x < y -> y := y - x
 | x > y -> x := x - y
od
{ x = gcd A B }
```

# Fast Multiplication

Shown below is a `O(log B)` algorithm for computing `A * B`, using only addition, subtraction, multiplication and division by two, and parity test.

```
con A, B : Int { A ≥ 0 ∧ B ≥ 0 }
con even, odd : Int -> Bool
var r, a, b : Int

a, b, r := A, B, 0
{ a * b + r = A * B ∧ 0 ≤ b, bnd: b }
do b ≠ 0 ->
    if even b -> a := a * 2
                 b := b / 2
     | odd b ->  r := r + a
                 b := b - 1
    fi
od
{ r = A * B }
```

* The purpose of the program is stated in the postcondition `{ r = A * B }`.

* Loop and conditional branches are respectively denoted by `do...od` and `if...fi`, both accepting guarded commands in their bodies. Notice the  indent-aware syntax.

* The loop invariant is `a * b + r = A * B ∧ 0 ≤ b`, and the bound is `b`.

Recall the screen shot:

![](../images/screenshot-v0.2.3.jpg)

* The loop invariant is abbreviated to `INV` in the declaration block `{: ... :}` on line 5.
* As the program is being written, Guabao instantly parses the program and infers verification conditions yet to be proved to show that the program meets the postcondition.
 In this example, the pane on the right hand side shows that one ought to prove that the first branch of the `if` construct preserves the loop invariant. That is, the loop invariant `INV` and the guards `b ≠ 0` and `even b` implie a postcondition, obtained by applying substution `[a * 2, (b / 2) / a, b]` to the loop invariant `INV`.

* In line 16 we use `b := b - 1` to decrease the bound `b`. With this we can meet another verification condition demanding that the bound always decrease.

* In the screen shot we have yet to figure out what to fill in between line
13 - 15. However, Guabao has infered for us the pre and postconditions of the specification, from which one may conclude that `r := r + a` is the most sensible statement to use.

# Maximum Segment Sum

One cannot talk about program derivation without mentioning the maximum segment sum problem: given an array of (possibly negative) integers, compute the largest possible sum of any consecutive segment.

Notice definition of functions within `{: ... :}`,
and use of Eindhoven quantifier notation, e.g. `⟨ Σ k : i ≤ k < j : f[k] ⟩`.

```
con N : Int { N ≥ 0 }
con f : array [0..N) of Int
var s, r, n : Int

{: sum i j = ⟨ Σ k : i ≤ k < j : f[k] ⟩ :}
{: P n = (s = ⟨ ↑ i j : 0 ≤ i ≤ j ≤ n : sum i j ⟩) :}
{: Q n = (r = ⟨ ↑ i : 0 ≤ i ≤ n : sum i n ⟩) :}

s, n := 0, 0
{ P n ∧ Q n ∧ 0 ≤ n ≤ N , bnd: N - n }
do n ≠ N ->
    r := (r + f[n]) ↑ 0
    s := s ↑ r
    n := n + 1
od

{ s = ⟨ ↑ i j : 0 ≤ i ≤ j ≤ N :
          ⟨ + k : i ≤ k < j : f[k] ⟩ ⟩ }
```

# Integer Division and Reminder

Another textbook algorithm computing the quotient and reminder of `A/B` ---
that is, find `q` and `r` such that `A = q * B + r` and `0 ≤ r < B`.
Notice the use of Eindhoven style existentional quantifion.

```
con A, B : Int { A ≥ 0 ∧ B > 0 }
var q, b, r : Int

{: PowOf2 b = ⟨ ∃ k : 0 ≤ k : b = B * 2^k ⟩ :}

b := B
{ PowOf2 b, bnd: A - b }
do b ≤ A -> b := b * 2 od
{ PowOf2 b  ∧  b > A }

q, r := 0, A
{ A = q * b + r  ∧  PowOf2 b  ∧  0 ≤ r < b, bnd: b }
do b ≠ B ->
   q, b := q * 2, b / 2
   { A = q * b + r  ∧  PowOf2 b ∧  0 ≤ r < b * 2 }
   if r < b -> skip
    | r ≥ b -> q, r := q + 1, r - b
   fi
od
{ A = q * B + r  ∧  0 ≤ r < B }
```
