cmake_minimum_required(VERSION 3.22)
project(fake_cpp_project)

add_library(fake_cpp_lib ${BUILD_SHARED_LIBS}) # NOLINT
file(GLOB_RECURSE fake_cpp_lib_SOURCES "src/*.[hc]pp")
file(GLOB_RECURSE fake_cpp_lib_HEADERS "include/*.hpp")
target_sources(fake_cpp_lib PRIVATE ${fake_cpp_lib_SOURCES}
                                    ${fake_cpp_lib_HEADERS})
target_include_directories(
  fake_cpp_lib PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
                      $<INSTALL_INTERFACE:include>)
target_compile_features(fake_cpp_lib PUBLIC cxx_std_23)
add_library(FAKE::fake_cpp_lib ALIAS fake_cpp_lib)
