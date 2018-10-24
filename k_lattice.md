---
layout: page
title: Lattice thermal conductivity using Phono3py
---

1. Stuff to keep in mind while analyzing results (Is this really what you want?)
-The single mode relaxation time approximation (RTA): This is what we're using here
-Linearized phonon Boltzmann equation: This is what we're solving here
-The phonon-boundary scattering model: This indicates the scope of this calculation
-The cubic anharmonic force constant (3rd order): The absolute thermal conductivity, calssically can be expressed as a taylor series expansion
- Fermi’s golden rule > Im (self energy) = G : The scope of this calculation
- Phonon lifetime t = 1/2G (To double check your phonon-lifetime results)
- Here we get simplified phonon liftime using Single mode relaxation time (SMRT) approximation

2. Your fundamental input file (Don't be a sheep, make appropriate changes) 

PREC = Accurate

IBRION = -1  #not moving ions at all, but calculating forces

ISTART=1 #we're using previously generated WAVECAR to reduce computational time 

ENCUT = 500  

EDIFF = 1.0e-08 #We definitely need good precision

ISMEAR = 0; 

SIGMA = 0.01 #choose whatever suits your system

IALGO = 38

LREAL = .FALSE. #important

LWAVE = .FALSE.

LCHARG = .FALSE.

ADDGRID=.TRUE. #The single most important parameter, which ensures that the forces are accurate
