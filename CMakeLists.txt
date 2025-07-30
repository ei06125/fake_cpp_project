cmake_minimum_required(VERSION 3.22)

# ≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡
# Project Settings
# ≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡≡
project(fake_cpp_project VERSION 1.0.0.0 LANGUAGES CXX)
set(CMAKE_CXX_EXTENSIONS OFF)
set(DEBUG_POSTFIX d)
option(BUILD_SHARED_LIBS "Build using shared libraries" ON)

# ===========================================================================
# TARGET: fake_cpp_lib
# ===========================================================================

# ---------------------------------------------------------------------------
# BUILD
# ---------------------------------------------------------------------------
add_library(fake_cpp_lib)

file(GLOB_RECURSE fake_cpp_lib_SOURCES "src/*.[hc]pp")

file(GLOB_RECURSE fake_cpp_lib_HEADERS "include/*.hpp")

target_sources(fake_cpp_lib PRIVATE ${fake_cpp_lib_SOURCES}
                                    ${fake_cpp_lib_HEADERS})

target_include_directories(
  fake_cpp_lib PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
                      $<INSTALL_INTERFACE:include>)

target_compile_features(fake_cpp_lib PUBLIC cxx_std_23)

set_target_properties(fake_cpp_lib PROPERTIES POSITION_INDEPENDENT_CODE
                                              ${BUILD_SHARED_LIBS})

add_library(FAKE::fake_cpp_lib ALIAS fake_cpp_lib)

# ---------------------------------------------------------------------------
# INSTALL
# ---------------------------------------------------------------------------
install(
  TARGETS fake_cpp_lib
  DESTINATION lib
  EXPORT fake_cpp_libTargets)

install(FILES include/fake/fake.hpp DESTINATION include)

install(
  EXPORT fake_cpp_libTargets
  FILE fake_cpp_libTargets.cmake
  DESTINATION lib/cmake/fake_cpp_lib)

# ---------------------------------------------------------------------------
# PACKAGING
# ---------------------------------------------------------------------------
include(InstallRequiredSystemLibraries)
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/License.txt")
set(CPACK_PACKAGE_VERSION_MAJOR "${fake_cpp_project_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${fake_cpp_project_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${fake_cpp_project_VERSION_PATCH}")
set(CPACK_PACKAGE_VERSION "${fake_cpp_project_VERSION_MAJOR}.${fake_cpp_project_VERSION_MINOR}.${fake_cpp_project_VERSION_PATCH}")
set(CPACK_PACKAGE_VENDOR "ei06125")
include(CPack)

include(CMakePackageConfigHelpers)
# generate the config file that is includes the exports
configure_package_config_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/Config.cmake.in
  "${CMAKE_CURRENT_BINARY_DIR}/fake_cpp_libConfig.cmake"
  INSTALL_DESTINATION "lib/cmake/fake_cpp_lib"
  NO_SET_AND_CHECK_MACRO NO_CHECK_REQUIRED_COMPONENTS_MACRO)

write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/fake_cpp_libConfigVersion.cmake"
  VERSION
    "${fake_cpp_libTargets_VERSION_MAJOR}.${fake_cpp_libTargets_VERSION_MINOR}${fake_cpp_libTargets_VERSION_PATCH}"
  COMPATIBILITY
    AnyNewerVersion # <AnyNewerVersion|SameMajorVersion|SameMinorVersion|ExactVersion>
)

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/fake_cpp_libConfig.cmake
              ${CMAKE_CURRENT_BINARY_DIR}/fake_cpp_libConfigVersion.cmake
        DESTINATION lib/cmake/fake_cpp_lib)

export(EXPORT fake_cpp_libTargets
       FILE "${CMAKE_CURRENT_BINARY_DIR}/fake_cpp_libTargets.cmake")

# ===========================================================================
# SPECIAL TARGETS
# ===========================================================================
set(CUSTOM_INSTALL_DIR "$ENV{HOME}/.local/ei06125/fake")

add_custom_target(
  run_cmake_install COMMAND ${CMAKE_COMMAND} --install ${CMAKE_BINARY_DIR}
                            --prefix ${CUSTOM_INSTALL_DIR})
add_dependencies(run_cmake_install fake_cpp_lib)

if(MSVC)
  set(CUSTOM_CPACK_GENERATOR "ZIP")
elseif(APPLE)
  set(CUSTOM_CPACK_GENERATOR "TGZ")
  else()
  set(CUSTOM_CPACK_GENERATOR "TGZ")
endif()
add_custom_target(run_cmake_pack COMMAND cpack -G ${CUSTOM_CPACK_GENERATOR} -B
                                         ${CMAKE_CURRENT_SOURCE_DIR}/packages)
add_dependencies(run_cmake_pack fake_cpp_lib)

add_custom_target(clean_custom_install_dir COMMAND rm -rf ${CUSTOM_INSTALL_DIR})
