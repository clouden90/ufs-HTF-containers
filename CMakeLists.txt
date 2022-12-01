cmake_minimum_required(VERSION 3.19)

project(ufs-htf
        VERSION 1.0)

#
set(BUILD_DOCKER_IMAGES            ON  CACHE BOOL "Build ufs-htf docker images")
set(RUN_DOCKER_AS_ROOT             OFF CACHE BOOL "Enable run docker as root")

#
message("")
message("Build ufs-htf docker images ............ ${BUILD_DOCKER_IMAGES}")
message("Enable run docker as root .............. ${RUN_DOCKER_AS_ROOT}")
message("")

#
if(RUN_DOCKER_AS_ROOT)
  set(SUDO_EXECUTABLE "sudo")
endif()

#
if(BUILD_DOCKER_IMAGES)
  find_program(Docker_EXECUTABLE docker)
  if(NOT Docker_EXECUTABLE)
      message(FATAL_ERROR "Cannot find the docker executable!")
  endif()
  add_custom_target(build_docker_images
  COMMAND echo "Build ufs-wm and ccpp-scm docker images!!!" 
  COMMAND ${SUDO_EXECUTABLE} ${Docker_EXECUTABLE} build --build-arg run_case=test_C96_Barry --file ${CMAKE_CURRENT_SOURCE_DIR}/docker/Dockerfile.ufs-wm 
      --tag ufs_image ${CMAKE_CURRENT_SOURCE_DIR}
#  COMMAND ${SUDO_EXECUTABLE} ${Docker_EXECUTABLE} build --file ${CMAKE_CURRENT_SOURCE_DIR}/docker/Dockerfile.ccpp-scm 
#      --tag ccpp_scm_image ${CMAKE_CURRENT_SOURCE_DIR} 
  COMMAND ${SUDO_EXECUTABLE} ${Docker_EXECUTABLE} pull dtcenter/ccpp-scm:v6.0.0 
  )
endif()

#
enable_testing()
add_subdirectory(tests)

# This always needs to come last.
include(CTest)
