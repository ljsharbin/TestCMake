cmake_minimum_required(VERSION 3.22...3.25)

include(cmake/obs-version.cmake)

add_library(libobs SHARED)
add_library(OBS::libobs ALIAS libobs)

target_sources(
  libobs
  PRIVATE
  common.h
  function.h
  function.c)

target_compile_features(libobs PUBLIC cxx_std_17)

target_link_libraries(
  libobs
  PRIVATE
  OBS::libobs-version)

# os-windows.cmake
set_target_properties(libobs PROPERTIES PREFIX "" OUTPUT_NAME "obs")

configure_file(obsconfig.h.in "${CMAKE_BINARY_DIR}/config/obsconfig.h" @ONLY)

target_include_directories(libobs PUBLIC "$<BUILD_INTERFACE:${CMAKE_BINARY_DIR}/config>"
                                         "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>")

target_compile_definitions(libobs PUBLIC HAVE_OBSCONFIG_H)

set(public_headers
  common.h
  function.h)

# cmake-format: off
set_property(TARGET libobs APPEND PROPERTY OBS_PUBLIC_HEADERS ${public_headers})

set_target_properties_obs(
  libobs
  PROPERTIES FOLDER core
             VERSION 0
             SOVERSION "${OBS_VERSION_MAJOR}")
# cmake-format: on

target_export(libobs)
