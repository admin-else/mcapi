cmake_minimum_required(VERSION 3.15)
project(mcapi_project C)

set(CMAKE_C_STANDARD 23)
set(CMAKE_C_STANDARD_REQUIRED TRUE)

option(BUILD_SHARED_LIBS "Build shared libraries" ON)

find_package(CURL REQUIRED)

add_library(mcapi src/mcapi.c)

target_link_libraries(mcapi PRIVATE CURL::libcurl)
target_include_directories(mcapi PUBLIC include)

if(MSVC)
    target_compile_options(mcapi PRIVATE /W4)
else()
    target_compile_options(mcapi PRIVATE -Wall -Wextra -Werror=switch)
endif()

if(NOT PROJECT_IS_TOP_LEVEL)
    return()
endif()

include(CTest)

if(BUILD_TESTING)
    add_executable(info_test tests/info.c)
    target_link_libraries(info_test PRIVATE mcapi)
    add_test(NAME info COMMAND info_test)
endif()

include(GNUInstallDirs)

install(TARGETS mcapi)
install(DIRECTORY include/ TYPE INCLUDE)
