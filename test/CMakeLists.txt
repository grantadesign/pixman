FILE(GLOB PIXMAN_TEST_SRCS "*.c")

list(REMOVE_ITEM PIXMAN_TEST_SRCS ${CMAKE_CURRENT_SOURCE_DIR}/utils.c)

include_directories(../pixman
                    ${GATEWAYDEPS_SOURCE_DIR}/pthreads)

add_library(pixman_test_utils STATIC utils.c)
SET_PROPERTY(TARGET pixman_test_utils PROPERTY FOLDER "pixman/tests")

foreach(test_src ${PIXMAN_TEST_SRCS})
    
    file(RELATIVE_PATH pixman_test_name ${CMAKE_CURRENT_SOURCE_DIR} ${test_src})
    
    string(REPLACE ".c" "" pixman_test_name ${pixman_test_name})
    
    message("pixman test: ${pixman_test_name}")
     
    add_executable(${pixman_test_name} ${test_src})
    target_link_libraries(${pixman_test_name} pixman pixman_test_utils pthreadsVC2)
    
    if (NOT ${pixman_test_name} STREQUAL "lowlevel-blt-bench") #This takes bloody ages since it's a benchmark, so we don't want it to run every build.
        add_test(${pixman_test_name} ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${pixman_test_name})
    endif()
    
    SET_PROPERTY(TARGET ${pixman_test_name} PROPERTY FOLDER "pixman/tests")
    
endforeach()
