---
layout: page
title: Lattice thermal conductivity using Phono3py and VASP
---
Currently I'm working with some Alkali metal based Thermoelectric materials, where the lattice thermal conductivity has not been
reported. There being no definitive guide to calculating <b>&kappa;<sub>L</sub></b> using Phono3py, here I list out some common       pitfalls and summarize the way to carry out calculations. Here are the steps to follow (Always read, make notes, re-read and improve notes)

1. Stuff to keep in mind while analyzing results (Is this really what you want?):

- The single mode relaxation time approximation (RTA): This is what we're using here
- Linearized phonon Boltzmann equation: This is what we're solving here
- The phonon-boundary scattering model: This indicates the scope of this calculation
- The cubic anharmonic force constant (3rd order): The absolute thermal conductivity, calssically can be expressed as a taylor series expansion
- Fermi’s golden rule > Im (self energy) = G : The scope of this calculation
- Phonon lifetime t = 1/2G (To double check your phonon-lifetime results)<br>

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
- DONE<br>

4. Running phono3py to generate displacements:

- phono3py -d --dim="2 2 2" -c POSCAR-unitcell #--dim
- for isotropic lattices, we can also use phono3py --dim="2 2 2" <b>--sym-fc</b> -c POSCAR-unitcell
- A large number of POSCAR files (titled POSCAR-XXXXX) would be generated for which we would have to do force calculations using the INCAR provided above. <br>

5. Here comes the tricky part, <b> How to do all these calculations ? </b>:

- Don't worry, we're basically doing single SCF cycles for force calculation.
- We're using WAVECAR to speed up our calculations, doing this makes VASP guess a good starting value of Initial energy.
- <b>Automate the process !!</b><br>

6. The automation: obviously we use a script to do this, so here we go:

- If you're working on a workstation (single node) make a file called fcrun, copy the following content and then chmod +X fcrun:<br>
``` 
    #! /bin/csh -f 
    # 
    set max = `sed -n -e 's/num_displacements_created: //1p' disp_fc3.yaml` 
    set No = 1 
    echo $No, $max 
    while ( $No <= $max ) 
        echo "Here we go......" 
        echo $No"/"$max 
        date 
        if ( $No < 10) then 
            mkdir disp-0000$No
            mv POSCAR-0000$No ./disp-0000$No/POSCAR
            cp POTCAR ./disp-0000$No/POTCAR
            cp INCAR ./disp-0000$No/INCAR
            cp WAVECAR ./disp-$No/WAVECAR
            cd disp-0000$No
            endif
            if ( 10 <= $No && $No < 100) then
            mkdir disp-000$No
            mv POSCAR-000$No ./disp-000$No/POSCAR
            cp POTCAR ./disp-000$No/POTCAR
            cp INCAR ./disp-000$No/INCAR
            cp WAVECAR ./disp-$No/WAVECAR
            cd disp-000$No
            endif
            if ( 100 <= $No && $No < 1000) then
            mkdir disp-00$No
            mv POSCAR-00$No ./disp-00$No/POSCAR
            cp POTCAR ./disp-00$No/POTCAR
            cp WAVECAR ./disp-$No/WAVECAR
            cp INCAR ./disp-00$No/INCAR
            cd disp-00$No
        endif
        if ( 1000 <= $No && $No < 10000) then
            mkdir disp-0$No
            mv POSCAR-0$No ./disp-0$No/POSCAR
            cp WAVECAR ./disp-$No/WAVECAR
            cp POTCAR ./disp-0$No/POTCAR
            cp INCAR ./disp-0$No/INCAR
            cd disp-0$No
        endif
        if ( 10000 <= $No && $No < 100000) then
            mkdir disp-$No
            mv POSCAR-$No ./disp-$No/POSCAR
            cp POTCAR ./disp-$No/POTCAR
            cp WAVECAR ./disp-$No/WAVECAR
            cp INCAR ./disp-$No/INCAR
            cd disp-$No
        endif
        mpirun -np <b>no. of processors  vasp_executable</b>
        cd ..
        @ No = $No + 1
    end
```
- If you're working on a cluster, you most probably use a bashrc file to submit jobs:
    - In this case, divide the POSCAR-XXXXX in multiple equal parts. Keep this WAVECAR file just outside these directories
    - Append the following to your command for running vasp executable:
    ```
    for a in `seq -w 00001 00001 XXXXX`
    do 
     cp ../WAVECAR .
     cp POSCAR-$a POSCAR
     mpiexec.hydra -np 16 vasp_std> out
     mv vasprun.xml vasprun-$a
    done
    ```
- Of course for the second method, replace XXXXX by whatever number of files are present in that directory.<br>

7. Finding the thermal conductivity, <b> The easy part! </b>  (Well everything is relative isn't it?)

- Step 1: phono3py --cf3 disp-{00001..XXXXX}/vasprun.xml # for first method, collect all at same place for second method
 - Step 2: phono3py --dim="2 2 2" -c POSCAR-unitcell
 - Final step!! - phono3py --fc3 --fc2 --dim="2 2 2" --mesh="21 21 21" -c POSCAR-unitcell --br  --tmin=270 --tmax=1000 --tstep=10
 - <b>Important :</b> Check convergence with --mesh as well and use a value at which <b>&kappa;<sub>L</sub></b> plateaus.<br>
 
8. Visualizing results:

- Use hdfview (on ubuntu)
 - Off diagonal elements: Thermal conductivity is a tensor, so you might get off diagonal elements in some cases due to ENMAX (cutoff) not being large enough. In most cases, it would be neglegible compared to <b>&kappa;<sub>xx</sub></b>, <b>&kappa;<sub>yy</sub></b> and <b>&kappa;<sub>zz</sub></b> values.<br>
 
9. We're DONE. Yaaay!! Treat yourself to a chocolate.
