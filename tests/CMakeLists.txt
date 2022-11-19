add_test( NAME test_build_docker_image
    COMMAND docker build --no-cache --compress --file ./tests/ci/Dockerfile --tag my_image . )

set_tests_properties( test_build_docker_image
    PROPERTIES                  
    FIXTURES_SETUP test_build
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/../ufs-weather-model
    )
