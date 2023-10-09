cmake_minimum_required(VERSION 3.25)

# Specify the C++ standard
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# Define the project
project(Flare VERSION 0.1.0)

# Add the source files to the project
file(GLOB_RECURSE FLARE_SOURCES "src/*.cpp")
add_executable(Flare ${FLARE_SOURCES})
target_include_directories(Flare PUBLIC "${PROJECT_SOURCE_DIR}/src")

# Configure a header file to pass some of the CMake settings
# to the source code
configure_file(CMakeProjectConfig.h.in CMakeProjectConfig.h)

# Set output directories for the project
set_target_properties(Flare PROPERTIES 
    RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/${PROJECT_NAME}/
)

# Check for platform and set compiler definitions
if(WIN32)
    if(NOT MSVC)
        message(FATAL_ERROR "Unsupported compiler! Please use MSVC")
    endif()
    message(STATUS "Building for Windows")
    set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>DLL")
    target_compile_definitions(Flare PUBLIC FL_PLATFORM_WINDOWS)
elseif(UNIX AND NOT APPLE)
    message(STATUS "Building for Linux")
    target_compile_definitions(Flare PUBLIC FL_PLATFORM_LINUX)
else()
    message(FATAL_ERROR "Unsupported platform! Currently only Windows and Linux are supported")
endif()

# Check for build type and set compiler definitions
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    message(STATUS "Building in Debug mode")
    target_compile_definitions(Flare PUBLIC FL_DEBUG)
elseif(CMAKE_BUILD_TYPE STREQUAL "Release")
    message(STATUS "Building in Release mode")
    target_compile_definitions(Flare PUBLIC FL_RELEASE)
else()
    message(FATAL_ERROR "Unsupported build type! Please use either Debug or Release")
endif()
