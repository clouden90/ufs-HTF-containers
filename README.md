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
The UFS-HTF prototype is publicly available on GitHub. To download the HTF code, clone ``release/public-v1`` branch and sub-modules of the repository:
```
$ git clone --recurse-submodules -b release/public-v1 https://github.com/clouden90/ufs-HTF-containers.git
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
$ cmake -DRUN_DOCKER_AS_ROOT=ON ..
$ make build_docker_images
```
The ``RUN_DOCKER_AS_ROOT`` flag is mainly for the machine (e.g. NOAACLOUD) that requires root premission to run docker executable. The default is ``OFF``.

### Running Test Cases
After the build step, the testing platform and base docker images should be created. One can list images in local Docker repository to make sure base images are created successfully:
```
$ sudo docker image ls
REPOSITORY                              TAG       IMAGE ID       CREATED         SIZE
ccpp_scm_image                          latest    12907b725dba   4 hours ago     4.66GB
ufs_image                               latest    2fc9a43569fa   4 hours ago     4.89GB
noaaepic/ubuntu20.04-gnu9.3-hpc-stack   v1.2      77e33c658486   7 weeks ago     2.85GB
dtcenter/common-community-container     gnu9      a7c3b249398b   21 months ago   2.74GB
```
Again ``sudo`` is needed when you are in NOAACLOUD platforms. ``ccpp_scm_image`` is for ccpp-scm test cases and ``ufs_image`` is for ufs-wm test cases.

Now you can check the list of default test cases:
```
$ ctest -N
Test project /lustre/ufs-HTF-containers/build
  Test #1: test_ccpp_scm_LASSO_gfsv16
  Test #2: test_ccpp_scm_LASSO_gfsv17p8
  Test #3: test_gfs_v16_atm_Barry
  Test #4: test_gfs_v17p8_atm_Barry
```

Currently the prototype UFS-HTF supports 4 tests as shown in next Table. Users who plan to design/add a new test should refer to later section for details.
Test Number | Test Name  | Test Type/Description | 
------------| -----------------------------|-------------------------------------------------------------------------------|
#1          | test_ccpp_scm_LASSO_gfsv16   | component test / ccpp single column model: LASSO case with gfsv16 physics     |
#2          | test_ccpp_scm_LASSO_gfsv17p8 | component test / ccpp single column model: LASSO case with gfsv17p8 physics   |
#3          | test_gfs_v16_atm_Barry       | integrate test / ufs atm-only model: Hurricane Barry with gfsv16 physics      |
#4          | test_gfs_v17p8_atm_Barry     | integrate test / ufs atm-only model: Hurricane Barry with gfsv17p8 physics    |

