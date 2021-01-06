# SHub
Recipes for Singularity images to be built on Singularity Hub.

[![https://www.singularity-hub.org/static/img/hosted-singularity--hub-%23e32929.svg](https://www.singularity-hub.org/static/img/hosted-singularity--hub-%23e32929.svg)](https://singularity-hub.org/collections/4666)

## ArgonCube Optical Simulation [<img src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png" width="30">](https://github.com/PPKoller/ArCubeOptSim) [<img src="https://github.com/PPKoller/SHub/blob/master/.ArCube_Logo.png" width="100" align="right">](https://argoncube.org/)
### 1. Pull the container image:
The optical simulation software container can be pulled directly via the Singularity command:\
(size ~ 1.4G)
```bash
singularity pull shub://PPKoller/SHub:root6.geant4.optsim.ubuntu-18.04
```
### 2. Image default checks:
Performing the Singularity default checks should return `PASS: (retval=0)`.
```bash
mv PPKoller-SHub-master-root6.geant4.optsim.ubuntu-18.04.simg OptSim.simg
singularity check --tag default OptSim.simg
```
### 3. Export I/O binding paths:
Using the environment variable `$SINGULARITY_BINDPATH` there won't be any need to bind I/O paths manually later.
```bash
mkdir input output
export SINGULARITY_BINDPATH="input/:/input,output/:/output"
```
### 4. Run instructions:
Running the container without any arguments will return a list of the available apps including a short description on what it does and what parameters you might need to provide.
```bash
singularity run OptSim.simg
```
### 5. Run apps:
There are five apps available within the container: four simulaion related apps that run the optical simulation with different levels of user defined input and one app that allows you to build the photon look-up-table using the output created by running the simulation.

**The selected voxels will be processed sequentially. Separate container calls are needed for parallel processing.**

- **sim**\
Run the simulation on voxels no. 0 to 9 using the default statistics, voxel geometry and optical properties.

  `singularity run --app sim OptSim.simg 0 10`

  *Statistics*: 1'000 events per voxel / 10'000 photons per event
  
  *Voxel geometry*: 32 x 128 x 32 voxels / 9.460 x 9.858 x 9.692 mm<sup>3</sup> (drift x vertical x beam)
  
  *Opt. properties*: [PPKoller/ArCubeOptSim/tree/LUT/resources/datafiles](https://github.com/PPKoller/ArCubeOptSim/tree/LUT/resources/datafiles)
  
- **sim_usr_geo**\
Run the simulation on voxels no. 0 to 9 with user defined statistics and voxel geometry. Herefore, the file `OptSim_LUT_voxel_table.txt` has to be placed in the folder `input/` before executing the simulation.

  `singularity run --app sim_usr_geo OptSim.simg 0 10`
  
  The file `OptSim_LUT_voxel_table.txt` can be created by the Jupyter Notebook provided [here](create_OptSim_LUT_voxel_table.ipynb).

- **sim_usr_opt**\
Run the simulation on voxels no. 0 to 9 with user defined optical properties. Herefore, a folder `datafiles/` containing all optical properties files has to be placed in the folder `input/` before executing the simulation.

  `singularity run --app sim_usr_opt OptSim.simg 0 10`
  
  The folder `datafiles/` containing the default optical properties files can be found [here](https://github.com/PPKoller/ArCubeOptSim/tree/LUT/resources/datafiles).
  
- **sim_usr**\
Run the simulation on voxels no. 0 to 9 with user defined statistics, voxel geometry and optical properties. (see instructions above)

  `singularity run --app sim_usr OptSim.simg 0 10`
    
- **lut / lut_usr**\
Build the photon look-up-table using the output created by running the simulation. Herefore, voxel number '0' needs to have been processed and the respective root file `OptSim_00000000.root` has to be present in `output/root_files/`.

  `singularity run --app lut OptSim.simg`
  
  And in case the simulation was run with user defined statistics and voxel geometry:

  `singularity run --app lut_usr OptSim.simg`
  
### 6. Output
After running the optical simulation, log and error files will appear in `output/log_files/` and root files will appear in `output/root_files/`.

After running the LUT builder, the photon look-up-table will apper in `output/` as `OptSim_LUT_ArgonCube2x2.root`.

### [optional]
#### User defined TPB thickness
Place the file `preinit.mac` with custom TPB thickness in the folder `input/` before executing the simulation. The default `preinit.mac` can be found [here](https://github.com/PPKoller/ArCubeOptSim/tree/LUT/resources/macros/preinit.mac).

#### Build and shell into writable sandbox image:
```bash
sudo singularity build --sandbox OptSim OptSim.simg
sudo singularity shell --writable OptSim
```
#### Build compressed read-only squashfs image from sandbox image:
```bash
sudo singularity build OptSim_edited.simg OptSim
```
