# SHub
Recipes for Singularity images to be built on Singularity Hub.

[![https://www.singularity-hub.org/static/img/hosted-singularity--hub-%23e32929.svg](https://www.singularity-hub.org/static/img/hosted-singularity--hub-%23e32929.svg)](https://singularity-hub.org/collections/4666)

## ArgonCube Optical Simulation [<img src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png" width="30">](https://github.com/PPKoller/ArCubeOptSim) [<img src="https://github.com/PPKoller/SHub/blob/master/.ArCube_Logo.png" width="100" align="right">](https://argoncube.org/)
### 1. Pull image:
```bash
singularity pull shub://PPKoller/SHub:root6.geant4.optsim.ubuntu-18.04
```
### 2. Image default checks:
Performing the Singularity default checks should return: `PASS: (retval=0)`.
```bash
mv PPKoller-SHub-master-root6.geant4.optsim.ubuntu-18.04.simg OptSim.simg
singularity check --tag default OptSim.simg
```
### 4. Run instructions:
```bash
mkdir output
singularity run -B output:/output OptSim.simg
```
(where we use the `-B` argument to bind the host-dir `output` to the img-dir `/output`)
### [optional]
#### Build and shell into writable sandbox image:
```bash
sudo singularity build --sandbox OptSim OptSim.simg
sudo singularity shell --writable OptSim
```
#### Build compressed read-only squashfs image from sandbox image:
```bash
sudo singularity build OptSim_edited.simg OptSim
```
