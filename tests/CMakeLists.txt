# test build ccpp-scm container
add_test( NAME test_build_ccpp_scm_container
    COMMAND ${SUDO_EXECUTABLE} docker build --file ./docker/Dockerfile.ccpp-scm --tag ccpp_scm_image . )

set_tests_properties( test_build_ccpp_scm_container
    PROPERTIES
    FIXTURES_SETUP test_build            
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/../
    )

# test build ufs-wm container
add_test( NAME test_build_ufs_container
    COMMAND ${SUDO_EXECUTABLE} docker build --build-arg run_case=test_C96_Barry --file ./docker/Dockerfile.ufs-wm --tag ufs_image . )

set_tests_properties( test_build_ufs_container
    PROPERTIES
    FIXTURES_SETUP test_build   
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/../
    )

# ccpp-scm component test: -case LASSO_2016051812 --suite SCM_GFS_v16 --namelist input_GFS_v16.nml
add_test( NAME test_ccpp_scm_LASSO_gfsv16
    COMMAND ${SUDO_EXECUTABLE} docker run --rm --env case_name=LASSO_2016051812 --env suite_name=SCM_GFS_v16 --env suite_namelist=input_GFS_v16.nml -it ccpp_scm_image )

set_tests_properties( test_ccpp_scm_LASSO_gfsv16
    PROPERTIES                 
    FIXTURES_SETUP test_com
    DEPENDS "test_build_ccpp_scm_container"
    )

# ccpp-scm component test: -case LASSO_2016051812 --suite SCM_GFS_v17_p8 --namelist input_GFS_v17_p8.nml
add_test( NAME test_ccpp_scm_LASSO_gfsv17p8
    COMMAND ${SUDO_EXECUTABLE} docker run --rm --env case_name=LASSO_2016051812 --env suite_name=SCM_GFS_v17_p8 --env suite_namelist=input_GFS_v17_p8.nml -it ccpp_scm_image )

set_tests_properties( test_ccpp_scm_LASSO_gfsv17p8
    PROPERTIES                  
    FIXTURES_SETUP test_com
    DEPENDS "test_build_ccpp_scm_container"
    )

# global control test: GFSv16 atmosphere only at C96L127 Hurricane Barry
add_test( NAME test_gfs_v16_atm_Barry
    COMMAND ${SUDO_EXECUTABLE} docker run --rm --env test_name=control --env run_case=test_C96_Barry --workdir /home/builder/ufs-weather-model/tests --shm-size=512m -it ufs_image )

set_tests_properties( test_gfs_v16_atm_Barry
    PROPERTIES                  
    TIMEOUT 1800
    FIXTURES_SETUP test_int_atm
    FIXTURES_REQUIRED "test_com"
    DEPENDS "test_build_ufs_container;test_ccpp_scm_LASSO_gfsv16"
    )

# global control test: GFSv17p8 atmosphere only at C96L127 Hurricane Barry
add_test( NAME test_gfs_v17p8_atm_Barry
    COMMAND ${SUDO_EXECUTABLE} docker run --rm --env test_name=control_p8 --env run_case=test_C96_Barry --workdir /home/builder/ufs-weather-model/tests --shm-size=512m -it ufs_image )

set_tests_properties( test_gfs_v17p8_atm_Barry
    PROPERTIES                  
    TIMEOUT 1800
    FIXTURES_SETUP test_int_atm
    FIXTURES_REQUIRED "test_com"
    DEPENDS "test_build_ufs_container;test_ccpp_scm_LASSO_gfsv17p8"
    )
