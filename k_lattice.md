---
layout: page
title: Lattice thermal conductivity using Phono3py
---
Currently I'm working with some Alkali metal based Thermoelectric materials, where the lattice thermal conductivity has not been
reported. There being no definitive guide to calculating <b>&kappa;<sub>L</sub></b> using Phono3py, here I list out some common       pitfalls and summarize the way to carry out calculations. Here are the steps to follow (Always read, make notes, reread and improve notes)

1. Stuff to keep in mind while analyzing results (Is this really what you want?):

- The single mode relaxation time approximation (RTA): This is what we're using here
- Linearized phonon Boltzmann equation: This is what we're solving here
- The phonon-boundary scattering model: This indicates the scope of this calculation
- The cubic anharmonic force constant (3rd order): The absolute thermal conductivity, calssically can be expressed as a taylor series expansion
- Fermi’s golden rule > Im (self energy) = G : The scope of this calculation
- Phonon lifetime t = 1/2G (To double check your phonon-lifetime results)

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

4. Running phono3py to generate displacements:
- phono3py -d --dim="2 2 2" -c POSCAR-unitcell #--dim
- for isotropic lattices, we can also use phono3py --dim="2 2 2" <b>--sym-fc</b> -c POSCAR-unitcell
- A large number of POSCAR files (titled POSCAR-XXXXX) would be generated for which we would have to do force calculations using the INCAR provided above.

5. Here comes the tricky part, <b> How to do all these calculations ? </b>:
- Don't worry, we're basically doing single SCF cycles for force calculation.
- We're using WAVECAR to speed up our calculations, doing this makes VASP guess a good starting value of Initial energy.
- <b>Automate the process !!</b>

6. The automation: obviously we use a script to do this, so here we go:
- First way: in 'C',make a file called fc3run and in that copy the following:<br>
```   #! /bin/csh -f <br>
    # <br>
    set max = `sed -n -e 's/num_displacements_created: //1p' disp_fc3.yaml` <br>
    set No = 1 <br>
    echo $No, $max <br>
    while ( $No <= $max ) <br>
        echo "Here we go......" <br>
        echo $No"/"$max <br>
        date <br>
        if ( $No < 10) then <br>
            mkdir disp-0000$No<br>
            mv POSCAR-0000$No ./disp-0000$No/POSCAR<br>
            cp POTCAR ./disp-0000$No/POTCAR<br>
            cp INCAR ./disp-0000$No/INCAR<br>
            cd disp-0000$No<br>
            endif<br>
            if ( 10 <= $No && $No < 100) then<br>
            mkdir disp-000$No<br>
            mv POSCAR-000$No ./disp-000$No/POSCAR<br>
            cp POTCAR ./disp-000$No/POTCAR<br>
            cp INCAR ./disp-000$No/INCAR<br>
            cd disp-000$No<br>
            endif<br>
            if ( 100 <= $No && $No < 1000) then<br>
            mkdir disp-00$No<br>
            mv POSCAR-00$No ./disp-00$No/POSCAR<br>
            cp POTCAR ./disp-00$No/POTCAR<br>
            cp INCAR ./disp-00$No/INCAR<br>
            cd disp-00$No<br>
        endif<br>
        if ( 1000 <= $No && $No < 10000) then<br>
            mkdir disp-0$No<br>
            mv POSCAR-0$No ./disp-0$No/POSCAR<br>
            cp POTCAR ./disp-0$No/POTCAR<br>
            cp INCAR ./disp-0$No/INCAR<br>
            cd disp-0$No<br>
        endif<br>
        if ( 10000 <= $No && $No < 100000) then<br>
            mkdir disp-$No<br>
            mv POSCAR-$No ./disp-$No/POSCAR<br>
            cp POTCAR ./disp-$No/POTCAR<br>
            cp INCAR ./disp-$No/INCAR<br>
            cd disp-$No<br>
        endif<br>
        mpirun -np <b>no. of processors  vasp_executable</b><br>
        cd ..<br>
        @ No = $No + 1<br>
    end<br>``` 
