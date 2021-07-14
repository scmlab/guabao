---
title: Guabao
---

![<small>Photo: [Ron Dollete](https://www.flickr.com/photos/saucesupreme/4434798368). CC BY-ND 2.0.</small>](images/guabao-saucesupreme.jpg)

Guabao is a programming environment encouraging the methodology that a program and its correctness proof shuld be developing hand in hand,
and that how a program should be proved could sometimes give hints how the program can be written.

Guabao uses a variation of Edsger W. Dijkstra's [Guarded Command Language](https://en.wikipedia.org/wiki/Guarded_Command_Language) --- see the [language reference](pages/Language%20Reference.html).
However, Guabao is also an environment built into an editor.
Shown below is a screen shot:

![](images/screenshot-v0.2.3.jpg)

As the program is being written in the left pane, Guabao instantly parses the program, infers, and displays in the right pane verification conditions yet to be proved to show that the program meets the postcondition.

Code yet-to-be-written can be left as holes (specifications with pre and post consditions) to be filled in later, for example the hole between line 13 and 15. Attempts to prove verification conditions may give clues how the holes can be filled or, in case of failure, indicate that we need a stronger precondition or loop invariant.

For more explanation of this particular example see the [Examples](pages/Examples.html) page.

Guabao currently does not prove anything for the users. It is possible, in the future, to employ an external theorem prover to do the job.

**The Name**. GUA in Guabao comes from GUArded command language.
[Guabao (刈包)](https://en.wikipedia.org/wiki/Gua_bao) is a street food popular in places including Taiwan, where Guabao the software was designed.

# Download and Install

Guabao is implemented as an extension of [Visual Code Studio](https://code.visualstudio.com/). You can install it by searching for the extension "Guabao" in the editor, or through the [Extensions Marketplace](https://marketplace.visualstudio.com/items?itemName=scmlab.guabao).
A simple one-click installation downloads the frontend as well as pre-compiled backend.

# Getting Started

More information can be found in the Tutorial, the [Language Reference](pages/Language%20Reference.html), and the [Examples](pages/Examples.html) page.
