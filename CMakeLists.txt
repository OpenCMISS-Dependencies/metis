CMAKE_MINIMUM_REQUIRED(VERSION 3.0)
#cmake_policy(SET CMP0048 NEW) # Allows VERSION in project definitions

project(METIS VERSION 4.0.3 LANGUAGES C)

option(BUILD_TESTS "${PROJECT_NAME} - Build tests" ON)

# Find sources.
file(GLOB metis_sources Lib/*.c)

# Build libmetis.
add_library(metis STATIC ${metis_sources})
target_include_directories(metis 
    PUBLIC $<BUILD_INTERFACE: Lib>)
set_target_properties(metis PROPERTIES OUTPUT_NAME metis-${METIS_VERSION})

if(UNIX)
  target_link_libraries(metis PUBLIC m)
endif()

install(TARGETS metis 
    EXPORT metis-config
    DESTINATION lib
    INCLUDES DESTINATION include/metis-${METIS_VERSION})
install(DIRECTORY Lib/ DESTINATION include/metis-${METIS_VERSION}
    FILES_MATCHING PATTERN "*.h")   
install(EXPORT metis-config DESTINATION lib/cmake/metis-${METIS_VERSION})
include(CMakePackageConfigHelpers)
WRITE_BASIC_PACKAGE_VERSION_FILE(${CMAKE_CURRENT_BINARY_DIR}/metis-config-version.cmake
    VERSION ${METIS_VERSION} COMPATIBILITY SameMajorVersion)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/metis-config-version.cmake
    DESTINATION lib/cmake/metis-${METIS_VERSION})

# Tests
if (BUILD_TESTS)
    enable_testing()
    add_executable(metistest Test/mtest.c Programs/io.c)
    target_link_libraries(metistest PRIVATE metis)
    add_test(metistest-4graph metistest ${METIS_SOURCE_DIR}/Graphs/4elt.graph) 
    add_test(metistest-mgraph metistest ${METIS_SOURCE_DIR}/Graphs/test.mgraph)
endif()