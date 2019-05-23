# DICEtools
Repository of selected Python 3 scripts used to aid data analysis and input generation of Monte Carlo and Configurational Bias Monte Carlo simulations performed with Dice.


## Dependencies
To use the scripts a few Python 3 dependencies are needed, for example
- [NumPy](http://www.numpy.org/)
- [OpenBabel](http://openbabel.org/)
- [matplotlib](https://matplotlib.org/)

The number of dependencies may vary depending on which analysis script one is using, those are just the most common used in almost all the scripts.
You can install these dependencies in any way you want, however, we encourage the use of the [Anaconda](https://www.anaconda.com/download/) Python distribution.
To install the libraries with Anaconda, do the following:
```
conda install numpy
conda install -c openbabel openbabel
conda install matplotlib
```

If you want to use the graphical interface of DiceWin to perform some analysis, you will also need some stuff like:
- [SciPy](https://www.scipy.org/) 
- [SIP](https://pypi.org/project/SIP/)
- [PyQt5](https://riverbankcomputing.com/software/pyqt/intro)

that can also be easily installed with conda with
```
conda install scipy
conda install sip
conda install pyqt=5
```

## Short description of tools
All the scripts can be run with the `-h` option to show a brief description of what the script does and the mandatory and optional parameters.

### ang_distr_from_torsionals.py
Receives the file name of a file containing data of a angle (or torsional angle) as one number per line, and an integer (number of bins) to give a file "pdf.dat" and a plot of the probability density function interpolated from the histogram.

### calculate_angles.py
Receives a file containing a molecular trajectory in any format supported by OpenBabel, and 3 integers (indexes of atoms) to compute the angle between the atoms and print it to screen.

### calculate_dihedrals.py
Same as calculate_angles.py, but receives 4 integers defining a dihedral angle.

### calculate_dipoles.py
Given a .xyz file with a trajectory and a .txt DICE input to print the dipole moment for each configuration.

### clean_dof_dfr.py
Receives a .dfr DICE input that had some fragment types changed to "R" (rigid) and simplifies the .dfr removing unecessary information related to the rigid degrees of freedom.

### dice2gromacs.py
Receives a .dfr and a .txt to convert the DICE inputs to GROMACS inputs .gro and .top (with a separate .itp for the molecular topology). When running the script, you need to specify the force field, either opls or amber, in the command line. The force field name is used to select the combination rules and fudges correctly.

### dicewin.py
Graphical user interface that can open files generated by DICE to plot the evolution of properties with the simulation steps, plot all the radial distribution functions, calculate statistical correlation and more. The interface is very intuitive, but for more information you can see the [manual](man/dicewin_manual.pdf) (unfortunately, just in Portuguese at the moment).

### dihedral_step_evolution.py
Receives a file containing several angles (normally a dihedral angles) one in each line and an integer (usually the interval "isave" used in DICE input) to plot how this angle changed during a simulation.

### distance_evolution.py
Receives a trajectory and two integer (atom indexes) to plot the evolution of a distance with the simulation steps.

### estimate_variance_timeseries.py
Receives a file containing a series of numbers, one in each line (usually the output of calculate_dihedrals.py), classifies all the values as belonging to a group in [min,max] or [min2,max2] or not and then estimate the variance, or how much the populations deviate from the others inside each one of the "nwindows" (integer parameter).

### fragGen.py
The fragGen is an script used to generate the input for CBMC simulations with DICE. It receives a file containing the geometry for a molecule in any format supported by OpenBabel to generate the .dfr and .txt files. 
fragGen always generates the maximum fragmentation of the molecule, breaking the molecule into the rotatable bonds.
After running fragGen, the user still needs to specify the force field parameters in the .dfr and .txt

### get_conf_traj.py
Given a DICE .xyz trajectory and a configuration number (integer), extracts the configuration labeled with this configuration number from the trajectory and print to STDOUT. Useful, for example, to extract the whole configuration (considering the solvent) of a medoid of a cluster found with [Clustering Trajectory](https://github.com/hmcezar/clustering-traj).

### get_solute_xyz.py
Given a .xyz file and an integer representing the number of atoms, print the first "natoms" atoms for the molecule as a .xyz. Usually used to extract the solute configurations from the simulation boxes, with "natoms" being the number of atoms of the solute.

### gromacs2dice.py
Receives a GROMACS topology file (.top or .itp) built using either OPLS-AA or an AMBER variation, and a file containing the geometry of the molecule (with the atoms in the same order) in .gro or any format supported by OpenBabel.
The script automatically converts the input to the DICE format (.txt and .dfr) also generating the maximum fragmentation of the molecule.
This script is particularly useful because one can use one of the several possible topology generator tools, like [MKTOP](http://www.aribeiro.net.br/mktop/), [Antechamber](http://ambermd.org/antechamber/ac.html) or [LigParGen](http://zarbi.chem.yale.edu/ligpargen/) and then convert the topology to the DICE format.
Beware though that you **MUST** check your topology when using these tools, specially your dihedrals.
Depending on the type of molecule, it is not unusual for these tools to get some dihedral energies VERY wrong, and it is your job to identify and correct them.
To check the dihedrals, you can use, e.g., the plot_eff_tors.py script.

### plot_eff_tors.py
The plot_eff_tors is a script to find the rotational barrier between two fragments of a molecule.
What is does is to divide the molecule in two parts around the chosen rotatable bond, keeping the parts as rigid bodies but rotating one of the sides around the rotatable bond.
During this rotation, the energies are evaluated based on the given .dfr parameters and configurations at each step of the rotation can be stored.
If the used wants, the script can also generate a Gaussian input based on a given .txt file containing the method, basis set and charge and multiplicity (as one usually have in the beginning of each Gaussian input).
This input contains all the conformations of the rotation linked, and can be used to perform single point calculations and get the energy profile of the rotation.
By comparing both the molecular mechanics and quantum energy profiles, the user may adjust the force field parameters to then perform the simulation.

### plot_en_angle_gaussian_scan.py
This script receives a .log of the Gaussian calculation performed with the input of plot_eff_tors and then extracts the curve of dihedral angle vs energy.

### probability_interval.py
Given a file containing a value per line, this script gives the probability of getting one value in a given interval.
This is specially useful for computing, e.g., the number of *cis* configurations of a trajectory based on a list of dihedral angles.

### reorder_ligpargen.py
Sometimes the LigParGen web server scrambles the atoms after running the parametrization. This script receives the original .pdb uploaded to LigParGen and the LigParGen outputs .gro and .itp to reorder these output to have the atoms in the same order of the uploaded .pdb.

### separate_configs_box.py
Given a trajectory in .xyz, this script select a few configurations separated by an interval of steps and outputs them to STDOUT.
This is useful if you saved configurations too often during the simulation and want to filter just a few of them.

### solute_en_vs_torsion.py
Receives a text file contaning one dihedral angle per line (generated from calculate_dihedrals.py) and the .ien and .e12 from DICE.
Two plots are generated: one that associates each dihedral angle to an intra molecular energy (U_{intra}) and solute solvent energy (U_{xs}), plotting the spread of the values as a scatter plot; and a second plot where the U_{intra} and U_{xs} are binned and then averaged (for a range of dihedral angles some configurations exist, the energy of these configurations are averaged), plotting as error bars the standard deviation of each of these averages.
A fourth optional argument of the script is the number of bins used to the second plot (default = 36, meaning each bin is 10 degrees wide).

## Authorship
Most of the scripts here were written by Henrique Musseli Cezar, with the exception of DiceWin which was written by Thiago de Souza Duarte.
These tools were written with the important contribution of Prof. Kaline Coutinho, who supervised the work and gave suggestions to the improvement of the tools.


## Acknowledgments
We thank the Brazilian funding agencies [CNPq](http://www.cnpq.br/) and [CAPES](http://www.capes.gov.br/) for the fellowships and the approved research projects.
Most of this work was done under a CNPq PhD fellowship for Henrique Musseli Cezar (grant number 140489/2015-0) and undergraduate research fellowships for Thiago de Souza Duarte.