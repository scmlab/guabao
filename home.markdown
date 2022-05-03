---
title: Guabao
---

![<small>Photo: [Ron Dollete](https://www.flickr.com/photos/saucesupreme/4434798368). CC BY-ND 2.0.</small>](images/guabao-saucesupreme.jpg)

Guabao is a programming environment encouraging the methodology that **a program and its correctness proof should be developed hand in hand**,
and that **how a program should be proved could sometimes give hints how the program can be written**.

Guabao uses a variation of Edsger W. Dijkstra's [Guarded Command Language](https://en.wikipedia.org/wiki/Guarded_Command_Language) --- see the [language overview](pages/1-gcl-overview.html).
However, Guabao is also an environment built into an editor.
Shown below is a screen shot:

![](images/tutorial/tutorial04.jpg)

As the program is being written in the left pane, Guabao instantly parses the program, infers, and displays in the right pane proof obligations yet to be proved to show that the program meets the postcondition.

Code yet-to-be-written can be left as holes (specifications with pre and post consditions) to be filled in later, for example the hole between line 8 -- 10. Attempts to prove the proof obligations may give clues how the holes can be filled or, in case of failure, indicate that we need a stronger precondition or loop invariant.

See the [tutorial](pages/0-tutorial.html) for more explanations.

**The Name**. GUA in Guabao comes from GUArded command language.
[Guabao (刈包)](https://en.wikipedia.org/wiki/Gua_bao) is a street food popular in places including Taiwan, where Guabao the software was designed.

# What Guabao is Not

It might also help to clarify what Guabao is not:

* Gaubao is not an implementation of Guarded Command Language in which one can write a program and test it, but an environment where a program and its proof can be designed together. [^1]
* While Guabao Guabao does employ an SMT solver (currently [Z3](https://github.com/Z3Prover/z3)) to discharge simple proof obligations, it does not intend to prove *everything* for the user. Instead, it computes the proof obligations needed to guarantee correctness, and let them guide the development of the program.
  * We believe that proving properties that are related to the algorithmic aspect of a program and, in case of failure, learning from the proof how the program should evolve to allow the proof to go through, is a part of the program development process (see the [tutorial](pages/0-tutorial.html)), which should be carried out by the user.
* Currently Guabao does not check user-written proofs. To do so we need to develop a representation of equational proofs, which is one of our future works.

[^1]: In his article ["On the cruelty of really teaching computing science" (EWD1036)](https://www.cs.utexas.edu/~EWD/transcriptions/EWD10xx/EWD1036.html), Dijkstra wrote that he would design a programming language for teaching (which we believe would be GCL) and ``see to it that [it] has not been implemented on campus so that students are protected from the temptation to test their programs.''

# Download and Install

The frontend of Guabao is an extension of [Visual Code Studio](https://code.visualstudio.com/). You can install it by searching for the extension "Guabao" in the editor, or through the [Extensions Marketplace](https://marketplace.visualstudio.com/items?itemName=scmlab.guabao).
A simple one-click installation downloads the frontend as well as pre-compiled backend (implemented in Haskell).

[Z3](https://github.com/Z3Prover/z3) has to be installed separately and locatable from `$PATH`. You do not need Z3 to run Guabao if you do not use the SMT-solving feature.

# Where to Start?

More information can be found in the [tutorial](pages/0-tutorial.html), the [Language Overview](pages/1-gcl-overview.html), and the [Examples](pages/2-examples.html) page.

# Development Team

[Shin-Cheng Mu](https://scm.iis.sinica.edu.tw/home/), Ting-Yan Lai, Thing-Han Lim, Chien-Yuan Su ([Institute of Information Science](https://www.iis.sinica.edu.tw/), Academia Sinica, Taiwan.), and Hsien-En Tzeng (National Taiwan University).

# More Info.

The backend of Guabao is implemented using [Haskell](https://www.haskell.org/), while the frontend is implemented using [Reason](https://reasonml.github.io/) and compiled to Javascript to run as a VS Code extension.
Source code of Guabao is stored on GitHub (frontend: [https://github.com/scmlab/gcl-vscode](https://github.com/scmlab/gcl-vscode), backend: [https://github.com/scmlab/gcl](https://github.com/scmlab/gcl)).

Guabao is still under development --- everything may change or break tomorrow! We welcome bug reports, feature requests, and other suggestions.
Feel free to submit them as issues through the GitHub repositories above, or [contact](pages/3-contacts.html) the authors.
