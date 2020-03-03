---
layout: page
title: So I wrote a useful code! [*convex (v1.0.1)*](https://github.com/s-utkarsh/convex/releases)
---

[![DOI](https://zenodo.org/badge/244065279.svg)](https://zenodo.org/badge/latestdoi/244065279)

In September of last year (2019), I was working on finishing up a paper (my first as a First author). During that time, some senior collegues in the same lab, who had submitted a paper on Pressure-driven phase transition in thermoelectric materials recieved a query from the referees about the stability of the transformed phase which would (as predicted by *first-principles calculations* at the level of DFT) exist at the a pressure higher or lower than ambient pressure (see Negative Pressure).

Broadly speaking, the stability of an alloy could be classified into 3 types: 

1. Static stability (usually indicated by the DFT energy/enthalpy obtained)

2. Dynmaic stability (indicated by the absence of imaginary frequencies in phonon-spectrum)

3. Thermodynamic stability (here we talk in terms of formation enthalpy, rather than enthalpy)

In this specific case, while the first 2 criterion were satisfied, the thermodynamic stability remained a question. Here I was able to contribute through the learning experience I had in my pre-final year internship with the group of [Prof. Stefano Sanvito](http://www.spincomp.com/group/stefano-sanvito/) where I had learned a lot about the convex-hull approach to assess thermodynamic stability. There being no reliable code I could find and generating a 3-dimensional convex hull being the need, I ended up writing this code in the most naive form. 

In addition, I also performed a large number (~350) of DFT calculations within a short period of time (11 days! and an absurd amount of computational time :P) which covered all the possible phases of each possible decomposition. This was done for the 3 alloys which were in focus. Here, a database such as [AFLOW](http://aflow.org) comes in handy. However, one still has to do some more groundwork covering all the experimental work that has been published on the parent elements which might include high pressure and metastable phases which might become relevant at the conditions relevant to the work at hand.

Thankfully, I was also listed as a co-author since I also contributed with the bonding analysis in addition to above. :) Here's the published [paper](https://iopscience.iop.org/article/10.1088/1361-648X/ab4e71/meta)

## Convex-hull and Thermodynamic stability ##

Here's a very useful paper which explains both concepts in a lucid manner.

[Corey Oses *et al.* AFLOW-CHULL: Cloud-Oriented Platform for Autonomous Phase Stability Analysis *J. Chem. Inf. Model. 2018, 58, 12, 2477-2490*](https://pubs.acs.org/doi/10.1021/acs.jcim.8b00393)

## *convex* (v1.0.1) ##

The code works both interactively and with pre-made inputs for user comfort.

A big-problem is the representation of results. As the reader is aware, it's difficult to get 3-d plots in a publishable form and hence one, almost always has to go to a countour plot.

This package will also make a highly customizable plot for the user via plotly!! 

For the uninitiated, plotly is a powerful python library (also in R, json) which produces visually pleasing and highly customizable plots. On top of that, it also has a web-ui which makes the process of customization user-friendly and easy.

This package will also generate a 2-d contour plot of the 3-d hull (with formation enthalpy as the contour alpha) as a html file which can be opened with any internet-enabled browser and contains an "Export to plotly" hyperlink.

## Citation ##
As I'm a young researcher just starting out, a citation, although not required will be hugely appreciated.

Please cite as: 

Utkarsh Singh; "*convex*: A user friendly package to create phase diagrams via convex hull approach" [10.5281/zenodo.3693036](https://doi.org/10.5281/zenodo.3693036)

## Acknowledgement ##

I'm hugely thankful to [Dr. Hem C. Kandpal](https://www.iitr.ac.in/departments/CY/pages/People+Faculty+hem12fcy.html) and [Dr. Prof. Stefano Sanvito](http://www.spincomp.com/group/stefano-sanvito/) for their contribution to the development of this. 

Although this is a very small project, it is the first time I've published a code I've written and for that I'm very thankful.
