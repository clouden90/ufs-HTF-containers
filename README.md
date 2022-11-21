# Prototype UFS Hierarchical Testing Framework (UFS-HTF)

## Goals for prototype UFS-HTF
The objective of this work is to provide a framework to homogenize the testing capabilities among the UFS repositories. The ultimate goal is to develop a transparent and approachable framework for UFS community testing of the UFS WM and its components consistent with the HSD (Hierarchical Systems Development) approach and NOAA baseline operational metrics, that can be easily used by the community.

The current work is focused on the engineering component of the testing framework, investigating the possibilities that broadly used technologies, already integrated into the UFS (i.e., cmake) can provide this necessary framework, and implementing the existing tests.

We expect output of this prototype development would be:

* Simplification of the code management;
* User-friendliness of the testing for the broader UFS-community;
* Reduction of computational cost;
* Reproducibility of the testing results independently of the HPC.

At this stage, we are exploring low-handing fruits for the prototype testing framework. We are combining [Docker container](https://www.docker.com/) and the [CTest](https://cmake.org/cmake/help/book/mastering-cmake/chapter/Testing%20With%20CMake%20and%20CTest.html) approaches. 

## CTest
CTest is the part of CMake that handles testing code. Ctest allows for an easy way to run user-defined test cases with various argument and option settings, and then check the results against expected output/baseline.

The following are the benefits of using CTest for our prototype HTF: 

* Ctest is the CMake test driver. Since CMake-based build system is used for all UFS components and applications, it is suitable to use CTest without any further changes to conduct tests and report results;
* Developers/users working on new codes can easily/quickly follow a few steps to design/add theirs new tests in Ctest with the HSD concepts;
* It is easy to maintain repeatability and reproducibility when transfer test cases to other users and whole community using Ctest;
* Ctest approach has potential to integrate with NOAA-EMC/GDASApp (emc jedi-base DA system with Ctest implementation already) as well as the recent developed EPIC Jenkins CI/CD pipeline.

## Docker container
The following are the benefits of using Docker container for our prototype HTF:

* A container is a stand-alone, software artifact that packages applications (e.g., an atmospheric model) for portability.
* Model installation made easy. No need to worry about environmental variables and library dependencies.
* Test case becomes portable, from laptop to cloud.
* Ensure bitwise identical results, regardless of different computing systems.

## Building and Running the UFS-HTF
The UFS-HTF provides a platform for users to design/add/run their own testing cases. Once the HTF is built, users can follow the instruction/examples to configure and add test cases for thier UFS applications based on the concept of HIERARCHICAL SYSTEM DEVELOPMENT (HSD).

Currently, the UFS-HTF has been tested in the following machines that Docker is pre-installed:

Machine     | NoaaCloud(AWS)                     | 
------------| -----------------------------------|
machine configurations | VCPUS: 96; MEM: 128 GB  |

The prototype UFS-HTF should work with any linux systems with CMake Version > 3.19 and pre-installed Docker.

### Download the UFS-HTF prototype
The UFS-HTF prototype is publicly available on GitHub. To download the HTF code, clone the ``develop`` branch and sub-modules of the repository:
```
git clone --recurse-submodules -b develop https://github.com/clouden90/ufs-HTF-containers.git
```

### Build the UFS-HTF Using CMake
In the ``ufs-HTF-containers`` directory, create a subdirectory to hold the build’s executables:
```
cd ufs-HTF-containers
mkdir build
cd build
```
From the ``build`` directory, run the following commands to build the testing platform and create base docker images for the exaple testing cases:
```
cmake -DRUN_DOCKER_AS_ROOT=ON ..
make build_docker_images
```
The ``RUN_DOCKER_AS_ROOT`` flag is mainly for the machine (e.g. NOAACLOUD) that requires root premission to run docker executable. The default is ``OFF``.

### Running Test Cases
After the build step, the testing platform and base docker images should be created. One can list images in local Docker repository
