cmake_minimum_required(VERSION 3.19)

project(ufs-htf
        VERSION 1.0)

#
enable_testing()
add_subdirectory(tests)

# This always needs to come last.
include(CTest)
