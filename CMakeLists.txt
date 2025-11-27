cmake_minimum_required(VERSION 3.18)

# project(rte-rrtmgp)
project(rte-rrtmgp LANGUAGES C CXX Fortran)

# include(FetchContent)

# FetchContent_Declare(
#     rte_rrtmgp
#     GIT_REPOSITORY https://github.com/earth-system-radiation/rte-rrtmgp.git
#     GIT_TAG main
# )
# FetchContent_MakeAvailable(rte_rrtmgp)

set(CMAKE_POSITION_INDEPENDENT_CODE ON)

add_subdirectory(rte-rrtmgp)

# find_package(rte-rrtmgp REQUIRED)

# Compile Python bindings with pybind11
find_package(pybind11 REQUIRED)

find_package(Python REQUIRED COMPONENTS Development.Module)

set(TARGET_NAME pyrte_rrtmgp)
set(OUTPUT_DIRECTORY pyrte_rrtmgp)
set(SOURCES ${CMAKE_SOURCE_DIR}/pybind_interface.cpp)

pybind11_add_module(${TARGET_NAME} ${SOURCES})

# target_include_directories(${TARGET_NAME} PUBLIC
#     $ENV{CONDA_PREFIX}/include
# )
target_link_libraries(${TARGET_NAME} PUBLIC
    rte-rrtmgp::rte
    rte-rrtmgp::rrtmgp
)

target_include_directories(${TARGET_NAME} PRIVATE
    ${CMAKE_CURRENT_BINARY_DIR}/headers
    ${CMAKE_SOURCE_DIR}/rte-rrtmgp
    ${CMAKE_SOURCE_DIR}/rte-rrtmgp/include
)

# target_link_libraries(${TARGET_NAME} PUBLIC
#     rte-rrtmgp::rte
#     rte-rrtmgp::rrtmgp
# )

target_compile_definitions(${TARGET_NAME} PRIVATE
    VERSION_INFO=${VERSION_INFO}
    DBL_EPSILON=5.8e-2 #TODO: Remove when rte_types.h updates
    DCMAKE_LIBRARY_OUTPUT_DIRECTORY=${OUTPUT_DIRECTORY}
)

# The install directory is the output (wheel) directory
install(TARGETS ${TARGET_NAME} DESTINATION ${OUTPUT_DIRECTORY})
