cmake_minimum_required(VERSION 3.19)

project(ufs-htf
        VERSION 1.0)

#
set(RUN_DOCKER_AS_ROOT             OFF CACHE BOOL "Enable run docker as root")

#
message("")
message("Enable run docker as root .............. ${RUN_DOCKER_AS_ROOT}")
message("")

#
if(RUN_DOCKER_AS_ROOT)
  set(SUDO_EXECUTABLE "sudo")
endif()

#
enable_testing()
add_subdirectory(tests)

# This always needs to come last.
include(CTest)
