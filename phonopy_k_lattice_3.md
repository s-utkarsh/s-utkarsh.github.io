---
layout: page
title: Gr&uuml;neisen Parameter, Group velocity and Phonon-lifetime
---
Although the phonon bands and DOS would give you some explanation concerning the low or high value of thermal conductivity, the mode Gr&uuml;neisen parameter is as close as we get to a qualitative parameter justifying the trend in a closed set of materials. Phonon agroup velocity and Phonon-lifetime are also very important quantities which help us discern how phonons will behave at certain frequencies. Obviously, here we are only concerned with the acoustic range where low phonon lifetime would indicate higher scattering rates and thus higher group velocity. Let's see how we can generate the data for all of this.

I. Mode Gr&uuml;neisen parameter:

- Phonopy lets us calculate mode grueneisen parameter as a function of frequency under the harmonic approximation as well as as a function of temperature under the quasi-harmonic approximation. I feel that the variation with frequency analysed along with number of phonon modes and phonon bands provides more insight into the vibrational dynamics of the system.

- Thermal conductivity (&kappa;) varies inversely with the square of Mode Gr&uuml;neisen Parameter (&gamma;)

  Make sure to use the same type of unit cell (conventional/primitive) as used to calculate thermal conductivity.
First, we need to completely relax the structure with say EDIFF=10E-08 (the precision is important) then make changes to the unit cell volume (while maintaining the cell shape). Make one structure with a slightly larger volume and another with equally smaller volume. Put all of these structures in 3 different folders at the same location and rename them to POSCAR-unitcell (name the folders equi, plus and minus respectively) After that, in each folder run:

  ```
  phonopy -d --dim="2 2 2" -c POSCAR-unitcell
  ```
  *use --dim="4 4 4" if your unit cell is primitive.*

  After that, in each folder:
  ```
  cp SPOSCAR POSCAR
  ```
  Using the following INCAR, we do a single step calculation using VASP to measure linear response of the system when 3 ions are displaced in 3 mutually perpendicular directions.
  ```
  SYSTEM=Your System name
  IBRION=8
  IALGO=38
  LWAVE=.FALSE.
  LCHARG=.FALSE.
  LREAL=.FALSE.
  PREC=Accurate
  EDIFF=1E-8     #make sure this is the same as used for relaxation of unit cell
  EDIFFG=1E-8
  ISMEAR=0
  SIGMA=0.1
  ADDGRID=.TRUE.
  ```
  The vasp calculation is simple done by using the following command:
  ```
  mpirun -np 8 vasp_std > out
  ````
  After the calculation is complete, run (outside all 3 directories)
  ```
  phonopy-gruneisen equi plus minus --dim="2 2 2" --mesh="20 20 20" -p -c POSCAR-unitcell --color="RB"
  ```
  A file named gruneisen.pdf is formed and it would contain  plot that looks like:
  
  <img src="/pcr/ph3.svg" alt="bands" width="900" height="400">
  
  However sometimes, at a negative frequency value (slightly negative, although always less than 50cm-1) we might get an extreemely high or low value of gruneisen parameter which is not relevant and we don't want that in the plot as well. 
  This case might look something like this:
  
  <img src="/pcr/ph4.svg" alt="bands" width="900" height="400">
  
  In that case, we need to extract the data from another file that is generated 'grunieisen.yaml' which can be done using the following steps:
  ```
  grep "gruneisen:" gruneisen.yaml>gru.dat
  grep "frequency:" gruneisen.yaml>freq.dat
  paste gru.dat freq.dat>freqVSgru.dat
  ```
  Then simply plot column 1 (gruneisen parameter) VS column 4 (frequency).
  
  For both group velocity and phonon lifetime date, we need the results obtained for thermal conductivity using phono3py as discussed in Phonons with Phonopy #1
  
II. Group velocity:

- Interchangably used as Scattering rate (=group velocity/boundary mean free path), group velocity is defined as the variation of phonon eigenfrequency within the first brillouin zone.
If plotted against frequency or mean free path, group velocity provides great insight into contribution of phonons in different frequency intervals to thermal conductivity.
Group velocity can be extracted from the outfile we created with the last step to calculate lattice thermal conductivity [Find it here.. ](/k_lattice.md)
The plotted data would look something like this:

<img src="/pcr/ph5.svg" alt="gv" width="400" height="400">

Doesn't look too good does it?

The above plot doesn't make too much sense since there are too many scattered points. To get some meaning out of this, we overlay the scatter plot with the weighted (over no. of phonon modes at frequency f) average of group velocity at each frequency.

This would look like:

<img src="/pcr/ph6.svg" alt="gv" width="400" height="400">


III. Phonon lifetime:

Another important physical quantity is the time interval between 2 successive phonon collisions (the lifetime). Just like phonon group velocity, lifetime in specific frequency intervals (acoustic or optical) would indicate whether the phonons in that range scatter more OR less. Ofcourse, a smaller lifetime indicates larger scattering and if it lies in acoustic range, possibly larger thermal conductivity.

Phono3py offers a tool for calculating phonon lifetime obtained from the imaginary part of phonon self-interaction energy. 
phono3py-kdeplot is part of the package and can be used after you've calculated lattice thermal conductivity.
The sequence is made clear on the official phonopy webpage [here](https://atztogo.github.io/phono3py/auxiliary-tools.html#phono3py-kdeplot)

