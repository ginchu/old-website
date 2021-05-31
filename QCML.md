# QCML
## Installation and Setup
### 1. MOPAC
- Request for license and download [MOPAC](http://openmopac.net/downloads.html)
- Follow installation instructions.txt in the downloaded zip folder
- To install locally,
    - Create a directory called *mopac* and move contents of downloaded zip folder into it
    - On command line/terminal:
        ```
        export LD_LIBRARY_PATH=/path/to/mopac
        export MOPAC_LICENSE=/path/to/mopac
        /path/to/mopac/MOPAC2016.exe LICENSE_NUMBER
        ```
    - If permission denied, change permissions on file MOPAC2016.exe by running
        ```
        chmod 755 MOPAC2016.exe
        /path/to/mopac/MOPAC2016.exe LICENSE_NUMBER
        ```
    - A file called pass_for_mopac2016 should be created
- Test the Example_data_set.mop to check that MOPAC works
     ```
    /path/to/mopac/MOPAC2016.exe Example_data_set.mop
    ```
    - An Example_data_set.arc and Example_data_set.out file should be created
    
### 2. Virtual Environment 
- Need python3 and package virtualenv
- Create virtual environment `virtualenv ENVIR_NAME`
    - If you have multiple python versions
        ```
        virtualenv -p /path/to/python3 ENVIR_NAME
        ```
- To activate/use the virtual environment, use this command
    ```
    source ENVIR_NAME/bin/activate  # for LINUX
    .\ENVIR_NAME\Scripts\activate  # for WINDOWS
    ```
- Install required python packages for QCML
    ```
    pip install -r requirements.txt
    ```
- When done with the virtual environment, use this command `deactivate`
### 3. Redirecting ASE to Local MOPAC
- Approach 1: Edit ASE Package File
    - Go into ENVIR_NAME/lib/python3/site-packages/ase/calculators
    - Open mopac.py
    - Change class MOPAC to this
        ```
        class MOPAC(FileIOCalculator):
            implemented_properties = ['energy', 'forces', 'dipole', 'magmom']
            # command = 'mopac PREFIX.mop 2> /dev/null'
            command = 'path/to/mopac/MOPAC2016.exe PREFIX.mop 2> /dev/null'
        ```
- Approach 2: Add Code to Each Project Python File
    - Whenever there is code that looks similar to this
        ```
        mol = read('baker_chan_xyz_files/{}'.format(xyz_file))
        mol.set_calculator(MOPAC(label='tmp/mopac_energy', task='PM7 1SCF UHF GRAD'))
        ```
    - Change to this
        ```
        mol = read('baker_chan_xyz_files/{}'.format(xyz_file))
        mopac = MOPAC(label='tmp/mopac_energy', task='PM7 1SCF UHF GRAD')
        mopac.command = '/path/to/mopac/MOPAC2016.exe PREFIX.mop'
        mol.set_calculator(mopac)
        ```
## Check Setup
- Try `python torch_energy_force_gp.py` on command line/terminal
- It should run successfully if everything was installed and set correctly