The design of these test cases is based on the concept of HSD. Let's consider a scenario:
Developers/Scientists would like to implement a new physic suite for opreational GFS. First we test new development in a relatively simple setup (#1 and #2) for sanity check (e.g. make sure new codes do not crash whole system; check results make sense in single column model case). Then we conduct experitments (#3 and #4) for selected extreme weather events to check the impact of new development. 

The simplest way to run all test cases is run the following command:
```
$ ctest
Test project /lustre/ufs-HTF-containers/build
    Start 1: test_ccpp_scm_LASSO_gfsv16
1/4 Test #1: test_ccpp_scm_LASSO_gfsv16 .......   Passed    2.25 sec
    Start 2: test_ccpp_scm_LASSO_gfsv17p8
2/4 Test #2: test_ccpp_scm_LASSO_gfsv17p8 .....   Passed   10.57 sec
    Start 3: test_gfs_v16_atm_Barry
3/4 Test #3: test_gfs_v16_atm_Barry ...........   Passed  320.72 sec
    Start 4: test_gfs_v17p8_atm_Barry
4/4 Test #3: test_gfs_v17p8_atm_Barry ........... Passed  474.85 sec 

100% tests passed, 0 tests failed out of 4
```
This will run thruogh all 4 test cases sequentially.

The developers can choose to run ccpp-scm test cases only:
```
$ ctest -R test_ccpp_scm*
Test project /lustre/ufs-HTF-containers/build
    Start 1: test_ccpp_scm_LASSO_gfsv16
1/2 Test #1: test_ccpp_scm_LASSO_gfsv16 .......   Passed    2.25 sec
    Start 2: test_ccpp_scm_LASSO_gfsv17p8
2/2 Test #2: test_ccpp_scm_LASSO_gfsv17p8 .....   Passed   10.57 sec

100% tests passed, 0 tests failed out of 2
```

The developes can also choose to run single test case with verbose output from test:
```
$ ctest -V -R test_gfs_v17p8_atm_Barry
$ ctest -R test_gfs_v17p8_atm_Barry
Test project /lustre/ufs-HTF-containers/build
    Start 2: test_ccpp_scm_LASSO_gfsv17p8
1/2 Test #2: test_ccpp_scm_LASSO_gfsv17p8 .....   Passed    4.29 sec
    Start 4: test_gfs_v17p8_atm_Barry
2/2 Test #4: test_gfs_v17p8_atm_Barry .........   Passed  474.85 sec

100% tests passed, 0 tests failed out of 2
```
In the above example, it is interesting to see that although we choose to run ``test_gfs_v17p8_atm_Barry`` only, the test platform actually runs 2 test cases ``test_ccpp_scm_LASSO_gfsv17p8`` and ``test_gfs_v17p8_atm_Barry``. It is because a fixture is set for ``test_gfs_v17p8_atm_Barry`` (see tests/CMakeLists.txt). If ``test_ccpp_scm_LASSO_gfsv17p8`` case fails, ``test_gfs_v17p8_atm_Barry`` will not be executed in this case. One can check [here](https://cmake.org/cmake/help/latest/prop_test/FIXTURES_REQUIRED.html#prop_test:FIXTURES_REQUIRED) for more details.

## Adding new test to UFS-HTF
All the ctesting in UFS-HTF is controlled through ``ufs-HTF-containers/tests/CMakeLists.txt``. A ctest could be either a unit test, or an integration test that executes an application. A unit ctest contains results in a reference log file based on analytical solutions or accurate numerical studies. For application ctest, one could compare to associated reference based on a previous execution of the same test. To determine the pass or failure for a ctest, the actual output could be compared against the reference or obs data. The reader is referred to ``ufs-HTF-containers/tests/CMakeLists.txt``, where several examples exist for both. For example, one can modify ``ufs-HTF-containers/tests/CMakeLists.txt`` to add a ``test_gfs_v17p8_atm_ocn_cice_Barry`` test case by adding the following lines at the end:
```
# global Fully coupled FV3-CCPP-MOM6-CICE-CMEPS system at C96L127 Hurricane Barry
add_test( NAME test_gfs_v17p8_atm_ocn_cice_Barry
    COMMAND ${SUDO_EXECUTABLE} docker run --rm --env test_name=cpld_control_nowave_noaero_p8 --env run_case=test_C96_Barry --workdir /home/builder/ufs-weather-model/tests --shm-size=512m -it ufs_image )

set_tests_properties( test_gfs_v17p8_atm_ocn_cice_Barry
    PROPERTIES                  
    TIMEOUT 1800
    FIXTURES_SETUP test_cpld_v17p8
    FIXTURES_REQUIRED "test_atm_v17p8"
    DEPENDS "test_gfs_v17p8_atm_Barry"
    )
```

This will add ``test_gfs_v17p8_atm_ocn_cice_Barry`` test case. Then you will have to do ``cmake`` again under your <build-directory> folder. Then you will find a new test has been added in the test set:
```
$ ctest -N
Test project /lustre/ufs-HTF-containers/build
  Test #1: test_ccpp_scm_LASSO_gfsv16
  Test #2: test_ccpp_scm_LASSO_gfsv17p8
  Test #3: test_gfs_v16_atm_Barry
  Test #4: test_gfs_v17p8_atm_Barry
  Test #5: test_gfs_v17p8_atm_ocn_cice_Barry
```
Keep in mind the developer will have to prepare the associated input files such as model ICs for ocn and cice models. In this prototype, we provide those ICs within ufs-wm docker image (check ``ufs-HTF-containers/docker/Dockerfile.ufs-wm``).

No we can run the new test using the following command:
```
$ ctest -R test_gfs_v17p8_atm_ocn_cice_Barry -FA .
```
The ``-FA .`` flag is to exclude fixtures matching so we can run new test case only (assuming all other tests passed).
