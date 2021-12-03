 # Molecular Dynamics README
 
 ## Software Packages Needed
 - GROMACS
 - Grace

*Note: the input PDB files have already been pre-processed in order to keep the focus on the molecular dynamics specific commands. For conciseness, `<protein_name>` refers to the input protein (in this project it’s either pUL15c or portal)*

To begin the molecular dynamics, **GROMACS** must be installed on the machine chosen to run the simulation. **GROMACS** is a software package that prepares an input protein (encoded in a PDB file for instance) for simulation and runs the result subsequently.

[Gromacs Download link](https://manual.gromacs.org/documentation/2021.4/download.html)

[Gromacs Installation Guide](https://manual.gromacs.org/documentation/current/install-guide/index.html)

Once **GROMACS** is installed, all of its tools can be accessed through the `gmx` command

Grace is a plotting tool that will be necessary in order to plot the `.xvg` files outputted by a simulation run. GROMACS creates these files to be plotted only by Grace. Grace is made for Unix-based systems, which would include Mac computers and any Linux distributions. To run Grace on a Windows computer, a wrapper program called **qtGrace** exists.

[(Mac / Linux) Grace installation link](https://formulae.brew.sh/formula/grace#default)

[(Windows) qtGrace installation link](https://sourceforge.net/projects/qtgrace/)

`.mdp` files contain parameterization needed to run the **GROMACS** tools, and will inform the underlying algorithms on things such as convergence condition, number of iterations, etc.

## Instructions
### 1. Generate topology file using the `gmx2pdb` tool

`$ gmx pdb2gmx -f <protein_name>.pdb -o <protein_name>_processed.gro -water -spce`

### 2. Choose to use the **AMBER03** force field for simulation when prompted 
### 3. Solvate the protein system by first creating a cubic enclosure

`$ gmx editconf -f <protein_name>_processed.gro -o <protein_name>_newbox.gro -c -d 1.0 -bt cubic`
### 4. Add water molecules using the solvate tool

`$ gmx solvate -cp <protein_name>_newbox.gro -cs spc216.gro -o <protein_name>_solv.gro -p topol.top`

### 5. Add ions to the solvated system to create neutralization

`$ gmx grompp -f ions.mdp -c <protein_name>_solv.gro -p topol.top -o ions.tpr`

*Note: this intermediate .tpr file contains a parameterization of the atoms in the system*

`$ gmx genion -s ions.tpr -o <protein_name>_solv_ions.gro -p topol.top -pname NA -nname CL -neutral`

### 6. Minimize the energy of the protein-water-ion system so structure starts off at a natural conformation

`$ gmx grompp -f minim.mdp -c <protein_name>_solv_ions.gro -p topol.top -o em.tpr`

`$ gmx mdrun -v -deffnm em`

### 7. Equilibrate the system to come to a stable temperature


`$ gmx grompp -f nvt.mdp -c em.gro -r em.gro -p topol.top -o nvt.tpr`

`$ gmx mdrun -deffnm nvt`

### 8. Equilibrate the system to come to a stable density

`$ gmx grompp -f npt.mdp -c nvt.gro -r nvt.gro -t nvt.cpt -p topol.top -o npt.tpr`
`$ gmx mdrun -deffnm npt`

### 9. Create setup for protein trajectory during simulation run

`$ gmx grompp -f md.mdp -c npt.gro -t npt.cpt -p topol.top -o md_0_1.tpr`

### 10. Run the simulation

`$ gmx mdrun -deffnm md_0_1`
