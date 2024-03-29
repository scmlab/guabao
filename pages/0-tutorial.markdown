---
title: Tutorial
---

<small>**Disclaimer** This is a quick tutorial on interacting with the Guabao environment.
We will give a quick grasp of the program derivation methodology that Guabao supports.
However, this is certainly not a sufficient introduction to the methodology itself.
For that we refer the readers to materials in the [References](5-references.html).</small>

## Installation

To install Guabao you must have [Visual Studio Code](https://code.visualstudio.com/). You can install Guabao by searching for the extension "Guabao" in the editor, or through the [Extensions Marketplace](https://marketplace.visualstudio.com/items?itemName=scmlab.guabao).
A one-click installation downloads the frontend as well as pre-compiled backend.

[Z3](https://github.com/Z3Prover/z3) has to be installed separately and locatable from `$PATH`. You do not need Z3 to run Guabao if you do not use the SMT-solving feature.

## Activation

Once the extension is installed, activate Guabao by creating a new file and saving it with extension `.gcl` (for Guarded Command Language). Guabao uses a variation of [Guarded Command Language](https://en.wikipedia.org/wiki/Guarded_Command_Language).
We will introduce the language as we go in this tutorial. See [Language Overview](1-gcl-overview.html) for a more complete summary.

Shown below is an incomplete GCL program which we will use as our running example.
We declare two constants `A` and `B`, about them all we know is that `B` is non-negative
(as asserted in `{ B ≥ 0 }` --- assertions are enclosed in curly brackets),
and constant functions `even` and `odd`, which we will use later.
Also declared are three variables `r`, `a`, and `b`, all of them having type `Int`.
The goal of the program is to store the value of `A * B` in variable `r`,
as stated in the postcondition `{ r = A * B }`.

```
con A, B : Int { B ≥ 0 }
con even, odd : Int -> Bool
var r : Int

?
{ r = A * B }
```

Copy and paste the code into the editor, save it as a file having extension `.gcl`,
and you should see a screen similar to the screen shot below.

![](../images/tutorial/tutorial00.jpg)

The question mark `?` has been extended to a *spec* --- a hole in the program to be filled in. To complete the program we are supposed in fill in a piece of code that brings the state of the system from precondition `True` to postcondition `r = A * B`.
What we know about global constants (namely `B ≥ 0`) are universally true, and therefore displayed in a separate pane.

We will attempt to complete the task, but the arithmetic operations we use are limited to addition, subtraction, multiplication and division by 2, and `even` and `odd`. This is a common exercise, and was once a useful one for early microcomputers, which did not have an atomic instruction for general multiplication (multiplication by 2 involves only bit-shifting and is much cheaper).

## Introducing a Loop

We will need at least a loop for such a non-trivial task, and
*each loop must have an invariant and a bound*.
Various techniques were developed to construct candidates of loop invariants from the postcondition. We cannot cover them here and refer the readers to the [References](5-references.html) page.
For this problem,
```
a * b + r = A * B
```
is a loop invariant that might work, with bound `b`.

Copy and paste or type the following code into the spec.
To type unicode characters, press '\\' and follow the hints in the drop-down menu. A list of key combinations to for inputting unicode characters can be found [here](3-keyscombs.html).
```
a, b, r := A, B, 0
{ a * b + r = A * B, bnd: b }
do b ≠ 0 ->
    ?
od
```
In the code we initialize the variables `a, b, r` to satisfy the loop invariant.
The guard of the loop is `b ≠ 0`.
The body of the loop is yet to be constructed.

Your current screen may look like:
![](../images/tutorial/tutorial01.jpg)

When the cursor is in the spec, press `ctrl-c-r` to fill in the spec.
You shall see something similar to the screen shot below:

![](../images/tutorial/tutorial02.jpg)

## The Interface

Taking a look at the interface:

The program is in the left pane. In the program in the left pane,
blue shade in the code indicates ``there are proof obligations incurred here.''
Program locations associated with more POs get thicker shades.

The right pane contains information including
* inferred proof obligations (only those on the path of the current location of the cursor are displayed),
* pre/postconditions of *specs* (holes missing code),
* global properties, etc.

Since the number of proof obligations can be large, in the right pane we display those on the path of the current location of the cursor.
In the screen shoot, the right pane shows, among other proof obligations,
```
(a * b) + r = A * B ∧ ¬ b ≠ 0   ⇒   r = A * B
(a * b) + r = A * B ∧ b ≠ 0   ⇒   b ≥ 0
```
The first is proof obligation is labelled "InvBase".
It says that the invariant and the negation of the guard guarantee the postcondition.
The second, labelled "TermBase", says that the bound `b` is non-negative as long as the loop is still running.

Move the cursor around to see other proof obligations.

The proof obligation InvBase is trivial to prove --- in fact, the invariant and the bound were designed to make it trivial. At the top of the box displaying this PO there is a icons of a magic wand.
Clicking on it invokes the SMT solver Z3, which generates the output "Q.E.D.", indicating that it is proved.
The proof obligation labelled TermBase, however, turns out to be falsifiable. Indeed, the premise does not guarantee `b >= 0`!

![](../images/tutorial/tutorial03.jpg)

We thus realise that we need a stronger invariant.
The new invariant would be
```
a * b + r = A * B  ∧  b ≥ 0
```
Update your code accordingly.
You will see that the proof obligations and the specs are updated accordingly.

## Constructing the Loop Body

Now we attempt to construct the loop body.
To have a loop that terminates we have to decrease the bound, and there are various ways to do so. One may decrement `b` by `b := b - 1`, or one may divide `b` by half --- knowing that `b` is not zero.
Let us try the second way. Type this into the spec and press `ctrl-c-r`:
```
?
b := b / 2
```
That gives you a new spec with updated postcondition:
![](../images/tutorial/tutorial04.jpg)

We now have to come up with some code that brings the computer from a state satisfying
```
(a * b) + r = A * B ∧ b ≥ 0 ∧ b ≠ 0
```
to a state satisfying
```
(a * (b / 2)) + r = A * B ∧ b / 2 ≥ 0
```
One possibility is `b := b * 2`. However, if we fill in `b := b * 2` to the spec:

![](../images/tutorial/tutorial05.jpg)

Among the proof obligations we have to prove:
```
(a * b) + r = A * B ∧ b ≥ 0 ∧ b = ?bnd_51 ∧ b ≠ 0 ⇒ (b * 2) / 2 < ?bnd_51
```
where `?bnd_51` is a system generated variable.
It looks complex but the relevant parts are:
```
.... b = ?bnd_51 ... ⇒ (b * 2) / 2 < ?bnd_15
```
which is not possible to prove.
Therefore `b := b * 2` is a bad idea.

Another possibility is `a := a * 2`. For that we have to prove an proof obligation, which simplifies to
```
(a * b) + r = A * B ....  ⇒
((a * 2) * (b / 2)) + r = A * B ....
```
This time we demonstrate a manual proof.
Each proof obligation comes with a hash key.
Press the hash key for the first proof obligation, a comment block having the key is added to the program file,
where you are supposed to fill in a proof of that property.
Let us try to do the proof:

![](../images/tutorial/tutorial06.jpg)

It turns out that the property is true *if `b` is an even number*.
This is a hint that we shall wrap `a := a * 2; b := b / 2` in a guard `even b`,
to ensure that `b` is even, and put it in an `if` construct.


The current code is now:
```
con A, B : Int { A ≥ 0 ∧ B ≥ 0 }
con even, odd : Int -> Bool
var r, a, b : Int

a, b, r := A, B, 0
{ a * b + r = A * B ∧ b ≥ 0 , bnd: b }
do b ≠ 0 ->
    if even b ->
        a := a * 2
        b := b / 2
    fi
od
{ r = A * B }
```

A program is proven correct if all proof obligations are proved.
Hash key of a proof obligation with a proof block is displayed in blue
(see the top-right corner).
Currently the system makes no attempt to check these proofs, however.
They are just comments for the user.

## Totalising IF

We are not done yet.
Among all the proof obligations we will be asked to prove that `if` is total --- every possible case is covered.
Therefore we need to think about what to do in the `odd b` case.
For this case we might decrease `b` by `b := b - 1`.
By a similar process we can construct what to do with `a` and `r` in this case to maintain the invariant.
A possible final program would be:
```
con A, B : Int { A ≥ 0 ∧ B ≥ 0 }
con even, odd : Int -> Bool
var r, a, b : Int

a, b, r := A, B, 0
{ a * b + r = A * B ∧ b ≥ 0 , bnd: b }
do b ≠ 0 ->
    if even b -> a := a * 2
                 b := b / 2
     | odd b ->  r := a + r
                 b := b - 1
    fi
od
{ r = A * B }
```
which computes `A * B` using `O(log B)` atomic arithmetic operations.

But that is not the only possible program.
One might also decide to do nothing in the `odd b` case
and always decrease `b` regardless of its parity, resulting in
```
con A, B : Int { A ≥ 0 ∧ B ≥ 0 }
con even, odd : Int -> Bool
var r, a, b : Int

a, b, r := A, B, 0
{ a * b + r = A * B ∧ b ≥ 0 , bnd: b }
do b ≠ 0 ->
    r := a + r
    b := b - 1
    if even b -> a := a * 2
                 b := b / 2
     | odd b ->  skip
    fi
od
{ r = A * B }
```
The program is correct as long as one can prove all the proof obligations.

## Summary

In this example we saw how a program and its proof are developed together.
The program certainly determines what needs to be proved.
Meanwhile, what needs to be proved also give hints on what the program could be.
Code can be motivated by the need to decrease a bound, or to prove a property.
Failure of proving a property may indicate that we need to strengthen an assumption, that a previous choice of certain code is wrong, or that the code needs to be put in a guarded command and there are other case to be covered.

It is this style of program development Guabao is designed to support .

## Key Combinations

* `ctrl-c-r`: when the cursor is in a hole, pressing `ctrl-c-r` fills in the hole.
* `ctrl-x-r`: restarts the Guabao backend.
* A question mark `?` generates a new hole.
* The language uses unicode characters in various occasions. Press `\` to input unicode characters used in this language. A list of key combinations to for inputting unicode characters can be found [here](3-keyscombs.html).


## The Programming Language

For more information about the programming language, see [Language Overview](1-gcl-overview.html).
