# test build ufs-wm container
add_test( NAME test_build_ufs_container
    COMMAND docker build --no-cache --compress --file ./tests/ci/Dockerfile --tag my_image . )

set_tests_properties( test_build_ufs_container
    PROPERTIES                  
    FIXTURES_SETUP test_build1
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/../ufs-weather-model
    )

# test build data image
add_test( NAME test_build_data_image
    COMMAND docker build --build-arg test_path=test_C96_Barry --file Dockerfile --tag ufs-case-study:Barry . )

set_tests_properties( test_build_data_image
    PROPERTIES                  
    FIXTURES_SETUP test_build2
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/../docker
    )

# test build data volume
add_test( NAME test_build_data_volume
    COMMAND docker run --detach --volume DataVolume:/tmp ufs-case-study:Barry )

set_tests_properties( test_build_data_volume
    PROPERTIES                  
    FIXTURES_SETUP test_build3
    FIXTURES_REQUIRED test_build2
    )
