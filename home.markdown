---
title: Guabao
---

![<small>Photo: [Ron Dollete](https://www.flickr.com/photos/saucesupreme/4434798368). CC BY-ND 2.0.</small>](images/guabao-saucesupreme.jpg)

Guabao is a programming environment encouraging the methodology that **a program and its correctness proof should be developed hand in hand**,
and that **how a program should be proved could sometimes give hints how the program can be written**.

Guabao uses a variation of Edsger W. Dijkstra's [Guarded Command Language](https://en.wikipedia.org/wiki/Guarded_Command_Language) --- see the [language overview](pages/1-gcl-overview.html).
However, Guabao is also an environment built into an editor.
Shown below is a screen shot:

![](images/tutorial/tutorial03.jpg)

As the program is being written in the left pane, Guabao instantly parses the program, infers, and displays in the right pane verification conditions yet to be proved to show that the program meets the postcondition.

Code yet-to-be-written can be left as holes (specifications with pre and post consditions) to be filled in later, for example the hole between line 8 and 10. Attempts to prove verification conditions may give clues how the holes can be filled or, in case of failure, indicate that we need a stronger precondition or loop invariant.

See the [tutorial](pages/0-tutorial.html) for more explanations.

Guabao currently does not prove anything for the users. It is possible, in the future, to employ an external theorem prover to do the job.

**The Name**. GUA in Guabao comes from GUArded command language.
[Guabao (刈包)](https://en.wikipedia.org/wiki/Gua_bao) is a street food popular in places including Taiwan, where Guabao the software was designed.

# Download and Install

The frontend of Guabao is an extension of [Visual Code Studio](https://code.visualstudio.com/). You can install it by searching for the extension "Guabao" in the editor, or through the [Extensions Marketplace](https://marketplace.visualstudio.com/items?itemName=scmlab.guabao).
A simple one-click installation downloads the frontend as well as pre-compiled backend (implemented in Haskell).

# Where to Start?

More information can be found in the [tutorial](pages/0-tutorial.html), the [Language Overview](pages/1-gcl-overview.html), and the [Examples](pages/2-examples.html) page.

# More Info.

Guabao is implemented and maintained by [Shin-Cheng Mu](https://scm.iis.sinica.edu.tw/home/), Ting-Yan Lai, and Thing-Han Lim in [Institute of Information Science](https://www.iis.sinica.edu.tw/), Academia Sinica, Taiwan.

The backend of Guabao is implemented using [Haskell](https://www.haskell.org/), while the frontend is implemented using [Reason](https://reasonml.github.io/) and compiled to Javascript to run as a VS Code extension.
Source code for the [frontend](https://github.com/scmlab/gcl-vscode) and [backend](https://github.com/scmlab/gcl) are stored on GitHub.

Guabao is still under development --- everything may change or break tomorrow! We welcome bug reports, feature requests, and other suggestions.
Feel free to submit them as issues through the GitHub repositories above, or [contact](pages/3-contacts.html) the authors.
