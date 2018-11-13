---
layout: page
title: Modelling of a Polymerase Chain Reaction (PCR) in COMSOL
---

As part of my course project last semester, I had to investigate the use and functioning of Microreactors in Diagnostic applications. 
One of these uses is in a PCR unit, whose function is to multiply the DNA-sample in the drawn blood n-fold in order to successfully detect
diseases (those which directly affect DNA of the local region).

## How this is a analogous to a Chemical Engineering problem: 

**Scaling up v/s Numbering up**

Typically, when we encounter a problem of increasing an extensive property in our system, the approach is to simply **scale up** the process until a target value of that property is achieved.

However, if that extensive property is the concentration of a species, an excessive amount of raw material would be required, which, more often than not is not possible in case of biological applications.
However, many biological species show interesting properties, such as, the property of replication in DNA, which makes processes which include biological species worth exploring. 

**Flow in the Microreactor**

The flow in microreactor is governed by viscous forces rather than inertial forces, this property along with the manifold increase in the surface to volume ratio in a microreactor compared to a large-scale industrial reactor motivates us to utilize microreactors where we need a high qualitative throughput with the smallest amount of raw material available. So the PCR unit is essentially like a **Plug flow reactor**.

## Model

<img src="model.jpg" alt="Model of a PCR unit" width="600" height="300">

### Overview:

Here, we take a look at the process of detection of diseases such as Malaria, Tuberculosis etc. which is done typically via. Blood testing.  And our particular model, will focus on one of these units: **Amplification unit (A type of plug flow reactor) of the system**


### Mathematical Model of PCR

Every genome (disease injected/ human DNA) has a particular fluorescence value which, when detected, confirms the presence of a disease in the system as well as the stage of the disease depending upon the genome count. However, for all practical testing purposes, we can only draw up to a millilitre of patient’s blood, which is not enough for detection. <br/>

A DNA amplification sequence will move ahead in 3 steps as per out model: <br/>

<img src="model.jpg" alt="PCR cycle" width="600" height="300">
Step 1: Denaturation of Double strand DNA (dsDNA):,<br/>

   dsDNA -> 2*ssDNA (T= 368.15 K) <br/>
      
Step 2: Annealing of Single strand DNA (ssDNA):<br/>
     
   ssDNA + enzyme  aDNA (T= 328.15 K) <br/>

Step 3: Extension of Annealed DNA (aDNA):<br/>
    
   aDNA -> dsDNA (T= 345.15 K) <br/>
      
All these steps have an associated efficiency(yield in this case): 
Ꜫdenaturation, Ꜫannealing & Ꜫextension <br/>

Hence, we can write, after ‘n’ such PCR cycles: <br/>

[DNA]<sub>n</sub> = [DNA]<sub>0</sub>(1+Ꜫ)<sup>n</sup>      Where, Ꜫ = Ꜫ<sub>denaturation</sub> * Ꜫ<sub>annealing</sub> * Ꜫ<sub>extension</sub> = efficiency of PCR unit <br/>


And the detected fluorescence is:  Fn = α * [DNA]<sub>n</sub>   Where α is the const. of proportionality dependent on the instrument used to measure the fluorescence. <br/>


**Process variables**: Concentration, time, efficiency          **Defined/ Control variables**: T, ΔP

### Computational Model

