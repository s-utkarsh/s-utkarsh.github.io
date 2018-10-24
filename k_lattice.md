---
layout: page
title: Lattice thermal conductivity using Phono3py
---

1. Stuff to keep in mind while analyzing results (Is this really what you want?):

- The single mode relaxation time approximation (RTA): This is what we're using here
- Linearized phonon Boltzmann equation: This is what we're solving here
- The phonon-boundary scattering model: This indicates the scope of this calculation
- The cubic anharmonic force constant (3rd order): The absolute thermal conductivity, calssically can be expressed as a taylor series expansion
- Fermi’s golden rule > Im (self energy) = G : The scope of this calculation
- Phonon lifetime t = 1/2G (To double check your phonon-lifetime results)
- Here we get simplified phonon liftime using Single mode relaxation time (SMRT) approximation

2. Your fundamental input file (<b>Don't be a sheep, make appropriate changes</b>) 

    PREC = Accurate \s\s
    IBRION = -1  #not moving ions at all, but calculating forces \s\s
    ISTART=1 #we're using previously generated WAVECAR to reduce computational time \s\s
    ENCUT = 500  \s\s
    EDIFF = 1.0e-08 #We definitely need good precision\s\s
    ISMEAR = 0\s\s
    SIGMA = 0.01 #choose whatever suits your system\s\s
    IALGO = 38\s\s
    LREAL = .FALSE. #important\s\s
    LWAVE = .FALSE.\s\s
    LCHARG = .FALSE\s\s
    ADDGRID=.TRUE. #The single most important parameter, which ensures that the forces are accurate\s\s
