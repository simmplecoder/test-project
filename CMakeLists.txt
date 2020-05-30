cmake_minimum_required(VERSION 3.15)
project(use-flash)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_CXX_STANDARD 17)

option(use_conan "Use Conan to install dependencies")

if (USE_CONAN)
  # Download automatically, you can also just copy the conan.cmake file
  if(NOT EXISTS "${CMAKE_BINARY_DIR}/conan.cmake")
    message(STATUS "blazing-gil: Downloading conan.cmake from https://github.com/conan-io/cmake-conan")
    file(DOWNLOAD "https://raw.githubusercontent.com/conan-io/cmake-conan/v0.15/conan.cmake"
      "${CMAKE_BINARY_DIR}/conan.cmake")
  endif()

  # NOTE: See RelWithDebInfo for Release builds, http://docs.conan.io/en/latest/howtos/vs2017_cmake.html
  set(_build_type_saved ${CMAKE_BUILD_TYPE})
  if(CMAKE_BUILD_TYPE STREQUAL "MinSizeRel" OR CMAKE_BUILD_TYPE STREQUAL "RelWithDebInfo")
    set(CMAKE_BUILD_TYPE "Release")
  endif()

  include(${CMAKE_BINARY_DIR}/conan.cmake)
  conan_cmake_run(CONANFILE conanfile.txt BASIC_SETUP CMAKE_TARGETS BUILD missing)

  set(CMAKE_BUILD_TYPE ${_build_type_saved})
  unset(_build_type_saved)
endif()

find_package(blazing-gil REQUIRED)


### dependencies for examples
add_executable(padding padding.cpp)
target_link_libraries(padding blazing-gil::blazing-gil)

get_target_property(blazing_gil_include blazing-gil::blazing-gil INTERFACE_INCLUDE_DIRECTORIES)
message(STATUS "Include directories of blazing-gil are set to ${blazing_gil_include}")
