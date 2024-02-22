cmake_minimum_required(VERSION 3.5)
project(gtest-ci-test)

enable_testing()

find_package(GTest REQUIRED)

if (CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    add_definitions("/source-charset:utf-8")
    set(CMAKE_CXX_FLAGS_DEBUG "/MTd")
    set(CMAKE_CXX_FLAGS_RELEASE "/MT")
elseif (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    # enable static analysis for gcc
    add_compile_options(-fanalyzer -Werror)
    # disable stuff that gets triggered by googletest
    add_compile_options(-Wno-analyzer-malloc-leak)
endif ()

add_executable(test_everything)

target_include_directories(test_everything
        PRIVATE
        ${GTEST_INCLUDE_DIRS}
)

target_link_libraries(test_everything
        ${GTEST_BOTH_LIBRARIES}
)

add_test(
        NAME test_everything
        COMMAND test_everything
)

file(GLOB ITEMS *)
foreach(item ${ITEMS})
    if(IS_DIRECTORY ${item} AND EXISTS ${item}/CMakeLists.txt)
        message(${item})
        add_subdirectory(${item})
    endif()
endforeach()
