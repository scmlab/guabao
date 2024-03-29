---
title: Guarded Command Language Overview
---

Guabao uses a variation of Guarded Command Language based on that in [Anne Kaldewaij's book](5-references.html), with slight variation in syntax.

As a running example, the following program computes the greatest common divisor (`gcd`) of constants `A` and `B` and stores it in `r`, using what is perhaps one of the oldest algorithm.

```
con A, B : Int { A > 0 ∧ B > 0}
con gcd : Int -> Int -> Int
var x, y : Int

x, y := A, B

{ gcd x y = gcd A B ∧ x > 0 ∧ y > 0,
  bnd: x + y }
do x < y -> y := y - x
 | x > y -> x := x - y
od
{ x = gcd A B }
```

From this example one may see that
the main program consists of

  * a declaration section,
  * followed by the main program statements, and
  * ends with a postcondition written in an assertion.

# Declarations

Each declaration starts with a `con` or a `var` keyword, respectively denoting constants and variables. Constant declarations can be followed by an optional assertion containing properties the constants are assumed to hold.

In the GCD program, for example, we declared two constants `A` and `B` of type `Int`, a constant `gcd` having type `Int -> Int -> Int`, and two variables `x` and `y` having type  `Int`.

```
con A, B : Int { A > 0 ∧ B > 0}
con gcd : Int -> Int -> Int
var x, y : Int
```

# Types

Base types include `Int`, `Char`, and `Bool`. Function types are denoted by `t1 -> t2`, currently used only in specifications.

The type of arrays is denoted by `array interval of type`, where `interval` can be either opened or closed on both ends, and may refer to declared constants.
In the example below we declared a constant `N`, about which all we know is that it is non-negative, and an array of `Int` whose elements may include `f[0]` but not `f[N]`.
```
con N : Int { N ≥ 0 }
con f : array [0..N) of Int
```

# Declarative Sublanguage

Guabao embeds a small declarative language, in which one can define constants and functions to be used in assertions. In the future we will allow datatype definitions here, and eventually extend it to a more complete functional language.

Currently this language resides in blocks delimited by `{: ... :}`:

```
{:
   even : Int -> Int
   odd  : Int -> Int

   P = ⟨ ∀ i : 0 ≤ i < n : even F[i] ⟩
   sum n = ⟨ ∀ i : 0 ≤ i < n : F[i] ⟩
:}
```

In the code fragment above we declare two functions `even` and `odd`, which can be used in assertions later. Their definitions are not given.
In constrast, `P` and `sum` are fully defined. Note that `P` refers to a free variable `n`, which must be declared already, while `sum` is a function taking an argument `n`.
Simple Hindley-Milner type inference applies to this language, therefore types of `P` and `sum` need not be given.


# Statements

## Assertions

Assertions are `Bool`-valued expressions written in curly brackets.
For example,
```
{ x = gcd A B }
```

An assertion that appears immediately before a loop is the invariant of the loop.
In this case we also need to specify, using the keyword `bnd`, the bound of the loop. The following is the invariant used in the GCD algorithm:
```
{ gcd x y = gcd A B ∧ x > 0 ∧ y > 0, bnd: x + y }
```
The bound must be an `Int`-valued expression.
In a completed program, each loop must have an invariant and a bound.

When an assertion that has a `bnd` part does not appear before a loop, the `bnd` part is ignored.

## Abort, Skip, and Assignment

The statement `abort` aborts the program.

The statement `skip` does nothing.

Variable assignment is denoted by `:=`.
Simultaneous assignment of multiple variables is allowed for simple variables.
For example, the following statement simultaneously assigns the value of `y` to `x`, and the value of `x+y` *before the assignment* to `y`:
```
x, y := y, x+y
```

Given an array `f`, elements in it can be updated by `f[i] := e` where `i` is an `Int`-valued expression. For example:
```
f[f[0]] := 1
```
However, simultaneous assignment is currently not allowed for array updating.

## Guarded Commands

A *guarded command* has the form `B -> S` where `B`, the "guard", is a `Bool`-valued expression,
and `S` is a (sequence of) program statement (guarded by `B`).
Guarded commands cannot appear alone,
but are essential building blocks of program constructs below.

## Conditional Branching

Conditional branching is denoted by
`if B0 -> S0 | B1 -> S1 | ... | Bn -> Sn fi`,
where `B0 -> S0`.. `Bn -> Sn` are guarded commands.
The operational interpretation is that the computer may execute an arbitrary `Si` whose corresponding guard `Bi` evaluates to `True`.
When none of the guards is `True`, the program aborts.

When written in multiple lines, Guabao uses ident-aware parsing to separate the guarded commands.
For example:
```
if even b -> a := a * 2
             b := b / 2
 | odd  b -> c := a + c
             b := b - 1
fi
```

## Loop

A loop is denoted by
`do B0 -> S0 | B1 -> S1 | ... | Bn -> Sn od`,
where `B0 -> S0`.. `Bn -> Sn` are guarded commands.
For example, this is a loop enclosing two guarded commands:
```
do p ≠ 0 ∧ mod p x = 0 -> y, p := y + 1, p - x
 | p ≠ 0 ∧ mod p y = 0 -> x, p := x + 1, p - y
od
```
The loop above

* performs `y, p := y + 1, p - x` if `p ≠ 0 ∧ mod p x = 0`, and
* performs `x, p := x + 1, p - y` if `p ≠ 0 ∧ mod p y = 0`.
* If both guards are true, the computer is free to choose any branch.
* If both guards are false (for example when `p = 0`), the loop terminates.

# Expressions

We currently support

* arithmetic operations: `+, -, *, /`,
`↑` and `↓` for (binary) maximum and minimum, and `^` for exponential.
* Binary relations: `=, ≠, <, ≤, ≥, >`. One may also write `<=` and `>=`.
* Logical operators: `¬, ∧, ∨, ⇒`. One may also wrote `&&` and `||` for conjunction and disjunction.

Regarding quantification, we use the Eindhoven notation, e.g. `⟨ Σ k : i ≤ k < j : f[k] ⟩`.

A list of key combinations to for inputting unicode characters can be found [here](3-keyscombs.html).

# Comments

We currently allow two style of comments:

```
-- single line comments, and

{- blocked, multiline comments
    {- that can be nested -}
  -}
```
