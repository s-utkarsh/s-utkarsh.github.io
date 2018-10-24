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

    PREC = Accurate <br>
    IBRION = -1  #not moving ions at all, but calculating forces  <br>
    ISTART=1 #we're using previously generated WAVECAR to reduce computational time  <br>
    ENCUT = 500   <br>
    EDIFF = 1.0e-08 #We definitely need good precision <br>
    ISMEAR = 0 <br>
    SIGMA = 0.01 #choose whatever suits your system <br>
    IALGO = 38 <br>
    LREAL = .FALSE. #important <br>
    LWAVE = .FALSE. <br>
    LCHARG = .FALSE <br>
    ADDGRID=.TRUE. #The single most important parameter, which ensures that the forces are accurate <br>

3. Preparing <b>POSCAR-unitcell</b> and <b>WAVECAR</b> (we need it for phono3py)

- First, take your conventional lattice cell and make a 2x2x2 (in some cases when you're not dealing with cubic cells, we scale up the cells approprately; For example, in case of a hexagonal cell, if you only wish to calculate kappa in x and y directions, a 2x2x1 supercell would also work)
- Relax the volume of this supercell with highly accurate convergence criterion and also set it to write WAVECAR file. This is done by the following changes in the incar file:
    - LWAVE=.TRUE.
    - EDIFF=1E-09 # remember that EDIFFG=10 times EDIFF
- Divide the supercell lattice parameters by 2 and then use as the lattice parameters of the conventional lattice cell. Rename POSCAR (the conventional lattice file) to POSCAR-unitcell
- DONE
<br>
4. Running phono3py to generate displacements:
