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

# INSTALLATION
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

# PACKAGING
include(InstallRequiredSystemLibraries)
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/License.txt")
set(CPACK_PACKAGE_VERSION_MAJOR "${fake_cpp_project_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${fake_cpp_project_VERSION_MINOR}")
include(CPack)

include(CMakePackageConfigHelpers)
# generate the config file that is includes the exports
configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/Config.cmake.in
  "${CMAKE_CURRENT_BINARY_DIR}/fake_cpp_libTargets.cmake"
  INSTALL_DESTINATION "lib/cmake/fake_cpp_lib"
  NO_SET_AND_CHECK_MACRO
  NO_CHECK_REQUIRED_COMPONENTS_MACRO
  )

  write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/fake_cpp_libTargetsConfigVersion.cmake"
  VERSION "${fake_cpp_libTargets_VERSION_MAJOR}.${fake_cpp_libTargets_VERSION_MINOR}"
  COMPATIBILITY ExactVersion # <AnyNewerVersion|SameMajorVersion|SameMinorVersion|ExactVersion>
)
install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/fake_cpp_libConfig.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/fake_cpp_libConfigVersion.cmake
  DESTINATION lib/cmake/fake_cpp_lib
  )
  export(EXPORT fake_cpp_libTargets
  FILE "${CMAKE_CURRENT_BINARY_DIR}/fake_cpp_libTargets.cmake"
)