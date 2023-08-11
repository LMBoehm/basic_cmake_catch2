# 3.24.1 required to fix warning for gtest download
cmake_minimum_required(VERSION 3.24.1)
project(toy_project)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY
    ${CMAKE_BINARY_DIR}/bin
    CACHE INTERNAL "")

string(
    CONCAT DEFAULT_BUILD_FLAGS
         "-g "
         # do not ignore warnings
         "-Werror "
         # enable more warnings
         "-Wall "
         "-Wextra "
         "-Wshadow "
         "-Wconversion "
         "-Wpedantic ")

# default to release build
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE
      Release
      CACHE INTERNAL "")
endif()

if(${CMAKE_BUILD_TYPE} STREQUAL "Release")
  add_definitions(-DNDEBUG)
  set_property(
    DIRECTORY ${CMAKE_SOURCE_DIR}
    APPEND
    PROPERTY COMPILE_DEFINITIONS NDEBUG)
endif()

set(CMAKE_CXX_FLAGS
    ${DEFAULT_BUILD_FLAGS}
    CACHE INTERNAL "")
set(CMAKE_CXX_FLAGS_RELEASE
    "-O3"
    CACHE INTERNAL "")
set(CMAKE_CXX_FLAGS_DEBUG
    "-O1 -fsanitize=address -fsanitize=leak -fsanitize=undefined -fno-omit-frame-pointer "
    CACHE INTERNAL "")

message("BUILD TYPE: ${CMAKE_BUILD_TYPE}")
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

Include(FetchContent)

FetchContent_Declare(
  Catch2
  GIT_REPOSITORY https://github.com/catchorg/Catch2.git
  GIT_TAG        v3.4.0 # or a later release.... this version is required to work with gcc13
)

FetchContent_MakeAvailable(Catch2)
list(APPEND CMAKE_MODULE_PATH ${catch2_SOURCE_DIR}/extras)
# lets trust Catch2 and disable some warnings such that it compiles
target_compile_options(Catch2 PRIVATE -Wno-conversion -Wno-maybe-uninitialized)


include(CTest)
include(Catch)


include_directories(src)
add_subdirectory(src)
