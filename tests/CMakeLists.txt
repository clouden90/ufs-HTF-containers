# test build ufs-wm container
add_test( NAME test_build_ufs_container
    COMMAND docker build --build-arg run_case=test_C96_Barry --no-cache --compress --file ./docker/Dockerfile --tag my_image . )

set_tests_properties( test_build_ufs_container
    PROPERTIES                  
    FIXTURES_SETUP test_build
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/../
    )

# run global control test: GFSv16 atmosphere only at C96L127 Hurricane Barry
add_test( NAME test_gfs_v16_atm_Barry
    COMMAND docker run --rm --env test_name=control --env run_case=test_C96_Barry --workdir /home/builder/ufs-weather-model/tests --shm-size=512m -it my_image )

set_tests_properties( test_gfs_v16_atm_Barry
    PROPERTIES                  
    TIMEOUT 3600
    FIXTURES_SETUP test_atm
    FIXTURES_REQUIRED test_build
    DEPENDS "test_build_ufs_container"
    )
