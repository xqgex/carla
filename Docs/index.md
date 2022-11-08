# RACER-SIM

Welcome to the CARLA RACER-SIM project documentation. The following page contains installation information specific to the RACER-SIM project. This comes bundled with the [general CARLA documentation](carla_home.md) that can also be found online [here](https://carla.readthedocs.io/en/latest/). 

* __[Requirements](#requirements)__ 
* __[Unreal Engine Editor](#unreal-engine-editor)__    
* __[Package](#package)__  
    * [Package flags](#package-flags)
* __[Installing CUDA and CUDNN dependencies](#installing-cuda-and-cudnn-dependencies)__
* __[Terrain Editor](#terrain-editor)__ 


## Requirements

*   CUDA capable GPU with more than 20GB of video RAM (RTX 3090 was used for testing).
64GB of main RAM memory.
*   2TB of hard drive for long sessions and to develop new maps.
Unreal Engine Editor
*   Ubuntu 20.04

## Running CARLA

There are two ways to run the CARLA simulator. One is to run from the package, which is a ready to run version that can be launched quickly from the command line. The second method is to build from source. Building from source gives access to the CARLA version of the Unreal Editor, enabling the use of the unreal editor for building custom maps. 

## Unreal Engine Editor

When built from source, the CARLA version of the Unreal Editor can be launched to edit or create maps, assets and simply build new things on top of this project. Details on building CARLA from source can be found in the [CARLA build docs](build_carla.md). Follow the instructions to build from source, __but do not clone the CARLA repository from GitHub__, use the source code delivered with the RACER-SIM materials.

There are 3 main commands to be aware of, from the root of the CARLA repository you can type:

*   `make launch ARGS=”--pytorch”` -  launches the editor itself with all of carla files and assets.

*   `make PythonAPI ARGS=”--pytorch”` - compiles the PythonAPI to enable python scripts to load the carla library in order to interface with the simulation.

*   `make clean ARGS=”--pytorch”` - cleans intermediate files to recompile the project, useful in case something goes wrong during compilation.

Notice that the `ARGS=”--pytorch”` flag is absent in the general documentation but it is necessary to enable the terramechanics module used for the RACER-SIM project.

## Package

The package is the ready to run format, we provide 3 packages __CARLA_Base_RacerSim.tar.gz__, __Desert.tar.gz__ and __Forest.tar.gz__. 

*   __CARLA_Base_RacerSim.tar.gz__ - This package contains the binaries needed to run the simulation and connect through the client. The PythonAPI folder contains scripts to connect a client to the simulation, load maps, a simple interface to drive a vehicle, etc.

*   __Desert.tar.gz__ and __Forest.tar.gz__ - These two packages contain the maps Desert and Forest respectively. The files need to be extracted in the root folder of the first package in order to load the maps into the simulation. To load each map you should move to the `PythonAPI/util` folder and run `python3 config.py -m DesertMap` or `python3 config.py -m ForestMap` to load each map respectively.

### Package flags

When running the package there are a few flags that change the simulation before running it. The recommended command line to run the package is the following:

```sh
CarlaUE4.sh --network-path=path/to/wrapped_gnn_cuda.pt --max-particles-per-wheel=10000 --box-search-forward=0.6 --box-search-lateral=0.15 --box-search-depth=0.15 --use-local-frame --floor-height=0.1
```

*   __network-path__: defines the path of the Neural Network controlling the terramechanics module.
max-particles-per-wheel: sets a maximum amount of particles per wheel, useful to control a tradeoff between accuracy and performance.

*   __box-search-forward__: control the dimension of the box sampling the terrain around the wheel in the direction of motion.

*   __box-search-lateral__: control the dimension of the box sampling the terrain around the wheel in the lateral direction of motion.

* __box-search-lateral__: control the dimension of the box sampling the terrain around the wheel in the vertical direction.

*    __use-local-frame__: necessary flag to center the coordinate system around the wheel. Can be deactivated to obtain data in global coordinates when developing a new Neural Network model.

*   __floor-height__: modifies the terrain sampling to better fit the landscape layout (moves the sampling slightly up or down).

## Installing CUDA and CUDNN dependencies

This guide helps installing the CUDA dependencies to run pytorch models.

This is the main guide

*   __1.__ Remove all existing drivers and CUDA with
    *   `sudo apt-get purge nvidia* cuda* cudnn*`
    *   Any other CUDA or nvidia driver should be removed
    *   `reboot`

*   __2.__ Install the CUDA runtime, steps __a__ to __g__ are the summarized version of the instructions [on the NVIDIA site](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#ubuntu-installation). This will also install a matching driver (latest nvidia driver 515)

    *   Ubuntu 18: 
    
        *   `wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-keyring_1.0-1_all.deb`
    

    *   Ubuntu 20: 
    
        *   `wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-keyring_1.0-1_all.deb`

    *   `sudo dpkg -i cuda-keyring_1.0-1_all.deb`
    *   `sudo apt-get update`
    *   `sudo apt-get install cuda`
    *   `sudo apt-get install nvidia-gds`
    *   `reboot`

* __3.__ Install CUDNN, steps a to h are the summarized version of the instructions [on the NVIDIA site](https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html#install-linux)

    *   Download your matching ubuntu version CUDNN (Deb) [here](https://developer.nvidia.com/cudnn) 
	*   In the next  steps, replace ${OS}-8.x.x.x_1.0-1 with the corresponding file name matching os and version. You can pick the CUDA 11.x (example for Ubuntu20.04: `sudo dpkg -i cudnn-local-repo-ubuntu2004-8.6.0.163_1.0-1_amd64.deb`)

    *   `sudo dpkg -i cudnn-local-repo-${OS}-8.x.x.x_1.0-1_amd64.deb` or `sudo dpkg -i cudnn-local-repo-${OS}-8.x.x.x_1.0-1_arm64.deb`
    *   Import the CUDA GPG keys: `sudo cp /var/cudnn-local-repo-*/cudnn-local-*-keyring.gpg /usr/share/keyrings/`
    *   In the next  steps, replace cudaX.Y with the cuda version (e.g. cuda11.7)

    *   Refresh the repository metadata: `sudo apt-get update`
    *   Install the runtime library: `sudo apt-get install libcudnn8=8.x.x.x-1+cudaX.Y`
    *   Install the developer library: `sudo apt-get install libcudnn8-dev=8.x.x.x-1+cudaX.Y`
    *   Install the code samples and the cuDNN library documentation: `sudo apt-get install libcudnn8-samples=8.x.x.x-1+cudaX.Y`
    *   `reboot`


## Terrain Editor

The CARLA RACER-SIM software includes the [__Desert and Forest Terrain Editor__](tool_terrain_editor.md). This allows the development of randomly generated maps with unique terrain characteristics to create a high diversity of virtual 3D environments for generating training data for off-road AVs. Please refer to [the documentation](tool_terrain_editor.md) for usage instructions.