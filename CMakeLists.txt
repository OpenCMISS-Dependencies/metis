CMAKE_MINIMUM_REQUIRED(VERSION 3.0)
cmake_policy(SET CMP0048 NEW) # Allows VERSION in project definitions

project(METIS VERSION 4.0.3 LANGUAGES C)

if (OPENCMISS_DEPENDENCIES_LIBRARIES)
    SET(CMAKE_PREFIX_PATH ${OPENCMISS_DEPENDENCIES_CONFIGS_DIR} ${CMAKE_PREFIX_PATH})
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${OPENCMISS_DEPENDENCIES_LIBRARIES})
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${OPENCMISS_DEPENDENCIES_LIBRARIES})
else()
    SET(CMAKE_PREFIX_PATH ../cmake ${CMAKE_PREFIX_PATH})
    SET(OPENCMISS_DEPENDENCIES_CONFIGS_DIR ${CMAKE_CURRENT_BINARY_DIR})
endif()

# Find sources.
file(GLOB metis_sources Lib/*.c)

# Build libmetis.
add_library(metis STATIC ${metis_sources})
target_include_directories(metis PUBLIC Lib)
set_target_properties(metis PROPERTIES OUTPUT_NAME metis-${METIS_VERSION})

if(UNIX)
  target_link_libraries(metis m)
endif()
export(TARGETS metis FILE ${OPENCMISS_DEPENDENCIES_CONFIGS_DIR}/metis-${METIS_VERSION}/metis-config.cmake)
include(CMakePackageConfigHelpers)
WRITE_BASIC_PACKAGE_VERSION_FILE(${OPENCMISS_DEPENDENCIES_CONFIGS_DIR}/metis-${METIS_VERSION}/metis-config-version.cmake
    VERSION ${METIS_VERSION} COMPATIBILITY SameMajorVersion)

# Tests
enable_testing()
add_executable(metistest Test/mtest.c Programs/io.c)
target_link_libraries(metistest metis)
add_test(metistest-4graph metistest ${METIS_SOURCE_DIR}/Graphs/4elt.graph) 
add_test(metistest-mgraph metistest ${METIS_SOURCE_DIR}/Graphs/test.mgraph)