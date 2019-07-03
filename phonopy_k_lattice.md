---
layout: page
title: Phonons with Phonopy #1 : Bandstructure & Partial Phonon-DOS
---
While investigating the vibrational dynamics of your system, a number of physical quantities and relations between physical quantities help us evaluate the dynamic properties of the system. Continuing the previous thread, we'll look at properties which support our result for lattice thermal conductivity (<b>&kappa;<sub>L</sub></b>) whether it is very low or very high.

I. Phonon bandstructure:

- We'll calculate the phonon bandstructure using phonopy and VASP via. the DFPT method where the linear response (force) corresponding to the displacement of one ion in 3 mutually perpendicular directions each is recorded and used to calculate the dispersion of frequency with k-points in reciprocal space for the cell.
- As consistent with the theme for phono3py, we shall do this calculation for a conventional cell using the following set of commands:
    ```
    phonopy -d --dim="2 2 2" -c POSCAR-unitcell
    ```
    We should make sure that POSCAR-unitcell is completely relaxed. A few files including POSCAR-XXXX and SPOSCAR are generated. For DFPT, the SPOSCAR file is relevant and for FDM (Finite dsplacement method), we utilize the POSCAR-XXXX files.
    ```
    cp SPOSCAR POSCAR
    ```
    The INCAR file is prepared as follows:(<b> make appropriate changes, don't be a sheep </b>)
    ```
    SYSTEM=Your System name
    IBRION=8
    IALGO=38
    LWAVE=.FALSE.
    LCHARG=.FALSE.
    LREAL=.FALSE.
    PREC=Accurate
    EDIFF=1E-7     #make sure this is the same as used for relaxation of unit cell
    EDIFFG=1E-8
    ISMEAR=0
    SIGMA=0.1
    ADDGRID=.TRUE.
    ```
    We should make sure that EDIFF and EDIFFG are the same as used for relaxing POSCAR-unitcell in order to not have extra forces spoiling our party!! 
    The next step, obviously is to run the vasp calculation:
    ```
    mpirun -np 16 vasp_std > out &
    ```
    vasprun.xml is one of the files generated when the DFPT calculation with VASP is completed. Make sure that the vasprun file contains hessian elements (i.e. the forces, a quick ?force in the vasprun.xml file opened with vim should do the trick) and create force constants by:
    ```
    phonopy --fc vasprun.xml
    ```
    You will notice that a new file called FORCE_CONSTANTS is generated.
    After that, a band.conf file is to be created which phonopy will read and provide you with the phonon dispersion data.
    A sample system is Fe2VAl, a well known Heusler alloy for which a band.conf file would look like this.
    ```
    ATOM_NAME = V Fe Al
    FORCE_CONSTANTS = READ
    BAND = 0.0 0.0 0.0  0.5 0.0 0.5  0.5 0.25 0.75  0.375 0.375 0.75  0.0 0.0 0.0  0.5 0.5 0.5  0.625 0.25 0.625  0.5 0.25  0.75  0.5 0.5 0.5  0.375 0.375 0.75  0.625 0.25 0.625  0.5 0.0 0.5
    BAND_POINTS = 101
    BAND_LABELS = $\Gamma$ X W K $\Gamma$ L U W L K U X
    BAND_CONNECTION = .TRUE.
    MESH = 21 21 21
    GAMMA_CENTER =.TRUE.
    ```
    After this step, phonopy is instructed to create dispersion data following the path and other instructions provided in the band.conf file.
    ```
    phonopy --dim="2 2 2" -c POSCAR-unitcell band.conf
    ```
    You'll notice that band.yaml is created which contains the dispersion data but in order to plot it we need to use another tool in the phonopy package that is phonopy-bandplot
    ```
    phonopy-bandplot --gnuplot band.yaml >out_band &
    ```
    This should put all the plottable data in the file out_band. 
    After plotting it looks like.
    
    <img src="/pcr/ph1.svg" alt="bands" width="900" height="400">
    
II. Atom projected phonon density of states:

While the bandstructure does give a lot of information about the vibrational dynamics of the system, in order to find out what atom contributes the most to the increase/decrease in rattling, one must look at the atomic contributions to the number of phonon modes at relevant frequencies. Projected Phonon-DOS helps us here. Let's have a look at how one can do this using phonopy.

We start from midway in the last section about phonon bandstrcuture where we had created the FORCE_CONSTANTS. Instead of a band.conf, we create another post processing configuration file called pdos.conf which would look like:
    ```
    ```

