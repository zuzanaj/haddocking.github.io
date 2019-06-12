---
layout: page
ags: [Jekyll, HADDOCK, Bonvin, Docking, Simulation, Molecular Dynamics, Structural Biology, Computational Biology, Modelling, Protein Structure]
modified: 2014-08-08T20:53:07.573882-04:00
comments: false
image:
  feature: pages/banner_software.jpg
---

# <font color="RED">HADDOCK2.4</font> manual

## <font color="RED">T</font>he <font color="RED">D</font>ocking

* * *

*   [Before starting](#start)
*   [Topologies and structures generation](#topology)
*   [Rigid body energy minimization](#mini)
*   [Semi-flexible simulated annealing](#sa)
*   [Flexible refinement in explicit solvent (water or DMSO)](#water)

* * *

<a name="start">**<u>Before starting</u>**</a>  

Before starting the docking, you need to specify a number of parameters, such as:
*   Number of structures to generate and refine
*   Histidine protonation state
*   Flexible segments
*   Which kind of restraints to use and associated parameters
*   Solvated docking options
*   Scoring scheme
*   Electrostatic treatment
*   ...

Many of those have default values which you do not need to change.  

Using your web browser, go to the [HADDOCK online page](/software/haddock2.4/start_new), and use the Edit your run.cns option, choose the **run.cns** file to edit and click on **"edit file"**. A new window will be created. (If the window is empty, try using a different browser).  

For a description of the run.cns file, refer to the "[run.cns file](/software/haddock2.4/run)" section.  

After setting all the parameters save the file as "run.cns" in your run directory using the **"save updated file"** button.  

**Note:** If you have turned on the use of DNA/RNA restraints in **run.cns** HADDOCK expects to find a file called **dna-rna_restraints.def** in the **data/sequence** directory. This files allows you to define standard A-, B- or custom restraints for DNA such as base-pairing, puckering and backbone dihedral angles. You can edit a template file that can be found in the **protocols** directory and save it as **dna-rna_restraint.def** into the **data/sequence** directory. Using your web browser, go to the [HADDOCK online page](/software/haddock2.4/start_new), and use the Edit your run.cns option, choose the **dna-rna_restraint.def** file to edit and click on **"edit file"**. A new window will be created. (If the window is empty, try using a different browser).

When all necessary files and parameters have been properly edited and saved then start HADDOCK in the run directory by typing:

<pre style="background-color:#DAE4E7" >    haddock2.4
</pre>

The entire protocol consists of four stages:
1.  [Topologies and structures generation](#topology)
2.  [Randomization of the starting orientation and rigid body energy minimization](#mini)
3.  [Semi-flexible simulated annealing](#sa)
4.  [Flexible refinement in explicit solvent (water or DMSO)](#water)  

* * *

<a name="topology">**1\. <u>Topologies and structures generation</u>**</a>  

The first step in HADDOCK in the generation of the CNS topologies and coordinates files for the various molecules and for the complex from the input PDB files (see section [PDB files](/software/haddock2.4/pdb)). HADDOCK should automatically recognize chain breaks, disulphide bonds, cis-prolines and even ions provided they are named as defined in the ion.top topology file located in the ***toppar*** directory.  

Job files will be generated in the ***run*** directory and the topologies, structures and output files will be generated in the ***begin*** directory. HADDOCK will use the fileroot names specified in the [*run.cns*](/software/haddock2.4/run) file.  

The following scripts will be run:  

*   **fileroot_generate_A/B/C/D/E/F.job**: Generates the CNS topology and coordinates file(s) (if starting from an ensemble) for the various molecules.  

Output files:  

*   *fileroot_A/B/C/D/E/F.psf* (topology)
*   *fileroot_A/B/C/D/E/F.pdb* (coordinates)
*   *fileroot_A/B/C/D/E/F_1.pdb, fileroot_A/B/C/D/E/F_2.pdb*, ... (if starting from an ensemble)
*   *file_A/B/C/D/E/F.list* (list of PDB coordinates files)  

CNS scripts called (depending on the options defined):  

*   *generate_A/B/C/D/E/F.inp*
    *   *dna_break.cns*
    *   *dna-rna_restraints.def*
    *   *flex_segment_back.cns*
    *   *iterations.cns*
    *   *prot_break.cns*
    *   *run.cns*

**Note:** If solvated docking is turned on, *generate_A/B/C/D/E/F-water.inp* will be used instead which calls in addition *generate_water.cns,rotate_pdb.cns* and generates additional output pdb files containing the water (*fileroot_A/B/C/D/E/F_1_water.pdbw_, ...*)  

*   **fileroot_generate_complex.job**: Generates the CNS topology and coordinates file(s) for the complex by merging the various topologies and coordinates files. When starting from ensembles, all combinations will be generated.  

Output files:  

*   *fileroot.psf* (topology)
*   *fileroot.pdb* (coordinates)
*   *fileroot_1.pdb, fileroot_2.pdb*, ... (if starting from an ensemble)
*   *file.cns, file.list, file.nam* (list of PDB coordinates files)  

CNS scripts called:  

*   *generate_complex.inp*
    *   *iterations.cns*
    *   *run.cns*
    *   *separate.cns*



**Note:** If solvated docking is turned on, *generate_complex-water.inp* will be used instead which will generates additional output pdb files containing the water (*fileroot_1_water.pdbw* ,...)  
In case of problems (*and in general to make sure that everything is OK*) look into the output files generated (.out) for error messages (search for ERR).  

* * *

<a name="mini">**2\. <u>Randomization and rigid body energy minimization:</u>**</a>  

The first docking step in HADDOCK is a rigid body energy minimization.  

First the molecules will be separated by a minimum of 25Å and rotated randomly around their center of mass. This randomization step can be turned off in the [*run.cns*](/software/haddock2.4/run) parameter file. If you wish to decrease (or increase) the separation distance between the two molecules, edit in the ***protocols*** directory the ***random_rotations.cns*** CNS script and change the value of the *$minispacing* parameter.  
The rigid body minimization is performed stepwise:  

*   four cycles of rotational minimization in which each molecule (molecule+associated solvent in case of solvated docking) is allowed to rotate in turn  

*   two cycles of rotational and translational rigid body minimization in which each molecule+associated solvent is treated as one rigid body  

If ***solvated docking is turned on*** the following additional steps will be performed:  

  *   rotational and translational rigid bogy minimization with each molecule and water molecule treated as separate rigid bodies

  *   Biased Monte Carlo removal of water molecules based on propensity of finding a water mediated contact until a user-defined percentage of water molecules remains

  *   rotational and translational rigid bogy minimization with each molecule and water molecule treated as separate rigid bodies

For details of the solvated docking protocol refer to:

  *   A.D.J. van Dijk and A.M.J.J. Bonvin  
"[Solvated docking: introducing water into the modelling of biomolecular complexes.](https://doi.org/doi:10.1093/bioinformatics/btl395)"  
*Bioinformatics*, **22** 2340-2347 (2006).

  *   M. van Dijk, K. Visscher, P.L. Kastritis and A.M.J.J. Bonvin  
"[Solvated protein-DNA docking using HADDOCK.](https://doi.org/doi:10.1007/s10858-013-9734-x)"  
*J. Biomol. NMR*, **56**, 51-63 (2013).

  *   P.L. Kastritis, K.M. Visscher, A.D.J. van Dijk and <font color="#333333"> A.M.J.J. Bonvin </font>  
"[Solvated docking using Kyte-Doolittle-based water propensities.](https://doi.org/doi:10.1002/prot.24210)"  
*Proteins: Struc. Funct. & Bioinformatic.*, **81**, 510-518 (2013).  

If ***RDC or diffusion anisotropy restraints are used*** two additional minimization steps are carried out to optimize the orientation of the molecules with respect to the alignment tensor(s).  
For each starting structure combination, this step can be repeated a number of times (given by the *Ntrials* parameter in the *[run.cns](/software/haddock2.4/run)* parameter file, and only the best solution is written to disk.  

A new option in HADDOCK 2.x allows to systematically sample 180 degree rotated solutions. Since in our experience symmetrical solutions occur quite often, sampling of 180 degree rotated solutions can increase the success rate significantly. This option can be turned on and off with the *rotate180_0* parameter in the *[run.cns](/software/haddock2.4/run)* parameter file.  

**Note:** The translational minimization can be turned off in *[run.cns](/software/haddock2.4/run#dock)*. This option can be useful for example for small flexible molecules to perform the docking during the simulated annealing stage allowing conformational changes to take place during the docking process. The number of steps in the first two stages of the simulated annealing should then be increased by at least a factor four to allow the molecules to approach each other.  

The ***refine.inp*** CNS script is used for this step.

*   *fileroot_1.pdb, fileroot_2.pdb_, ...* written in the ***structures/it0*** directory

***Note1:*** If solvated docking is turned on (*waterdock=true* in [*run.cns*](/software/haddock2.4/run#iter), additional output pdb files will be written to disk containing the water (*fileroot_1_water.pdbw* ,...).

***Note2:*** If random removal of restraints is turned on (*noecv=true* in [*run.cns*](/software/haddock2.4/run#iter)), additional files will be written to disk containing the random number seed (*fileroot_1.seed* ,...). This seed is used in the refinement to make sure that the same restraints are removed.

***Note3:*** If random AIR definition is turned on (*ranair=true* in [*run.cns*](/software/haddock2.4/run#iter)), additional files will be written to disk containing the list of residues selected for the AIR definition (*fileroot_1.disp* ,...)..


CNS scripts called in sequential order (depending on the options selected):


*   *initialize.cns*
*   *iterations.cns*
*   *run.cns*
*   *read_struc.cns*
*   *covalions.cns*
*   *flags_new.cns*
*   *read_data.cns*
*   *calc_free-ene.cns*
*   *read_water1.cns*
*   *water_rest.cns*
*   *read_data.cns*
*   *flags_new.cns*
*   *randomairs.cns*
*   *water_rest.cns*
*   *symmultimer.cns*
*   *cm-restraints.cns*
*   *surf-restraints.cns*_
*   *random_rotations.cns*
*   *scale_inter_only.cns*
*   *mini_tensor.cns*
*   *mini_tensor_dani.cns*
*   *waterdock_remove-water.cns*
*   *db0.cns*
*   *db00.cns*
*   *db1.cns*
*   *waterdock_mini.cns*
*   *bestener.cns*
*   *rotation180.cns*
*   *bestener.cns*
*   *read_noes.cns*
*   *symmultimer.cns*
*   *read_noes.cns*
*   *scale_inter_only.cns*
*   *print_coorheader.cns*
*   *waterdock_out0.cns*


When all structures have been generated (typically in the order of 1000 to 2000 depending on the number of starting conformations and your CPU resources), HADDOCK will sort them accordingly to the criterion defined in the [*run.cns*](/software/haddock2.4/run#iter) parameter file and write the sorted PDB filenames into *file.cns, file.list* and *file.nam* in the ***structures/it0*** directory. These will be used for the next step (semi-flexible simulated annealing).  

* * *

<a name="sa">**3\. <u>Semi-flexible simulated annealing</u>**</a>  

The best XXX structures after rigid body docking (typically 200, but this is left to the user's choice (see the [run.cns file](/software/haddock2.4/run) section)) will be subjected to a semi-flexible simulated annealing (SA) in torsion angle space. This semi-flexible annealing consists of several stages:  

*   High temperature rigid body search
*   Rigid body SA
*   Semi-flexible SA with flexible side-chains at the interface
*   Semi-flexible SA with fully flexible interface (both backbone and side-chains)  
The temperatures and number of steps for the various stages are defined in the [*run.cns*](/software/haddock2.4/run) parameter file.  

***Note1:*** HADDOCK also allows the definition of fully flexible regions (defined by the *nfle_X* parameter in [run.cns](/software/haddock2.4/run)) that remain fully flexible throughout all four stages. This should be useful for cases where part of a structure are disordered or unstructured or when docking small flexible ligands or peptides onto a protein. This option also allows the use of HADDOCK for structure calculations of complexes when classical NMR restraints are available to drive the folding.  

***Note2:*** A new option in HADDOCK 2.x allows to automatically define the semi-flexible regions by considering all residues within 5A of another molecule. To use this option, set *nseg_X* to -1 in [run.cns](/software/haddock2.4/run) (or another negative number if you still want to define manually segments for [random AIRs](/software/haddock2.4/generate_air_help#ranair) definition from a limited region of the surface). This can be set for each molecule separately.  

The ***refine.inp*** CNS script is used for this step.

*   *fileroot_1.pdb, fileroot_2.pdb_, ... written in the ***structures/it1*** directory  

***Note1:*** If solvated docking is turned on (*waterdock=true* in [*run.cns*](/software/haddock2.4/run#iter)), additional output pdb files will be written to disk containing the water (*fileroot_1_water.pdbw* ,...).  

***Note2:*** If random removal of restraints is turned on (*noecv=true* in [*run.cns*](/software/haddock2.4/run#iter)), additional files will be written to disk containing the random seed number (*fileroot_1.seed* ,...). This seed is used in the explicit solvent refinement to make sure that the same restraints are removed.


*  *initialize.cns*
*   *iterations*
*   *run.cns*
*   *read_struc.cns*
*   *covalions.cns*
*   *flags_new.cns*
*   *read_data.cns*
*   *calc_free-ene.cns*
*   *read_water1.cns*
*   *water_rest.cns*
*   *flags_new.cns*
*   *contactairs.cns*
*   *water_rest.cns*
*   *symmultimer.cns*
*   *cm-restraints.cns*
*   *surf-restraints.cns*
*   *dna-rna_restraints.def
*   *set_noe_scale.cns*
*   *mini_tensor.cns*
*   *mini_tensor_dani.cns*
*   *scale_inter_only.cns*
*   *rotation180.cns*
*   *flags_new.cns*
*   *flex_segment_back.cns*
*   *torsiontop.cns*  
*   *sa_ltad_hightemp.cns*
    - *set_noe_scale.cns*
    - *scale_inter.cns*
*   *sa_ltad_cool1.cns*
    - *set_noe_scale.cns*
    - *scale_inter.cns*
*   *torsiontop_flex.cns*
    - *flex_segment_side.cns*
    - *numtrees.cns*
*   *sa_ltad_cool2.cns*
    - *set_noe_scale.cns*
    - *scale_inter.cns*
*   *torsiontop_flex_back.cns*
    - *flex_segment_back.cns*
    - *numtrees.cns*
*   *sa_ltad_cool3.cns*
    - *set_noe_scale.cns*
    - *scale_inter.cns*
*   *set_noe_scale.cns*
*   *flex_segment_back.cns*
*   *scale_intra_only.cns*
*   *scale_inter_only.cns*
*   *symmultimer.cns*
*   *read_noes.cns*
*   *dna-rna_restraints.def
*   *print_coorheader.cns*
*   *waterdock_out1.cns*

At the end of the calculation, HADDOCK generates the *file.cns, file.list* and *file.nam* files containing the filenames of the generated structures sorted accordingly to the criterion defined in the [run.cns](/software/haddock2.4/run#iter) parameter file.  
At the end of this stage, the structures are analyzed and the results are placed in the ***structures/it1/analysis*** directory (see the [analysis](/software/haddock2.4/analysis) section).  

* * *

<a name="water">**4\. <u>Flexible explicit solvent refinement</u>**</a>  

In this final step, the structures obtained after the semi-flexible simulated annealing are refined in an explicit solvent layer (8A for water, 12.5A for DMSO). In this step, no spectacular changes are expected, however, the scoring of the various structures is improved.  

The ***re_h2o.inp*** or ***re_dmso.inp*** CNS script is used for this step.

*   *fileroot_1w.pdb, fileroot_2w.pdb_, ... written in the ***structures/it1/water*** directory  

***Note1:*** The numbering of the structures from it1 is kept.  

***Note2:*** If *keepwater* is set to true in [*run.cns*](/software/haddock2.4/run#iter), additional output pdb files will be written to disk containing the water (*fileroot_1_h2o.pdb* ,...).

*   *initialize.cns*
*   *iterations.cns*
*   *run.cns*
*   *read_struc.cns*
*   *flags_new.cns*
*   *calc_free-solv-ene.cns*
*   *calc_free-ene.cns*
*   *read_water1.cns*
*   *generate_water.cns (or generate_dmso.cns*)
*   *water_rest.cns*
*   *symmultimer.cns*
*   *set_noe_scale.cns*
*   *dna-rna_restraints.def*
*   *mini_tensor.cns*
*   *mini_tensor_dani.cns*
*   *flex_segment_side.cns*
*   *set_noe_scale.cns*
*   *flex_segment_back.cns*
*   *set_noe_scale.cns*
*   *scale_intra_only.cns*
*   *scale_inter_only.cns*
*   *symmultimer.cns*
*   *read_noes.cns*
*   *dna-rna_restraints.def*
*   *print_coorheader.cns*


At the end of the explicit solvent refinement, HADDOCK generates the *file.cns, file.list* and *file.nam* files containing the filenames of the generated structures sorted accordingly to the criterion defined in the [run.cns](/software/haddock2.4/run#iter) parameter file.  

Finally, the structures are analyzed and the results are placed in the ***structures/it1/water/analysis*** directory (see the [analysis](/software/haddock2.4/analysis) section).  

* * *
