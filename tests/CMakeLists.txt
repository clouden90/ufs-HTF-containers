# create folder for ccpp-scm output
file(MAKE_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/output)

# files for vrfy
set( comp_files
  ${CMAKE_CURRENT_SOURCE_DIR}/comp/scm_read_obs.py
  ${CMAKE_CURRENT_SOURCE_DIR}/comp/scm_plotting_routines.py
  ${CMAKE_CURRENT_SOURCE_DIR}/comp/scm_analysis.py
  ${CMAKE_CURRENT_SOURCE_DIR}/comp/forcing_file_common.py
  ${CMAKE_CURRENT_SOURCE_DIR}/comp/configspec.ini
  ${CMAKE_CURRENT_SOURCE_DIR}/comp/twp180iopsndgvarana_v2.1_C3.c1.20060117.000000.cdf
  ${CMAKE_CURRENT_SOURCE_DIR}/comp/twpice_comp.ini
)

# copy multirun_twpice_supported_suites.py to tests/output
execute_process( COMMAND ${CMAKE_COMMAND} -E copy
      ${CMAKE_CURRENT_SOURCE_DIR}/multirun_twpice_supported_suites.py
      ${CMAKE_CURRENT_BINARY_DIR}/output/multirun_twpice_supported_suites.py )

# copy vrfy scripts
file(MAKE_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/output/comp) 
foreach(FILENAME ${comp_files})
     get_filename_component(filename ${FILENAME} NAME )
     execute_process( COMMAND ${CMAKE_COMMAND} -E copy
         ${FILENAME}
         ${CMAKE_CURRENT_BINARY_DIR}/output/comp/${filename} )
endforeach()

# ccpp-scm twpice tests
add_test( NAME test_ccpp_scm_twpice
    COMMAND ${SUDO_EXECUTABLE} ${Docker_EXECUTABLE} run --user root --rm -v ${CMAKE_CURRENT_BINARY_DIR}/output:/home --name run-ccpp-scm  -it dtcenter/ccpp-scm:v6.0.0 /bin/bash -c "./run_scm.py --multirun --file /home/multirun_twpice_supported_suites.py -d" )

set_tests_properties( test_ccpp_scm_twpice PROPERTIES FIXTURES_SETUP test_unit)

# ccpp-scm twpice check
add_test( NAME check_ccpp_scm_twpice
    COMMAND ${SUDO_EXECUTABLE} ${Docker_EXECUTABLE} run --user root --rm -v ${CMAKE_CURRENT_BINARY_DIR}/output:/home --name run-ccpp-scm  -it dtcenter/ccpp-scm:v6.0.0 /bin/bash -c "cd /home/comp && ./scm_analysis.py twpice_comp.ini -d" )

set_tests_properties( check_ccpp_scm_twpice
    PROPERTIES                  
    TIMEOUT 1800
    FIXTURES_SETUP test_check
    FIXTURES_REQUIRED test_unit
    DEPENDS test_unit
    )


# global control test: GFSv16 atmosphere only at C96L127 Hurricane Barry
add_test( NAME test_gfs_v16_atm_Barry
    COMMAND ${SUDO_EXECUTABLE} docker run --rm --env test_name=control --env run_case=test_C96_Barry --workdir /home/builder/ufs-weather-model/tests --shm-size=512m -it ufs_image )

set_tests_properties( test_gfs_v16_atm_Barry
    PROPERTIES                  
    TIMEOUT 1800
    FIXTURES_SETUP test_atm
    FIXTURES_REQUIRED test_check
    )

# global control test: GFSv17p8 atmosphere only at C96L127 Hurricane Barry
add_test( NAME test_gfs_v17p8_atm_Barry
    COMMAND ${SUDO_EXECUTABLE} docker run --rm --env test_name=control_p8 --env run_case=test_C96_Barry --workdir /home/builder/ufs-weather-model/tests --shm-size=512m -it ufs_image )

set_tests_properties( test_gfs_v17p8_atm_Barry
    PROPERTIES                  
    TIMEOUT 1800
    FIXTURES_SETUP test_atm
    FIXTURES_REQUIRED test_check
    DEPENDS test_gfs_v16_atm_Barry
    )
