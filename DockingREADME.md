# Ligand Docking ReadMe

*Note: This ReadMe assumes the use of Windows 10 OS. Other systems have not been able to run successfully.*

## Molecule Downloads
- [pUL15C Download link (.pdb)](https://www.rcsb.org/structure/4IOX)
- [Nelfinavir Download link (.sdf)](https://zinc.docking.org/substances/ZINC000003833846/)

## Software Packages Needed
- ADFR
- Meeko
- MGLTools
- AutoDock Vina
- UCSF Chimera
- Anaconda/pip (will assume downloaded)
- PyMol (will assume downloaded)

The first step for ligand docking is the preparation of the ligand and protein. For this step, three software packages are needed. The donwloads for each package are linked below:

[ADFR Download link](https://ccsb.scripps.edu/adfr/downloads/)

[MGLTools Installation](https://manual.gromacs.org/documentation/current/install-guide/index.html)

Meeko Installation Commands:

1. Create conda virtual environment
```
$ conda create -n vina python=3
$ conda activate vina
$ conda config --env --add channels conda-forge
```
2. Install Meeko. Whenever using Meeko, activate the conda environment.
```
$ conda activate vina
$ conda install -c conda-forge numpy openbabel
$ pip install meeko
```

Once these packages are installed, we can prepare the ligand and protein for docking. 

*Note: the protein file will be referred to as `<protein_name>` while the ligand will be referred to as `ligand_name`*

## Docking Preparation Instructions

### 0. Before anything begins, the protein must have hydrogens added. Load the protein into PyMol and run the command `h_add`. Save the resulting pdb to <protein_name>.pdb.

### 1. Prepare receptor (protein) using ADFR prepare_receptor command. Make sure that ADFR is in the machine's path. This process outputs a PDBQT file which is used by vina. 

`$ prepare_receptor -r <protein_name>.pdb -o <protein_name>.pdbqt`

### 2. Prepare ligand using Meeko. Activate the conda environment to use the script. This process outputs a PDBQT file which is used by vina.

`$ conda activate vina`
`mk_prepare_ligand.py -i <ligand_name>.sdf -o <ligand_name>.pdbqt --pH 7.4`

### 3. Load AutoDockTools GUI and drag the PDBQT file of the protein into the GUI. The protein should load. On the row labeled 'ADT4.2', find the dropdown labeled 'Grid'. Hover over 'Macromolecule' in the Grid dropdown and click 'Choose'. Accept all potential warnings.

### 4. Under the 'Grid' dropdown, choose 'GridBox'. A second GUI should appear with sizes (default at 40) as well as coordinates. Save these values in a .txt file (named config.txt for this example) with the format below. Size is set to 126 in this example because it is the 

```
center_x = <x coord>
center_y = <y coord>
center_z = <z coord>

size_x = 126.0
size_y = 126.0
size_z = 126.0
```

Once this is completed, you should have PDBQT files of the protein and ligand as well as a config.txt file. The next step is to run docking and post-docking visualization of the ligand-protein complex. To do this, two packages should be installed:

[Vina Download link (this example uses the Windows executable)](https://github.com/ccsb-scripps/AutoDock-Vina/releases)

[UCSF Chimera](https://www.cgl.ucsf.edu/chimera/download.html)

*Note: Don't download ChimeraX, it doesn't include the required commands to create the ligand-protein complex.*

## Docking and Post-Docking Visualization Instructions

### 5. Run the docking simulation with the Vina forcefield. The exhaustiveness is set to 32, which maximizes the chance of one good pose.

`$ vina --receptor <protein_name>.pdbqt --ligand <ligand_name>.pdbqt --config config.txt --exhaustiveness=32 --out <output_name>.pdbqt`

The terminal should run and a result with the top nine poses should be displayed in console. To visualize the ligand-protein complex, follow the steps below.

### 6. Open the saved output PDBQT file in AutoDockTools. On the left side under 'All Molecules', delete every pose except the first one. Save this pose to a pdb file under File->Save->Write PDB.

### 7. Open UCSF Chimera and load in both the original protein pdb and the saved ligand pdb from #6. This can be done with File->Open.

### 8. Once both molecules are loaded in, open Favorites->Model Panel. On the bottom right of the pop up, choose Configure...

### 9. In the Configure pop up, choose Double Click and add 'copy/combine' from the Function Menu to the Execution List. Returning to the Model Panel, confirm that both the protein the ligand are active and double click <protein_name>.pdb

### 10. A pop up to combine molecules should appear. Select both <protein_name>.pdb and <ligand_name>.pdb in 'Molecules to combine/copy'. Click ok to create the ligand-protein complex.

### 11. To save the complex, go to File->Save PDB. Choose a file name to save and in 'Save models', only select the combination. Click save to save the ligand-protein complex. This file can then be viewed in PyMol.

