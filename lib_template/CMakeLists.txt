# Library project CMakeLists.txt template

cmake_minimum_required(VERSION 2.8.9)

# EDIT
project(LIBTURD CXX)

set(TARGET turd)

add_subdirectory(src)

option(BUILD_SHARED_LIBS "build shared libraries" ON)
add_library(${TARGET} ${SOURCES})
# EDIT
set(VERSION_MAJOR 0)
set(VERSION_MINOR 0)
set(VERSION_PATCH 0)

string(TOLOWER ${PROJECT_NAME} project_name)
set(EXPORT_TARGET_SET ${project_name}-targets)

##############################################################################
# Headers

set(INCLUDE_DIR include)
install(DIRECTORY ${INCLUDE_DIR} DESTINATION .)
include_directories(${INCLUDE_DIR})


set_target_properties(${TARGET} PROPERTIES
  VERSION ${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}
  SOVERSION ${VERSION_MAJOR})

install(TARGETS ${TARGET} EXPORT ${EXPORT_TARGET_SET}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin)

##############################################################################
# Configuration for dependent projects

set(INSTALL_CMAKE_DIR lib/cmake/${project_name})

# This must happen after INCLUDE_DIR and TARGET are set.
include(CMakePackageConfigHelpers)
configure_package_config_file(lib-config.cmake.in
  "${PROJECT_BINARY_DIR}/${project_name}-config.cmake"
  INSTALL_DESTINATION "${INSTALL_CMAKE_DIR}"
  PATH_VARS INCLUDE_DIR)

write_basic_package_version_file(
  "${PROJECT_BINARY_DIR}/${project_name}-config-version.cmake"
  VERSION ${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}
  COMPATIBILITY SameMajorVersion)

install(FILES
  "${PROJECT_BINARY_DIR}/${project_name}-config.cmake"
  "${PROJECT_BINARY_DIR}/${project_name}-config-version.cmake"
  DESTINATION "${INSTALL_CMAKE_DIR}")

export(TARGETS ${TARGET} FILE "${PROJECT_BINARY_DIR}/${EXPORT_TARGET_SET}.cmake")
export(PACKAGE ${PROJECT_NAME})

install(EXPORT ${EXPORT_TARGET_SET} DESTINATION "${INSTALL_CMAKE_DIR}")
