cmake_minimum_required(VERSION 3.22)
project(fake_cpp_project)

set(CMAKE_CXX_EXTENSIONS OFF)

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

install(TARGETS fake_cpp_lib
  EXPORT fake_cpp_libTargets
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
  INCLUDES DESTINATION include)

install(DIRECTORY include/ DESTINATION include)

install(EXPORT fake_cpp_libTargets
  FILE fake_cpp_libTargets.cmake
  NAMESPACE FAKE::
  DESTINATION lib/cmake/fake_cpp_lib)