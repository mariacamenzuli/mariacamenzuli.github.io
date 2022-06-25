---
layout: post
title: Applying Visual GUI Testing to Computer Game Testing (MSc research proposal)
author: Maria Camenzuli
---

Although test automation is a de-facto standard in general software engineering, the games industry still relies on manual testing as the primary means of quality assurance. One reason for this seems to be the fact that existing testing tools are not robust enough to gracefully cope with software as dynamic as a computer game. Automated tests written using such tools are fragile, breaking easily when game designers inevitably make tweaks to the game parameters. For my masters project, I have proposed to investigate whether visual GUI testing, a technique that uses computer vision to locate GUI components on screen, can be used to write automated tests for games that are robust enough to handle changes in the layout of a GUI, and the presence of other animations.

When developers want to setup an automated process to interact with the GUI of a computer game, they do not have the luxury of tools available to developers of other types of software, such as Selenium for web-pages, which reliably locate objects of interest on the GUI for the scripted process to interact with. They may therefore have to resort to the primitive approach of specifying absolute screen-space positions for GUI elements inside their scripts. Needless to say, this results in very fragile scripts that are almost sure to be broken.

Visual GUI testing is a relatively new and emergent GUI testing technique that may be able to help address this problem. Using an image of what a GUI component, such as a button, is expected to look like, a test engineer can specify that the test script should locate the section of the screen that looks like the specified component. Since this technique uses computer vision to locate GUI components on frames or images of the rendered GUI, it is not vulnerable to changes in GUI layout or structural code changes.

While existing research has provided support for the applicability of visual GUI testing in practice for general software testing, I was not able to find any existing research into the applicability of the technique for the testing of computer games. The anticipated result of this project is that visual GUI testing will be shown to be a viable technique for creating robust automated tests for games. Given that the games industry is lagging behind the rest of the software engineering world when it comes to adoption of automation, I hope that this study can help the games industry make tangible progress towards finally being able to reap the well known benefits of automation.

Read the full research proposal [here]({{ site.url }}/assets/documents/applying-visual-gui-testing-to-computer-game-testing-proposal.pdf).
